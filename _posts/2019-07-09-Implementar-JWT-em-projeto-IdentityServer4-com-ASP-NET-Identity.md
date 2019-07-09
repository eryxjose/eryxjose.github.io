---
layout: post
author: Eryx
title: Implementar JWT em projeto IdentityServer4 com ASP.NET Identity
date: 09/07/2019
---

Implementação de tokens de autenticação JWT em um projeto IdentityServer4 com ASP.NET Identity.

Incluir middleware JWTBearer em ConfigureServices na classe startup.cs

	services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
		.AddJwtBearer(options =>
			{
				options.TokenValidationParameters = new TokenValidationParameters
				{
					ValidateIssuer = true,
					ValidateAudience = true,
					ValidateLifetime = true,
					ValidateIssuerSigningKey = true,
					ValidIssuer = Configuration["Jwt:Issuer"],
					ValidAudience = Configuration["Jwt:Issuer"],
					IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(Configuration["Jwt:Key"]))
				};
			});

Será necessário resolver e ou referênciar as seguintes dependências

	using Microsoft.AspNetCore.Authentication.JwtBearer;
	using Microsoft.IdentityModel.Tokens;
	using System.Text;


Incluir chave de configuração para fornecer Key e Issuer para as requisições JWT no arquivo appsettings.json.
	
	{
	  "ConnectionStrings": {
		"DefaultConnection": "Data Source=db-stigeo-dev12.labtrans.ufsc.br\\devweb2012;database=bd_dnit_identity_server;trusted_connection=no;User ID=observatorio;Password=Stigeo_1_adm_1"
	  },
	  "Jwt": {
		"Key": "veryVerySecretKey",
		"Issuer": "http://localhost:5000/"
	  },
	  "Application": {
	...

Criar TokenController para fornecer endpoint para criação de tokens JWT em TokenController.cs.

Este controller inicializa SignInManager<ApplicationUser> no construtor para ter acesso aos métodos de autenticação ASP.NET por usuário e senha.

	private IConfiguration _config;
	private readonly SignInManager<ApplicationUser> _signInManager;

	public TokenController(IConfiguration config, SignInManager<ApplicationUser> signInManager)
	{
		_config = config;
		_signInManager = signInManager;
	}

As classes LoginModel e UserModel representam as informações de login e usuário referenciadas pelo TokenController.

  public class LoginModel
  {
      public string Username { get; set; }
      public string Password { get; set; }
  }

  private class UserModel
  {
      public string Name { get; set; }
      public string Email { get; set; }
      public DateTime Birthdate { get; set; }
  }

O método CreateTokenAsyn coordena a geração do token JWT.
	
  [AllowAnonymous]
  [HttpPost]
  public async Task<IActionResult> CreateTokenAsync([FromBody]LoginModel login)
  {
      IActionResult response = Unauthorized();
      var user = await AuthenticateAsync(login);

      if (user != null)
      {
          var tokenString = BuildToken(user);
          response = Ok(new { token = tokenString });
      }

      return response;
  }

O método AuthenticateAsync valida usuário e senha informados.

	private async Task<LoginViewModel> AuthenticateAsync(LoginModel login)
	{
		LoginViewModel user = new LoginViewModel();

		var result = await _signInManager.PasswordSignInAsync(login.Username, login.Password, false, lockoutOnFailure: false);

		if (result.Succeeded)
		{
			user.Email = login.Username;
			user.Password = login.Password;
			return user;
		}

		return null;
	}

A autenticação do código acima utiliza o método PasswordSignInAsync da instância de SigninManager<ApplicationUser> mas você pode autenticar o usuário e senha por diferentes métodos de autenticação contanto que o retorno seja uma instância de LoginViewModel. Por exemplo:

	private UserModel Authenticate(LoginModel login)
	{
		UserModel user = null;

		if (login.Username == "eryx" && login.Password == "secret")
		{
			user = new UserModel { Name = "Eryx", Email = "eryx.guimaraes@gmail.com" };
		}
		return user;
	}

O método BuildToken gera o token JWT com o método WriteToken utilizando o token criado com a instância do método JwtSecurityToken.

	private string BuildToken(LoginViewModel user)
	{
		var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
		var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

		var token = new JwtSecurityToken(
			_config["Jwt:Issuer"],
			_config["Jwt:Issuer"],
			expires: DateTime.Now.AddMinutes(30),
			signingCredentials: creds);

		return new JwtSecurityTokenHandler().WriteToken(token);
	}

## Teste do endpoint para alterar senha utilizando Postman

Você pode obter o token JWT enviando uma requisição POST para o endpoint http://localhost:52553/api/token enviando o JSON na aba Body do Postman com os campo username e password conforme exemplo abaixo.

	{
		"username": "eryx.guimaraes@gmail.com",
		"password": "12qw!@QW"
	}

Caso o usuário e senha informados sejam válidos, será retornado o token json ilustrado a seguir.

	{
		"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjI2NzUwMzUsImlzcyI6Imh0dHA6Ly9sb2NhbGhvc3Q6NTI1NTMvIiwiYXVkIjoiaHR0cDovL2xvY2FsaG9zdDo1MjU1My8ifQ.lvbcV1N0_b0qgfyCLT9sgBCvcIjFMxSAUZcW3CpSjpY"
	}

Em seguida, utilize o token obtido para fazer uma requisição ao endpoint de alteração da senha (Account/ChangePass). Crie uma requisição POST para o url http://localhost:52553/Account/ChangePass/?returnUrl=/home/index e envie o token no Header através do parâmetro Authorization com o valor Bearer seguido de espaço e o token obtido no passo anterior. O Body da requisição deverá conter os campos OldPassword, NewPassword e ConfirmPassword com respectivos valores.




## Referências

Documentação oficial
https://identityserver4.readthedocs.io/en/latest/

