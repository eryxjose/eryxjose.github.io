---
layout: post
author: Eryx
title: Criando Web API com Template WebApp no .NET Core 3.1 usando VSCode
date: 26/01/2021
---

Vamos criar uma aplicação Web API utilizando o template Web App para tirar proveito das configurações de autenticação pré-definidas e construir um exemplo de loja de livros onde uma aplicação Blazor interage com uma Web API.

A utilização do template webapp agiliza a criação do projeto webapi no sentido de disponibilizar configurações pré-definidas e também evitar o tempo gasto para remover algumas configurações não utilizadas, especificamente:

* Bibliotecas necessárias para uso de Identity
* Bibliotecas necessárias para EntityFrameworkCore.
* Configurações de Middleware para Entity Framework e Identity
* Arquivos de dados necessários (ApplicationDbContext e Migration)

Esta Web API será então utilizada como uma camada de serviços para aplicação Blazor.

## Web API 

Crie uma pasta BookStore e dentro dela a pasta BookStore.api. Abra o terminal na pasta criada e entre com o comando a seguir.

    $ dotnet new webapp --auth Individual

Abra a pasta no VSCode.

    $ code .

Inicialmente, abra o arquivo Startup.cs, localize a linha "services.AddRazorPages();" e substitua por "services.AddControllers();".

Remova a linha "app.UseStaticFiles();" e em seguida modifique o mapeamento de endpoint "endpoints.MapRazorPages()" para "endpoints.MapControllers()". E remova também as pastas wwwroot, Areas, e Pages.

Adicione uma pasta "Controllers" na pasta raiz do projeto e dentro dela o arquivo ApiController.cs com o código ilustrado abaixo.

    using System.Collections.Generic;
    using Microsoft.AspNetCore.Mvc;

    namespace webapi.Controllers
    {
        [Route("api/[Controller]")]
        [ApiController]
        public class ApiController : ControllerBase
        {
            [HttpGet]
            public IEnumerable<string> Get() 
            {
                return new string[] { "ok" };
            }
        }
    }

Em seguida, vamos configurar o Swagger para exibir a documentação da API como página inicial do projeto ao ser executado.

Veja mais sobre o uso do Swashbuckle em [Get started with Swashbuckle and ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/tutorials/getting-started-with-swashbuckle?view=aspnetcore-5.0&tabs=netcore-cli)

Instale os pacotes NuGet necessários.

    $ dotnet add package Swashbuckle.AspNetCore.Swagger --version 5.6.3
    $ dotnet add package Swashbuckle.AspNetCore.SwaggerGen --version 5.6.3
    $ dotnet add package Swashbuckle.AspNetCore.SwaggerUi --version 5.6.3

Veja mais sobre os comandos para instalar e gerenciar pacotes NuGet em [Install and manage packages using the dotnet CLI](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-dotnet-cli)

Modifique o trecho abaixo no arquivo webapi.csproj para habilitar a geração de documentação XML para comentários nos métodos dos controllers e fazer com que warnings do tipo 1591 relacionados a métodos sem comentários não sejam mostrados no controller.

    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>aspnet-webapi-2E145C6D-3154-412D-AAF5-2A2000FA1CDC</UserSecretsId>
        <GenerateDocumentationFile>true</GenerateDocumentationFile>
        <NoWarn>$(NoWarn);1591</NoWarn>
    </PropertyGroup>

Inclua o trecho a seguir antes da linha "services.AddControllers();" do método ConfigureServices em Startup.cs. Este código, define alguns valores a serem exibidos na documentação gerada pelo Swagger utilizando Reflection para criar um nome de arquivo concatenado com o caminho da aplicação. O valor de xmlPath então é passado como parâmetro para método "c.IncludeXmlComments(xmlPath)".

    services.AddSwaggerGen(c => { 
        c.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo { 
            Title = "My Book Store API", 
            Version = "v1",
            Description = "This is a API for Book Store."
        });
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });

Inclua o trecho de código abaixo antes da linha "app.UseHttpsRedirection()" no método Configure. A propriedade RoutePrefix com valor vazio faz com que o documento gerado pelo Swagger seja exibido por padrão (página inicial) ao executar a aplicação.

    app.UseSwagger();
    app.UseSwaggerUI(c => {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "Book Store API");
        c.RoutePrefix = string.Empty;
    });

Execute a aplicação para visualizar no navegador a página Swagger gerada.

...

## NLog

Vamos em seguida, configurar o componente NLog para registrar eventos do sistema.

Primeiro, instale o pacote NuGet NLog.Extensions.Logging com o comando ilustrado abaixo.

    $ dotnet add package NLog.Extensions.Logging --version 1.7.0

Em seguida crie o arquivo de configuração NLog.config na pasta raiz do projeto e copie o código abaixo.

    <?xml version="1.0" encoding="utf-8" ?>
    <nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        autoReload="true"
        internalLogLevel="Trace"
        internalLogFile="c:/logs/internallog.txt">

    <targets>
        <target name="logfile" xsi:type="File"
                fileName="./logs/${shortdate}_logfile.txt"
                layout="${longdate} ${level:uppercase=true} ${message}"/>
    </targets>

    <rules>
        <logger name="*" minlevel="Debug" writeTo="logfile" />
    </rules>
    </nlog>

Mais sobre configurações para NLog em:

* [Internal Logging](https://github.com/NLog/NLog/wiki/Internal-Logging)
* [Configuration File](https://github.com/NLog/NLog/wiki/Configuration-file)
* [Getting started with ASP.NET Core 5](https://github.com/NLog/NLog/wiki/Getting-started-with-ASP.NET-Core-5)

Adicione duas novas pastas: Contracts e Services. Crie o arquivo ILoggerService.cs na pasta Contracts e LoggerServices.cs na pasta Services.

Segue o código para interface ILoggerService.cs.

    namespace webapi.Contracts 
    {
        public interface ILoggerService 
        {
            void LogInfo(string message);
            void LogWarn(string message);
            void LogDebug(string message);
            void LogError(string message);
        }
    }

E o código que realiza (ou implementa) o contrato acima em LoggerService.cs.

    using NLog;
    using webapi.Contracts;

    namespace webapi.Services
    {
        public class LoggerService : ILoggerService
        {
            private static ILogger logger = LogManager.GetCurrentClassLogger();
            public void LogInfo(string message) 
            {
                logger.Info(message);
            }
            public void LogWarn(string message) 
            {
                logger.Warn(message);
            }
            public void LogDebug(string message) 
            {
                logger.Debug(message);
            }
            public void LogError(string message) 
            {
                logger.Error(message);
            }
        }
    }

Também será necessário, registrar um mapeamento singleton entre a interface ILoggerService e a classe LoggerService no método ConfigureServices da classe Startup. Adicione a linha abaixo antes da linha "services.AddControllers()". 

    services.AddSingleton<ILoggerService, LoggerService>();

O mapeamento singleton é utilizado para garantir que haja apenas uma instância da relação entre ILoggerService e LoggerService no ciclo de vida da aplicação. Esta é uma técnica denominada Dependency Injection (DI) disponível ...

Mais sobre Dependency Injection em [Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html) e [Dependency injection in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0).

Inclua as referências necessárias.

    using webapi.Contracts;
    using webapi.Services;

Vamos testar a geração de logs em nosso controller. Adicione as linhas a seguir, no início da classe ApiController.

    ...
    public class ApiController : ControllerBase
    {
        private readonly ILoggerService _logger;

        public ApiController(ILoggerService logger)
        {
            _logger = logger;
        }
    ...

E modifique o método Get() para gerar um log do tipo Info.

    ...
    public IEnumerable<string> Get()
    {
        _logger.LogInfo("Request para Get() em ApiController.");
        return new string[] { "ok" };
    }
    ...

Execute a aplicação e faça um teste com o método Get() para visualizar o log gerado.

## Configurando CORS 

Insira o código a seguir no método ConfigureServices da classe Startup.cs. No snippet abaixo, AddPolicy cria a policy com o nome "CorsPolicy" com permissões para todas as origens, métodos e cabeçalhos. Esta é a configuração mais permissiva onde os clientes poderão executar quaisquer operações disponibilizadas pela api.

    services.AddCors(o => {
                o.AddPolicy("CorsPolicy", 
                    builder => builder.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());
            });

Veja mais sobre configurações CORS em [Enable Cross-Origin Requests (CORS) in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-5.0).

Em seguida, entre com o nome da policy CORS criada acima (CorsPolicy) como parâmetro do método Configure. Antes da linha "app.UseRouting" inclua:

    app.UseCors("CorsPolicy");

## Modelo de Dados e Code First com Entity Framework

O template webapp utilizado para criar a aplicação, também configurou uma base de dados SQLite (arquivo app.db) com o recurso de migrations habilitado. 

Migrations é parte da abordagem denominada Code First para gerenciamento do modelo de dados (ou domínio) da aplicação. 

Em resumo, esta abordagem atualiza a base de dados a partir das classes (ou entidades) da aplicação.

Por exemplo, crie as classes Author.cs e Book.cs na pasta Data e copie o código ilustrado abaixo para os respectivos arquivos.

* Author.cs

    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations.Schema;
    namespace webapi.Data 
    {
        [Table("Authors")]
        public partial class Author
        {
            public int Id { get; set; }
            public string Firstname { get; set; }
            public string Lastname { get; set; }
            public string Bio { get; set; }

            public virtual IList<Book> Books { get; set; }
        }
    }

* Book.cs

    using System.ComponentModel.DataAnnotations.Schema;
    namespace webapi.Data 
    {
        [Table("Books")]
        public partial class Book
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public int? Year { get; set; }
            public string Isbn { get; set; }
            public string Summary { get; set; }
            public string Image { get; set; }
            public double? Price { get; set; }

            public int? AuthorId { get; set; }
            public virtual Author Author { get; set;}

        }
    }

Os atributos (ex.: [Table("Books)]) permitem sobrescrever padrões em relação as migrations que atualizam a base de dados.

Modifique a classe ApplicationDbContext.cs conforme exemplo abaixo contendo as propriedades Authors e Books. 

    ...
    public class ApplicationDbContext : IdentityDbContext
    {
        public DbSet<Author> Authors { get; set; }
        public DbSet<Book> Books { get; set; }
        
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ...

O Entity Framework utiliza a coleção de propriedades DbSet para migrations e para as operações de dados.

Para executar comandos do Entity Framework Migrations no terminal é necessário instalar o "dotnet ef". Utilize o comando abaixo.

    $ dotnet tool install --global dotnet-ef

Confirme que a instalação foi bem sucedida.

    $ dotnet ef

Adicione uma nova migration para atualizarmos a base com as classes Authors e Books.

    $ dotnet ef migrations add AddedBooksAndAuthors

O comando acima gera os scripts que farão a atualização na base. Em seguida, execute o comando "dotnet ef database update" para atualizar a base de dados.

    $ dotnet ef database update

Veja mais sobre a utilização de migrations para atualizar a base em [Migrations Overview](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli).


## SQLite

SQLite é um gerenciador de bancos de dados relacional embarcado principalmente em aplicações para dispositivos móveis. 

Sua arquitetura inclui uma aplicação, um conector e um arquivo de banco de dados, não requer nenhuma pré-configuração e suporta transações (ACID).

As tabelas utilizam tipos dinâmicos, e portanto, a mesma coluna pode armazenar diferentes tipos de dados.

A mesma conexão pode acessar múltiplos arquivos de bancos de dados. E o SQLite também pode gerenciar bases de dados em memória.

Caso ainda não tenha instalado o SQLite, abra o terminal na pasta raiz da aplicação e utilize o comando apt install para instalar a biblioteca sqlite3 utilizada para gerenciar bancos de dados via terminal.

    $ sudo apt install sqlite3

Em seguida, execute o comando abaixo para ter acesso a base de dados app.db através do prompt sqlite3.

    $ sqlite3 app.db

Verifique as tabelas existentes com o comando a seguir .tables.

    sqlite> .tables

Veja as tabelas Authors e Books adicionadas anteriormente e as tabelas geradas automaticamente pelo template que utilizamos para criar a aplicaçãoi e relacionadas ao gerencimaneto de identidade.

    AspNetRoleClaims       AspNetUserRoles        Books                
    AspNetRoles            AspNetUserTokens       __EFMigrationsHistory
    AspNetUserClaims       AspNetUsers          
    AspNetUserLogins       Authors 

## Data Transfer Objects (DTO)

DTOs são estruturas de dados criadas a partir das entidades do domínio. São compostos de uma ou mais propriedades da entidade que sejam relevantes para operação em andamento.

Toda operação que utilize as entidades do modelo de domínio da aplicação, será intermediada por uma ou mais DTOs. Imagine que pode haver um DTO BookDTO.cs que contenha as mesmas propriedades da classe Books e seja utilizado no carregamento de dados de um livro. Em outro exemplo, a operação de inclusão utilizará um DTO que não contenha a propriedade Id uma vez que se trata de uma chave primária na tabela. Ou ainda, na operação de atualização, o DTO não utilizará a propriedade Image que será atualizada em outra operação. 

O mapeamento entre as classes DTO e as entidades de dados (domínio) da aplicação será feito utilizando o pacote NuGet AutoMapper e AutoMapper.Extensions.Microsoft.DependencyInjection. Execute os comandos abaixo para instalar os referidos pacotes.

    $ dotnet add package AutoMapper --version 10.1.1
    $ dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection --version 8.1.0

Abra o arquivo Startup.cs e inclua o código abaixo antes do método services.AddSwaggerGen em ConfigureServices.

     services.AddAutoMapper(typeof(Maps));

Em seguida, crie uma pasta Mappings dentro da pasta Data. E nesta pasta, crie uma classe Maps.cs com o código a seguir. Aqui é feito o relacionamento entre interface e classes de dados utilizando CreateMap do AutoMapper.

    using AutoMapper;
    using webapi.Data;
    using webapi.DTOs;

    namespace webapi.Mappings
    {
        public class Maps : Profile
        {
            public Maps()
            {
                CreateMap<Author, AuthorDTO>().ReverseMap();
                CreateMap<Book, BookDTO>().ReverseMap();
            }
        }
    }

## Repository

Repository é um padrão para trabalhar com coleções de dados em memória. Um conjunto de funções para instanciar e atualizar entidades do domínio da aplicação a partir dos dados obtidos de uma fonte de dados.

Crie a interface IRepositoryBase.cs na pasta Contracts e copie código ilustrado abaixo.

    using System.Collections.Generic;
    using System.Threading.Tasks;
    namespace webapi.Contracts
    {
        public interface IRepositoryBase<T> where T : class
        {
            Task<IList<T>> FindAll();
        }
    }

O tipo Task<T> especifica que esta função é assíncrona, e IList<T> especifica que o retorno da função será uma coleção do tipo T. "where T : class" determina que somente classes possam realizar o contrato IRepositoryBase.

Crie o arquivo AuthorsRepository.cs na pasta Services com o código abaixo. Observe a injeção de dependência no construtor da classe que recebe ApplicationDbContext configurado na aplicação (Startup.cs). Este repository executa as ações relacionadas a interação com a base de dados e a manutenção do estado da entidade Author na aplicação. 

    using System.Collections.Generic;
    using System.Threading.Tasks;
    using webapi.Contracts;
    using webapi.Data;
    using Microsoft.EntityFrameworkCore;

    namespace webapi.Services
    {
        public class AuthorRepository : IAuthorRepository
        {
            private readonly ApplicationDbContext _db;
            public AuthorRepository(ApplicationDbContext db)
            {
                _db = db;
            }

            public async Task<bool> Create(Author entity)
            {
                var result = await _db.Authors.AddAsync(entity);
                return await Save();
            }

            public async Task<bool> Delete(Author entity)
            {
                _db.Authors.Remove(entity);
                return await Save();
            }

            public async Task<IList<Author>> FindAll()
            {
                var authors = await _db.Authors.ToListAsync();
                return authors;
            }

            public async Task<Author> FindById(int id)
            {
                var author = await _db.Authors.FindAsync(id);
                return author;
            }

            public async Task<bool> Save()
            {
                var changes = await _db.SaveChangesAsync();
                return changes > 0;
            }

            public async Task<bool> Update(Author entity)
            {
                _db.Authors.Update(entity);
                return await Save();
            }
        }
    }

## Authors Controller

Crie o arquivo AuthorsController.cs na pasta Controllers e cole o código abaixo. Na arquitetura atual, o Controller utiliza Repositories para instanciar as entidades do domínio com dados da base e o AutoMapper para gerar objetos DTO que serão utilizados no payload da resposta Http.

    using System.Collections.Generic;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.AspNetCore.Http;
    using webapi.Contracts;
    using AutoMapper;
    using webapi.DTOs;
    using System.Threading.Tasks;

    namespace webapi.Controllers
    {
        [Route("api/[controller]")]
        [ApiController]
        [ProducesResponseType(StatusCodes.Status200OK)]
        public class AuthorsController : ControllerBase
        {
            private readonly IAuthorRepository _authorRepository;
            private readonly ILoggerService _logger;
            private readonly IMapper _mapper;

            public AuthorsController(IAuthorRepository authorRepository, ILoggerService logger, IMapper mapper)
            {
                _authorRepository = authorRepository;
                _logger = logger;
                _mapper = mapper;
            }

            [HttpGet]
            public async Task<IActionResult> GetAuthors() 
            {
                try 
                {
                    _logger.LogInfo("Getting authors.");
                    var authors = await _authorRepository.FindAll();
                    var response = _mapper.Map<IList<AuthorDTO>>(authors);
                    return Ok(response);
                }
                catch (Exception ex) 
                {
                    return InternalError($"{ex.Message} - {ex.InnerException}")
                }
            }

            private ObjectResult InternalError(string message) 
            {
                _logger.LogError(message);
                return StatusCode(500, "Something wrong happened. Contact support.");
            }
        }
    }

O método privado InternalError, é utilizado como um helper para geração de log e objeto de erro a ser retornado sempre que ocorrer alguma exceção. 

Inclua o método GetAuthor obter Author por Id. Observe o critério de validação adicionado ao verbo HttpGet no atributo da action. Verifique que o método GetAuthor recebe o parâmetro inteiro id e passa o identificador para o método FindById de AuthorRepository.

    [HttpGet("{id}")]
    public async Task<IActionResult> GetAuthor(int id) 
    {
        try 
        {
            _logger.LogInfo($"Getting author by id. Id: {id}");
            var author = await _authorRepository.FindById(id);
            if (author == null) 
            {
                _logger.LogWarn($"Author com id {id} não foi encontrado.");
                return NotFound();
            }
            var response = _mapper.Map<AuthorDTO>(author);
            return Ok(response);
        }
        catch (Exception ex) 
        {
            return InternalError($"{ex.Message} - {ex.InnerException}")
        }
    }

 Vamos implementar a ação para criar um novo Author. Primeiro é necessário implementar um DTO para referida ação. Crie uma classe AuthorCreateDTO no arquivo AuthorDTO.cs com o código abaixo.

    public partial class AuthorCreateDTO
    {
        [Required]
        public string Firstname { get; set; }

        [Required]
        public string Lastname { get; set; }

        public string Bio { get; set; }

    }

Será necessário referenciar o namespace abaixo para decorar as propriedades com atributos.

    using System.ComponentModel.DataAnnotations.Schema;

Em seguida, adicione o respectivo mapeamento no arquivos Maps.cs na pasta Mappers.

    ...
    public Maps()
    {
        CreateMap<Author, AuthorDTO>().ReverseMap();
        CreateMap<Author, AuthorCreateDTO>().ReverseMap();
        CreateMap<Book, BookDTO>().ReverseMap();
    }
    ...

Adicione o método Create ilustrado abaixo em AuthorController.cs.

    [HttpPost]
    [ProducesResponseType(StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    [ProducesResponseType(StatusCodes.Status500InternalServerError)]
    public async Task<IActionResult> Create([FromBody] AuthorCreateDTO authorCreateDTO) 
    {
        try
        {
            // ModelState restaura as informações preenchidas para o form
            if (authorCreateDTO == null) 
            {
                _logger.LogWarn($"Empty request.");
                return BadRequest(ModelState);
            }
            
            if (!ModelState.IsValid)
            {
                _logger.LogWarn($"Verifique os dados e tente novamente.");
                return BadRequest(ModelState);
            }
            
            var author = _mapper.Map<Author>(authorCreateDTO);
            
            var isSuccess = await _authorRepository.Create(author);
            if (!isSuccess)
            {
                return InternalError($"Failed to create Author.");
            }

            _logger.LogInfo("Author created.");
            return Created("Create", new { authorCreateDTO });
        }
        catch (Exception ex) 
        {
            return InternalError($"{ex.Message} - {ex.InnerException}");
        }
    }

Repare que o método recebe um objeto AuthorCreateDTO, faz algumas validações, converte authorCreateDTO para Author utilizando Map do AutoMapper e então passa author para o método Create do respectivo repository. A ação retorna um objeto Http Created com o nome do método (Create) e o objeto criado (authorCreateDTO).

Em seguida crie o método Update 

