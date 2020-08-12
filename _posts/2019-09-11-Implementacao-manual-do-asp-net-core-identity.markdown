---
layout: post
author: Eryx
title: Implementação manual do ASP.NET Core Identity 
date: 11/09/2019
---

Este post aborda o registro do ASP.NET Core Identity em uma aplicação usando injeção de dependência, estrutura de armazenamento e o registro e autenticação de usuários.

# Implementando ASP.NET Core Identity

Crie uma aplicação com base no template ASP.NET Core Web Application selecionando Web Application Model-View-Controller (MVC) com a versão ASP.NET Core 2.0 (Framework .NET Core) e não inclua autenticação de usuário.

* Inclui por padrão o pacote NuGet Microsoft.AspNetCore.All (2.0) e respectivas dependências.

Remova todas as referências (<PackageReference>) do arquivo .csproj com exceção dos pacotes Microsoft.AspNetCore, Microsoft.AspNetCore.Mvc e Microsoft.AspNetCore.StaticFiles.

Instale o pacote Microsoft.Extnsions.Identity.Core através do Package Manage Console.

	> Install-Package Microsoft.Extensions.Identity.Core

Registre o ASP.NET Identity no método ConfigureServices da classe Startup.cs utilizando AddIdentityCore<classe-de-usuario>, em nosso exemplo AppUser.

	...
	
	services.AddIdentityCore<AppUser>(options => {});
	
	...

Crie a classe AppUser para representar os usuários da aplicação com as propriedades Id, UserName, NormalizedUserName e PasswordHash.

	public class AppUser 
	{
		public string Id { get; set; }
		public string UserName { get; set; }
		public string NormalizedUserName { get; set; }
		public string PasswordHash { get; set; }
	}

Crie uma classe para representar a UserStore da aplicação, por exemplo AppUserStore com uma implementação da interface IUserStore<T>. É recomendado retornar Task<T> nas chamadas dos métodos de persistência de dados que utilizam recursos de I/O com tempos de resposta variados.

	public class AppUserStore : IUserStore<AppUserStore>
	{
		public void Dispose() 
		{
		}
		
		public Task<string> GetUserIdAsync(AppUser user, CancellationToken cancelationToken)
		{
			return Task.FromResult(user.id);
		}
		
		public Task<string> GetUserNameAsync(AppUser user, CancellationToken cancelationToken)
		{
			return Task.FromResult(user.UserName);
		}
		
		public Task SetUserNameAsync(AppUser user, string userName, CancellationToken cancelationToken)
		{
			user.UserName = userName;
			return Task.CompletedTask;
		}
		
		public Task<string> GetNormalizedUserNameAsync(AppUser user, string normalizedUserName, CancellationToken cancelationToken)
		{
			return Task.FromResult(user.NormalizedUserName);
		}
		
		public Task SetNormalizedUserNameAsync(AppUser user, string normalizedUserName, CancellationToken cancelationToken)
		{
			user.NormalizedUserName = normalizedUserName;
			return Task.CompletedTask;
		}
		
		public Task<IdentityResult> CreateAsync(AppUser user, CancellationToken cancelationToken)
		{
			// Código implementado adiante
		}
	}

As operações de Get acima apenas retornam a propriedade do próprio objeto enviado como parâmetro para simplificar, mas em uma aplicação real, poderia haver chamadas para obter dados de múltiplas tabelas.

As operações de Set também atribuem os valores enviados como parâmetro apenas para os objetos em memória. 

As operações Create persistem os dados no banco de dados LocalDB descrito a seguir.

Utilize o SQL Management Studio para criar uma nova base de dados LocalDB com o nome AppDemo.

Crie a tabela AppUsers com os mesmos campos definidos na classe.
	
	USE AppDemo
	GO 
	
	CREATE TABLE AppUsers(
		Id nvarchar(450) NOT NULL, 
		UserName nvarchar(256) NULL, 
		NormalizedUserName nvarchar(256) NULL, 
		PasswordHash nvarchar(max) NULL,
	CONSTRAINT PK_AppUsers PRIMARY KEY CLUSTERED(
		Id ASC
	))
	GO

Instale o pacote NuGet Dapper para facilitar a obtenção de dados.

	> Install-Package Dapper

Crie o método (helper) GetOpenConnection() para retornar conexões para os métodos de acesso e manipulação de dados.

	public static DbConnection GetOpenConnection()
	{
		var conn = new SqlConnection("Data Source=(LocalDB)\\MSSQLLocalDB;database=AppDemo;trusted_connection=yes");
		conn.Open();
		return conn;
	}

Os métodos CreateAsync e UpdateAsync utilizam o método ExecuteAsync() para incluir ou atualizar dados na base.

	public async Task<IdentityResult> CreateAsync(AppUser user, CancellationToken cancelationToken)
	{
		using (var conn = GetOpenConnection())
		{
			await conn.ExecuteAsync(
				"INSERT INTO AppUsers(Id, UserName, NormalizedUserName, PasswordHash) Values (@id, @userName, @normalizedUserName, @passwordHash)", 
				new 
				{
					id = user.Id,
					userName = user.UserName,
					normalizedUserName = user.NormalizedUserName,
					passwordHash = user.PasswordHash
				}
			);
		}
		
		return IdentityResult.Success;
	}

	public async Task<IdentityResult> UpdateAsync(AppUser user, CancellationToken cancelationToken)
	{
		using (var conn = GetOpenConnection())
		{
			await conn.ExecuteAsync(
				"UPDATE AppUsers SET Id = @id, UserName = @userName, NormalizedUserName = @normalizedName, PasswordHash) WHERE Id = @id", 
				new 
				{
					id = user.Id,
					userName = user.UserName,
					normalizedUserName = user.NormalizedUserName,
					passwordHash = user.PasswordHash
				}
			);
		}
		
		return IdentityResult.Success;
	}

Os métodos FindByIdAsync e FindByNameAsync são semelhantes, retornando registros obtidos pelo método QueryFirstOrDefaultAsync<T>.

	public async Task<AppUser> FindByIdAsync(string userId, CancellationToken cancelationToken)
	{
		using (var conn = GetOpenConnection()) 
		{
			return await QueryFirstOrDefaultAsync<AppUser>(
				"SELECT * FROM AppUsers where Id = @id", 
				new { id = userId });
		}
	}
	
	public async Task<AppUser> FindByNameAsync(string normalizedUserName, CancellationToken cancelationToken)
	{
		using (var conn = GetOpenConnection()) 
		{
			return await QueryFirstOrDefaultAsync<AppUser>(
				"SELECT * FROM AppUsers where NormalizedUserName = @normalizedUserName", 
				new { name = normalizedUserName });
		}
	}

Inclua a implementação da interface IUserPasswordStore<AppUser>()

	public Task SetPasswordHashAsync(AppUser user, string passwordHash, CancellationToken cancelationToken)
	{
		user.PasswordHash = passwordHash;
		return Task.CompletedTask;
	}
	
	public Task<string> GetPasswordHashAsync(AppUser user, CancellationToken cancelationToken)
	{
		return Task.FromResult(user.PasswordHash);
	}
	
	public Task<string> HashPasswordAsync(AppUser user, CancellationToken cancelationToken)
	{
		return Task.FromResult(user.PasswordHash != null);
	}

Registre a implementação de IUserStore no método ConfigureServices da classe Startup.

	...
	
	services.AddIdentityCore<AppUser>(options => { });
	services.AddScoped<IUserStore<AppUser>, AppUserStore>();

	...

Neste ponto o ASP.NET Identity está registrado na aplicação com respectivas classes User e UserStore. 

# Registro de novos usuários

Implemente os métodos Register para requisições HttpGet e HttpPost em HomeController.

	[HttpGet]
	public IActionResult Register() 
	{
		return View();
	}
	
	[HttpPost]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> Register(RegisterModel model) 
	{
		...
	}

Crie a classe RegisterViewModel na pasta Models.

	public class RegisterModel
	{
		public string UserName { get; set; }
		
		[DataType(DataType.Password)]
		public string Password { get; set; }
		
		[Compare("Password")]
		[DataType(DataType.Password)]
		public string ConfirmPassword { get; set; }
		
	}

Adicione uma View Register.cshtml que faça referência para RegisterViewModel e uma view Success.cshtml contendo apenas uma mensagem de sucesso.

Implemente um método construtor para HomeController que recebe UserManager do tipo AppUser e atribua para uma propriedade local.

	private readonly UserManager<AppUser> userManager;
	
	public HomeController(UserManager<AppUser> userManager)
	{
		this.userManager = userManager;
	}

Implemente o método Register que utiliza userManager para localizar ou criar um usuário.

	...
	
	public async Task<IActionResult> Register(RegisterModel model) 
	{
		if (ModelState.IsValid)
		{
			var user = await userManager.FindByNameAsync(model.UserName);
			
			if (user == null)
			{
				user = new AppUser 
				{
					Id = Guid.NewGuid().ToString(),
					UserName = model.UserName;
				};
				
				var result = await userManager.CreateAsync(user, model.Password);
			}
		}
		
		return View(model);
	}
	
	...

Observe que o método userManager.CreateAsync recebe o campo UserName e é responsável por gerar o NormalizedUserName. De maneira semelhante, model.Password é passado no formato Plain Text e o método CreateAsync aplica o algoritmo de hash automaticamente. O método CreateAsync retorna um objeto do tipo Identity Result.

A página de sucesso será exibida da mesma forma para sucesso e falha por questões de segurança.

Implemente a funcionalidade de Login de maneira semelhante ao que foi feito para Register.

	[HttpGet]
	public IActionResult Login() 
	{
		return View();
	}
	
	[HttpPost]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> Login(LoginModel model)
	{
		...
	}

Crie a classe LoginModel na pasta Models.

	public class LoginModel 
	{
		public string UserName { get; set; }
		
		[DataType(DataType.Password)]
		publi string Password { get; set; }
	}

Adicione uma nova View para o Login com referência para LoginModel.

Modifique a View About.cshtml para listas as claims do usuário.

	...
	
	<ul>
		@foreach (var claim in User.Claims)
		{
			<li><strong>@claim.Type</strong>: @claim.Value</li>
		}
	</ul>

	...
	
Implemente o método de autenticação padrão para asp.net core, CookieAuthentication, que depende do pacote NuGet Microsoft.AspNetCore.Authentication.Cookies instalado com o comando abaixo.

	> Install-Package Microsoft.AspNetCore.Authentication.Cookies

E no método ConfigureServices da classe Startup implemente a atenticação por cookies e opções de login. Observe que o valor "cookies" passado como parâmetro para AddAuthentication faz com que seja o método de autenticação padrão.

	...
	
	services.AddAuthentication("cookies").AddCookie("cookies", options => options.LoginPath = "/Home/Login");

	...

E no método Configure ainda na classe Startup, defina uma chamada para app.UseAuthentication() antes de app.UseMvc().

	public void Configure(IApplicationBuilder app, IHostEnvironment env)
	{
		...
		
		app.UseAuthentication(); 
				
		...
	}

Inclua o atributo [Authorize] no método About() em HomeController para restringir o acesso aos usuários logados. 

O método de login valida as informações passadas para model (LoginModel) e usa o UserManager para fazer a autenticação e o login do usuário. ClaimsPrinciple obtém ClaimsIdentity e ambos representam o usuário em uma requisição Http.

	[HttpPost]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> Login(LoginModel model)
	{
		if (ModelState.IsValid)
		{
			var user = await userManager.FindByNameAsync(model.UserName);
			
			if (user != null && await userManager.CheckPasswordAsync(user, model.Password))
			{
				var identity = new ClaimsIdentity("cookies");
				identity.AddClaim(new Claim(ClaimTypes.NameIdentifierm, user.Id));
				Identity.AddClaim(new Claim(ClaimTypes.Name, user.UserName));
				
				await HttpContext.SignInAsync("cookies", new ClaimsPrincipal(identity));
				
				return RedirectToAction("Index");
			}
			
			ModelState.AddModelError("", "Nome de usuário ou senha inválidos.");
		}
		
		return View();
	}

Exemplo de código para fazer Mock de UserManager para testes unitários.

	var store = new Mock<IUserStore<TUser>>();
	var mgr = new Mock<UserManager<TUser>>(store.Object, null, null, null, null, null, null, null, null);
	mgr.Object.UserValidators.Add(new UserValidator<TUser>());
	mgr.Object.PasswordValidators.Add(new PasswordValidator<TUser>());

Ao instanciar o Mock acima, o único parâmetro obrigatório e exemplificado no código é uma instância de UserStore.

