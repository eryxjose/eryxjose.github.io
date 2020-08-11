---
layout: post
author: Eryx
title: ASP.NET Identity response header personalizado para erro de confirmação de email requerida
date: 10/07/2019
---

A regra inicial para validação de confirmação do email inicialmente foi implementada através de configuração do middleware na classe startup.cs de maneira que as requisições para a aplicação são interceptadas e validadas antes que o endpoint seja executado. Neste cenário, a aplicação retornará um erro 400 de requisição inválida.

	...
	
	services.AddIdentity<ApplicationUser, IdentityRole>(config =>
	{
		config.SignIn.RequireConfirmedEmail = true;
		config.User.RequireUniqueEmail = true;
	})
	
	...

Posteriormente houve uma demanda para que seja mostrada uma mensagem de erro personalizada para o caso de email não confirmado e foi necessário implementar a validação no endpoint de acesso ao sistema (Account/Login). Segue o código completo para o método Login.

	[HttpPost]
	[AllowAnonymous]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
	{
		ViewData["ReturnUrl"] = returnUrl;
		if (ModelState.IsValid)
		{
			var result = await _signInManager.PasswordSignInAsync(model.Email, model.Password, model.RememberMe, lockoutOnFailure: false);

			if (result.Succeeded)
			{
				var user = await _userManager.FindByEmailAsync(model.Email);
				var emailConfirmed = await _userManager.IsEmailConfirmedAsync(user);

				if (emailConfirmed == true)
				{
					_logger.LogInformation("User logged in.");
					return RedirectToLocal(returnUrl);
				}
				else
				{
					await _signInManager.SignOutAsync();
					return new ObjectResult(new
					{
						code = StatusCodes.Status403Forbidden,
						details = "É necessário confirmar o email através do link de confirmação.",
						message = "Email não confirmado",
						messageToken = "emailNotConfirmed",
					});
				}
			}

			if (result.RequiresTwoFactor)
			{
				return RedirectToAction(nameof(LoginWith2fa), new { returnUrl, model.RememberMe });
			}

			if (result.IsLockedOut)
			{
				_logger.LogWarning("User account locked out.");
				return RedirectToAction(nameof(Lockout));
			}
			else
			{
				ModelState.AddModelError(string.Empty, "Invalid login attempt.");
				return View(model);
			}
		}

		// If we got this far, something failed, redisplay form
		return View(model);
	}


No exemplo acima, primeiro ocorre a validação do objeto LoginViewModel através do método ModelState.IsValid. Em seguida, a variável result recebe o resultado da validação de acesso e então obtemos o usuário e a verificamos a confirmação do email. Caso o email não tenha sido confirmado, é feito o signout do usuário e retornado uma instância de ObjectResult contendo o código do erro 403 (Unauthorized) e detalhes adicionais.

Para atender a solicitação de mensagem de erro personalizada para email não confirmado, foi necessário remover a validação feita no middleware para que o endpoint de login pudesse verificar a confirmação de email do usuário.


