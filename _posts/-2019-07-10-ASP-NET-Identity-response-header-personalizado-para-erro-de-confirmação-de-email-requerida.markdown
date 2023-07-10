---
layout: post
author: Eryx
title: Configuração para confirmação de email no ASP.NET Identity
date: 10/07/2019
---

A regra inicial para validação de confirmação do email inicialmente foi implementada através de configuração do middleware na classe 'startup.cs' de maneira que as requisições para a aplicação são interceptadas e validadas antes que o endpoint seja executado. Neste cenário, a aplicação retornará um erro 400 de requisição inválida.

	...
	
	services.AddIdentity<ApplicationUser, IdentityRole>(config =>
	{
		config.SignIn.RequireConfirmedEmail = true;
		config.User.RequireUniqueEmail = true;
	})
	
	...

Posteriormente houve uma demanda de mensagem de erro personalizada para o caso de email não confirmado e foi necessário implementar a validação no endpoint de acesso ao sistema (Account/Login). Segue o código completo para o método Login.

	[HttpPost]
	[AllowAnonymous]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
	{
		ViewData["ReturnUrl"] = returnUrl;
		if (ModelState.IsValid)
		{
			var result = await _signInManager.PasswordSignInAsync(
				model.Email, model.Password, model.RememberMe, lockoutOnFailure: false);

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
				return RedirectToAction(nameof(LoginWith2fa), 
					new { returnUrl, model.RememberMe });
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


No exemplo acima, primeiro ocorre a validação do objeto 'LoginViewModel' através do método 'ModelState.IsValid'. Em seguida, a variável 'result' recebe o resultado da validação de acesso e então obtemos o usuário e verificamos a confirmação do email. Caso o email não tenha sido confirmado, é feito o signout do usuário e retornada uma instância de 'ObjectResult' contendo o código do erro 403 (Unauthorized).

Para atender a solicitação de mensagem de erro personalizada para email não confirmado, foi necessário remover a validação feita no middleware para que o endpoint de login pudesse verificar a confirmação de email do usuário.


...




Configuração para Confirmação de E-mail no ASP.NET Identity com Exemplos de Código

A confirmação de e-mail é uma etapa importante no processo de registro de usuários em um aplicativo ASP.NET usando o ASP.NET Identity. Ela ajuda a garantir que os usuários forneçam um endereço de e-mail válido e permite verificar sua identidade. Neste artigo, iremos abordar a configuração necessária para implementar a confirmação de e-mail no ASP.NET Identity, juntamente com exemplos de código para ilustrar cada etapa.

Passo 1: Configuração do Aplicativo

A primeira etapa é configurar o aplicativo ASP.NET Identity para habilitar a confirmação de e-mail. No arquivo `Startup.cs`, adicione o seguinte código no método `ConfigureServices`:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders()
    .AddTokenProvider<EmailTokenProvider<ApplicationUser>>(TokenOptions.DefaultEmailProvider);
```

Esse código configura o ASP.NET Identity para usar a classe `ApplicationUser` como a entidade de usuário personalizada e o `IdentityRole` como a entidade de função. Também adiciona o provedor de token de e-mail padrão e configura o provedor de token de e-mail personalizado `EmailTokenProvider`.

Passo 2: Envio do E-mail de Confirmação

O próximo passo é enviar um e-mail de confirmação para o usuário após o registro. No método `Register` do controlador `AccountController`, adicione o seguinte código:

```csharp
var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
var result = await _userManager.CreateAsync(user, model.Password);

if (result.Succeeded)
{
    var token = await _userManager.GenerateEmailConfirmationTokenAsync(user);
    var callbackUrl = Url.Action("ConfirmEmail", "Account", new { userId = user.Id, token }, protocol: HttpContext.Request.Scheme);

    await _emailService.SendConfirmationEmailAsync(model.Email, callbackUrl);

    // Resto do código...
}
```

Nesse exemplo, estamos criando um novo usuário usando o `UserManager` e, em seguida, gerando um token de confirmação de e-mail usando o método `GenerateEmailConfirmationTokenAsync`. Em seguida, construímos a URL de confirmação com base no token gerado e a passamos para o serviço de e-mail personalizado `EmailService` para enviar o e-mail de confirmação.

Passo 3: Confirmação do E-mail

A última etapa é implementar a ação `ConfirmEmail` no controlador `AccountController` para confirmar o e-mail do usuário. Adicione o seguinte código:

```csharp
[HttpGet]
public async Task<IActionResult> ConfirmEmail(string userId, string token)
{
    if (userId == null || token == null)
    {
        return RedirectToAction("Error", "Home");
    }

    var user = await _userManager.FindByIdAsync(userId);
    if (user == null)
    {
        return RedirectToAction("Error", "Home");
    }

    var result = await _userManager.ConfirmEmailAsync(user, token);
    if (result.Succeeded)
    {
        return View("EmailConfirmed");
    }
    else
    {
        return RedirectToAction("Error", "Home");
    }
}
```

Nessa ação, verificamos se os parâmetros `userId` e `token` são nulos. Em seguida, buscamos o usuário com base no ID fornecido e usamos o método `ConfirmEmailAsync` para confirmar o e-mail. Se a confirmação for bem-sucedida, exibimos a view "EmailConfirmed". Caso contrário, redirecionamos para uma página de erro.

Conclusão

A configuração e implementação da confirmação de e-mail no ASP.NET Identity podem ser realizadas seguindo esses passos simples. Através da adição das configurações adequadas no `Startup.cs`, envio de e-mails de confirmação após o registro e confirmação de e-mail no controlador `AccountController`, podemos garantir que os usuários forneçam um endereço de e-mail válido e confirmem sua identidade.

Lembre-se de adaptar o código de exemplo de acordo com a estrutura e os requisitos do seu aplicativo ASP.NET. Espero que essas informações e exemplos sejam úteis para implementar a confirmação de e-mail no seu projeto ASP.NET Identity.