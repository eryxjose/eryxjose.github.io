---
layout: post
author: Eryx
title: Criando um projeto ASP.NET Core com frontend ReactJS
date: 28/07/2021
---

Este texto descreve a construção de uma aplicação para acompanhar e participar de atividades dos usuários registrados e identificados.

O sistema será construído utilizando as tecnologias .net e react, com abordagem ágil iterativa e incremental. 

Dois projetos serão desenvolvidos em paralelo: 1) Arquitetura disponibilizada na forma de WebAPI, com abordagem 'Clean Architecture' caracterizada por uma série de princípios de construção (design e componentes), e 2) outro projeto que faz uso da referida arquitetura para construir as funcionalidades específicas do domínio da aplicação.

...

Por exemplo, para construir a tela para exibir a lista de registros de atividades, teremos inicialmente as seguintes 'user stories':

* No papel de usuário registrado, quero visualizar uma lista com todas as atividades com data de realização para o dia atual e posteriores para saber mais sobre as opções disponíveis.
* No papel de usuário registrado, quero poder filtrar por data, eventos que o usuário participará e eventos que o usuário criou.
* No papel de usuário registrado, quero que os registros sejam carregados gradativamente a medida que eu role na página para evitar o uso desnecessário de transmissão de dados.
* No papel de usuário registrado, quero acompanhar todas as atualizações referentes a atividades que me interessem para me manter atualizado.
  * No papel de usuário registrado, quero poder mudar a situação para não acompanhar um evento específico que eu esteja acompanhando caso as notificações estejam me incomodando.
* No papel de usuário registrado, quero participar de atividades criadas por outros usuários.
* No papel de usuário registrado, quero criar atividades para que outros usuários possam participar.


com recursos para incluir a inscrição para participar de eventos, criar atividades/eventos para que outros participem, conversar por chat com participantes do evento.

A aplicação será desenvolvida utilizando uma abordagem incremental e iterativa, em que novas funcionalidades e refatoramentos serão implementados quando necessário. 

# Tecnologias

* .NET 5.0 - Baixe o .net 5 em 'https://dotnet.microsoft.com/download'
* React 17 - Frontend client
* Mobx - State Management System


...

# Informações de ajuda e sobre o sistema

Versão do SDK .net instalado

    dotnet --info

Comandos mais usados

    dotnet -h

Mais detalhes sobre um comando específico

    dotnet new -h

# Configurações de solução e projetos

Crie uma pasta para o projeto.

    mkdir Personactivities
    cd Personactivities

Crie o arquivo da solução.

    dotnet new sln 

Crie o projeto webapi com o nome 'API'.

    dotnet new webapi -n API

Crie os projetos 'Application, Domain, e Persistence' do tipo 'classlib'.

    dotnet new classlib -n Application
    dotnet new classlib -n Domain
    dotnet new classlib -n Persistence

Utilize o comando 'dotnet sln' para adicionar os projetos criados a solution. O comando procura automaticamente na pasta pelo arquivo '.csproj' e vincula com a solução.

    dotnet sln add API
    dotnet sln add Application
    dotnet sln add Persistence
    dotnet sln add Domain

Você pode verificar os projetos da solução com o comando abaixo.

    dotnet sln list

# Dependência entre os projetos

Alterne para pasta do projeto API e entre com o comando 'dotnet add reference'.

    dotnet add reference ../Application

Alterne para pasta do projeto Application e relacione com os projetos 'Persistence' e 'Domain'.

    dotnet add reference ../Persistence
    dotnet add reference ../Domain

Alterne para pasta 'Persistence' e relacione com o projeto 'Domain'

    dotnet add reference ../Domain

# Configurações para o vscode

Abra o vscode na pasta do projeto e verifique se uma pasta .vscode foi criada no painel de pastas e arquivos. Caso não veja a referida pasta, acesse a paleta de comando (F1) e entre com o comando 'Generate Assets for Build and Debug'.

Abra o menu File > Preferences > Settings e digite 'exclude' na paleta de comando. Na lista 'Files: Exclude' inclua os tipos 'bin' e 'obj' para que estas pastas não sejam mostradas no vscode.

    **/bin
    **/obj

# Configurações de execução

No projeto 'API' expanda a pasta 'Properties' e abra o arquivo 'launchSettings.json'. Altere a propriedade "LaunchBrowser" para 'false' e remova o url 'https://localhost:5001' da propriedade "applicationUrl".

Execute a aplicação utilizando o comando 'dotnet run' ou 'dotnet watch run' para atualizar a aplicação automaticamente ao fazer alterações. Ainda não há nenhum endpoint respondendo pelo url raiz do site e portanto uma página '404' será mostrada ao acessar 'http://localhost:5000'.

As configurações do projeto 'webapi' no .NET 5 habilitam o Swagger por padrão. Acesse 'http://localhost:5000/swagger' para visualizar a documentação da API.

No arquivo 'appsettings.Development.json' altere a chave "Microsoft" para 'Information' para exibir mais informações no console, por exemplo, as queries sql geradas pelo Entity Framework.

    "Microsoft": "Information",

...

# Domínio e persistência de dados
Todo projeto de software é criado para atender a uma necessidade específica que determina um domínio ou escopo para a aplicação. O domínio da aplicação portanto, contém entidades relevantes para a solução do problema em questão.

Objetivos:
* Os usuários podem manter uma lista de atividades
* Os usuários podem se registrar para participar em atividades de outros usuários.
  * Os usuários podem visualizar as atividades que estão inscritos para participação.
* Os usuários podem seguir outros usuários para acompanhar as respectivas atividades.
* ...

Avaliando os objetivos da aplicação relacionados acima, podemos identificar algumas das entidades principais do sistema e iniciar o seu desenvolvimento.

Crie a classe 'Activity.cs' com as respectivas propriedades no projeto Domain conforme código a seguir.

    using System;
    namespace Domain
    {
        public class Activity
        {
            public Guid Id { get; set; }
            public string Title { get; set; }
            public DateTime Date { get; set; }
            public string Description { get; set; }
            public string Category { get; set; }
            public string City { get; set; }
            public string Venue { get; set; }
        }
    }

As entidades definidas no modelo de domínio serão utilizadas para geração da base de dados onde estas informações serão persistidas. 

O projeto prevê a utilização do EntityFramework para operações de persistência e recuperação de dados, assim como para manutenção da estrutura da base de dados. O EntityFramework cria uma camada de abstração que permite a utilização de código C# para as operações de leitura e escrita independente do banco de dados em uso.

Para habilitar o uso do EntityFramework no projeto, vamos inicialmente instalar a extensão 'NuGet Gallery' no VSCode e utilizar a paleta de comando (F1) para abrir a interface de gerenciamento de pacotes NuGet. Em seguida, pesquise e instale o pacote 'Microsoft.EntityFrameworkCore.Sqlite.Core' no projeto Persistence utilizando a mesma versão do runtime .net em uso. Utilize o comando 'dotnet info' para verificar as versões de Runtime e SDK em uso no seu sistema.

    > dotnet info

Em seguida, crie a classe 'DataContext.cs' no projeto 'Persistence' conforme exemplo abaixo.

    using Domain;
    using Microsoft.EntityFrameworkCore;
    namespace Persistence
    {
        public class DataContext : DbContext
        {
            public DataContext(DbContextOptions options) : base(options)
            {
            }

            public DbSet<Activity> Activities { get; set; }
        }
    }

A classe DataContext implementa ou herda de DbContext que permite definir as coleções de entidades que serão controladas pelo EntityFramework, e também permite sobrescrever relacionamentos entre entidades e outras configurações relacionadas ao mapeamento entre a base de dados e o modelo de domínio.

Abra o arquivo 'Startup.cs' do projeto API para configurar o uso do 'DataContext' criado. Você pode navegar utilizando a tecla de atalho 'Ctrl + P' e digitando o nome do arquivo. O código da classe 'Startup.cs' será refatorado posteriormente


    ...
    private readonly IConfiguration _config;
    public Startup(IConfiguration config)
    {
        _config = config;
    }
    ...

Você pode personalizar o vscode para utilizar o prefixo '_' em métodos construtores e desabilite o uso de 'this'. Selecione o menu File > Preferences > Settings e busque por 'private' e adicione o caracter '_' na caixa 'Prefix for generated private member declarations'. Em seguida pesquisa por 'this' e localize 'C# Extensions' na lista e desmarque a caixa 'Use this for Ctor Assignments'. Depois de mudar estas configurações, clique com o direito sobre o parâmetro do método construtor e escolha a opção adequada. O código gerado ficará como exemplo a seguir.

No método 'ConfigureService' inclua.

    services.AddDbContext<DataContext>(opt =>
    {
        opt.UseSqlite(_config.GetConnectionString("DefaultConnection"));
    });

E defina a connectionstring no arquivo 'appSettings.Development.json'

    ...
    ,
    "ConnectionStrings": {
        "DefaultConnection": "Data source=personactivities.db"
    }
    ...

# Migrations

Vamos utilizar Migrations para gerar a base de dados. Para isso, exiba o console e verifique se a ferramenta 'dotnet-ef' está instalada.

    dotnet tool list --global

Veja a documentação de referência do 'dotnet-ef' no url abaixo.

    https://www.nuget.org/packages/dotnet-ef/

Copie o comando de instalação, semelhante ao exemplo abaixo.

    dotnet tool install --global dotnet-ef --version 5.0.6

Para atualizar, no caso de já estar instalado, substitua o comando 'install' por 'update'.

Exiba a documentação de ajuda com os comandos disponíveis.

    dotnet ef -h

Em seguida entre com o comando para habilitar 'migrations' no projeto 'Persistence'.

    dotnet ef migrations add InitialCreate -p Persistence/ -s API/

Se a mensagem de erro abaixo for exibida, instale o pacote indicado e tente novamente. Você pode copiar o nome do pacote referenciado na mensagem e pesquisar na Galeria NuGet. Marque o projeto API para instalação do referido pacote.

    Your startup project 'API' doesn't reference Microsoft.EntityFrameworkCore.Design. This package is required for the Entity Framework Core Tools to work. Ensure your startup project is correct, install the package, and try again.

Verifique que a pasta 'Migrations' foi criada no projeto 'Persistence'.

A base de dados pode ser criada ou atualizada com o comando 'dotnet ef database update'.

    dotnet ef database update -s API -p Persistence

Para excluir uma base existente quando quiser a aplicação com os dados iniciais, utilize o comando 'dotnet ef database drop' conforme exemplo a seguir. O parâmetro '-s' indica o projeto de inicialização e o parâmetro '-p' para o projeto onde a pasta 'Migrations' será criada.

    dotnet ef database drop -s API -p Persistence

Instale os pacotes NuGet para SQLite no projeto de persistência.

    Microsoft.Data.Sqlite.Core by Microsoft
    Microsoft.EntityFrameworkCore.Sqlite by Microsoft
    Microsoft.Data.Sqlite by Microsoft
    Microsoft.EntityFrameworkCore.Sqlite.Core by Microsoft

De maneira alternativa, você também pode usar a classe Program para executar a Migration sempre que a aplicação for executada. Modifique a classe Program conforme exemplo a seguir.

    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();
        using var scope = host.Services.CreateScope();
        var services = scope.ServiceProvider;
        try 
        {
            var context = services.GetRequiredService<DataContext>();
            context.Database.Migrate();
        } 
        catch (Exception e) 
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(e, "Ocorreu algum problema.");
        }

        host.Run();
    }

Abra a paleta de comando do vscode (F1), localize e execute o comando 'SQLite Open Database' para exibir uma nova tab no painel lateral com o título 'SQLITE EXPLORER'.

Crie uma classe 'Seed.cs' no projeto 'Persistence' conforme exemplo abaixo para gerar informações iniciais para aplicação na base de dados.

    ...
    public class Seed
    {
        public static async Task SeedData(DataContext context)
        {
            if (context.Activities.Any()) return;
            
            var activities = new List<Activity>
            {
                new Activity
                {
                    Title = "Past Activity 1",
                    Date = DateTime.Now.AddMonths(-2),
                    Description = "Activity 2 months ago",
                    Category = "drinks",
                    City = "London",
                    Venue = "Pub",
                },
    ...

            }
            await context.Activities.AddRangeAsync(activities);
            await context.SaveChangesAsync();

Na classe 'Program.cs' inclua a linha abaixo para executar o método estático SeedData criado acima. Inclua a linha logo abaixo do comando 'context.Database.Migrate()'. 

    await Seed.SeedData(context);

Será também necessário incluir 'async' no método 'main()' da classe 'Program' e alterar o tipo de retorno para 'Task', conforme exemplo.

    public static async Task Main(string[] args)

Altere a linha do comando 'Migrate()' para utilizar a versão 'async'.

    await context.Database.MigrateAsync();

E altere também a linha que executa o servidor 'host.Run()' pela versão assíncrona.

    await host.RunAsync();

# Projeto API

No projeto 'API' crie uma classe 'BaseApiController' com o seguinte código.

    using Microsoft.AspNetCore.Mvc;
    namespace API.Controllers
    {
        [ApiController]
        [Route("api/[controller]")]
        public class BaseApiController : ControllerBase
        {
        }
    }

Crie um novo controller com o nome 'ActivitiesController' com o código abaixo.

    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Domain;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.EntityFrameworkCore;
    using Persistence;

    namespace API.Controllers
    {
        public class ActivitiesController : BaseApiController
        {
            private readonly DataContext _context;
            public ActivitiesController(DataContext context)
            {
                _context = context;
            }

            [HttpGet]
            public async Task<ActionResult<List<Activity>>> GetActivities() 
            {
                return await _context.Activities.ToListAsync();
            }

            [HttpGet("{id}")] // activities/id
            public async Task<ActionResult<Activity>> GetActivity(Guid id) 
            {
                return await _context.Activities.FindAsync(id);
            }
        }
    }

Utilize o Postman para fazer uma requisição para o url 'http://localhost:5000/api/activities'.

# Repositório

Crie um repositório github para o projeto. Utilize o comando 'git init' na pasta raiz do projeto para inicializar o novo repositório.

Execute o seguinte comando 'dotnet new gitignore' para criar o arquivo '.gitignore' utilizado para definir a lista de arquivos que não estarão inclusos no controle de versão. Você pode visualizar a relação de comandos disponíveis com o comando 'dotnet new -l'. 

    dotnet new gitignore

# Projeto frontend ReactJS 

Vamos criar um projeto ReactJS que irá consumir os dados da API para construir a interface de usuário da aplicação, utilizando TypeScript e forçando o uso do NPM como gerenciador de pacotes.

    npx create-react-app client-app --use-npm --template typescript

Depois de criada a aplicação, substitua a função 'App' do arquivo 'App.tsx' pelo código abaixo.

    function App() {
        const [activities, setActivities] = useState([]);

        useEffect(() => {
            axios.get('http://localhost:5000/api/activities').then(response => {
            console.log(activities);
            setActivities(response.data);
            })
        }, []);
        
        return (
            <div className="App">
                <header className="App-header">
                    <img src={logo} className="App-logo" alt="logo" />
                    <ul>
                        {activities.map((activity: any) => (
                            <li key={activity.id}>
                                {activity.title}
                            </li>
                        ))}
                    </ul>
                </header>
            </div>
        );
    }

No terminal, alterne para pasta 'client-app' e instale a biblioteca 'axios'.

    npm install axios

No método 'ConfigureServices' da classe 'Startup.cs' no projeto da API, adiciona uma 'Policy Cors' para permitir o acesso da aplicação cliente. 

    services.AddCors(opt => 
    {
        opt.AddPolicy("CorsPolicy", policy => 
        {
            policy.AllowAnyMethod().AllowAnyHeader().WithOrigins("http://localhost:3000");
        });
    });

Inclua a policy criada no middleware através do método 'Configure' logo depois da linha 'app.UseRouting()'.

    app.UseCors("CorsPolicy");

Com o terminal na pasta da aplicação 'client-app', instale a biblioteca 'semantic-ui-react' que será usada para facilitar a formatação visual da interface gráfica.

    npm install semantic-ui-react semantic-ui-css

Faça o import da biblioteca no arquivo 'index.tsx' antes da linha que faz o 'import' do 'index.css'.

    import 'semantic-ui-css/semantic.min.css';

Altere o método 'return' em 'App.tsx'.
        
    ...
    return (
        <div>
            <Header as='h2' icon='users' content='Personactivities' />
            <List>
                {activities.map((activity: any) => (
                    <List.Item key={activity.id}>
                        {activity.title}
                    </List.Item>
                ))}
            </List>
        </div>
    );
    ...




Instale o pacote NuGet 'MediatR.Extensions.Microsoft.DependencyInjection by Jimmy Bogard' no projeto 'Application'.

O 'MediatR' pode ser consumido diretamente nos Controllers ou pode ser definido em um Controller base herdado pelos demais. Veja abaixo o exemplo de instância em ActivitiesController.

    public class ActivitiesController : BaseApiController
    {
        private readonly IMediator _mediator;

        public ActivitiesController(IMediator mediator)
        {
            _mediator = mediator;
        }

        [HttpGet]
        public async Task<ActionResult<List<Activity>>> GetActivities() 
        {
            return await _mediator.Send(new List.Query());
        }

        [HttpGet("{id}")] // activities/id
        public async Task<ActionResult<Activity>> GetActivity(Guid id) 
        {
            return Ok();
        }
    }

Veja abaixo a implementação utilizando um controller base.

    using MediatR;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.DependencyInjection;

    namespace API.Controllers
    {
        [ApiController]
        [Route("api/[controller]")]
        public class BaseApiController : ControllerBase
        {
            private IMediator _mediator;
            protected IMediator Mediator => _mediator ??= HttpContext.RequestServices.GetService<IMediator>();
        }
    }

Em ActivitiesController mude a implementação para fazer uso do 'Mediator' disponibilizado no ControllerBase.

    public class ActivitiesController : BaseApiController
    {

        [HttpGet]
        public async Task<ActionResult<List<Activity>>> GetActivities() 
        {
            return await Mediator.Send(new List.Query());
        }

        [HttpGet("{id}")] // activities/id
        public async Task<ActionResult<Activity>> GetActivity(Guid id) 
        {
            return Ok();
        }
    }

Crie a classe 'Details.cs' na pasta 'Activities' do projeto 'Application'.

    namespace Application.Activities
    {
        public class Details
        {
            public class Query : IRequest<Activity>
            {
                public Guid Id { get; set; }
            }

            public class Handler : IRequestHandler<Query, Activity>
            {
                private readonly DataContext _context;

                public Handler(DataContext context)
                {
                    _context = context;
                }

                public async Task<Activity> Handle(Query request, CancellationToken cancellationToken)
                {
                    return await _context.Activities.FindAsync(request.Id);
                }
            }
        }
    }
    
Altere o método 'GetActivity()' em ActivitiesController para utilizar o 'Mediator Activities.Details'.

    [HttpGet("{id}")] // activities/id
    public async Task<ActionResult<Activity>> GetActivity(Guid id) 
    {
        return await Mediator.Send(new Details.Query{Id = id});
    }

Crie a classe 'Create' em 'Application/Activities'.

    using System.Threading;
    using System.Threading.Tasks;
    using Domain;
    using MediatR;
    using Persistence;

    namespace Application.Activities
    {
        public class Create
        {
            public class Command : IRequest
            {
                public Activity Activity { get; set; }
            }

            public class Handler : IRequestHandler<Command>
            {
                private readonly DataContext _context;

                public Handler(DataContext context)
                {
                    _context = context;
                }

                public async Task<Unit> Handle(Command request, CancellationToken cancellationToken)
                {
                    _context.Activities.Add(request.Activity);
                    
                    await _context.SaveChangesAsync();

                    return Unit.Value;
                }
            }
        }
    }

Adicione o método 'CreateActivity' no controller.

    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Application.Activities;
    using Domain;
    using Microsoft.AspNetCore.Mvc;

    namespace API.Controllers
    {
        public class ActivitiesController : BaseApiController
        {

            [HttpGet]
            public async Task<ActionResult<List<Activity>>> GetActivities() 
            {
                return await Mediator.Send(new List.Query());
            }

            [HttpGet("{id}")] // activities/id
            public async Task<ActionResult<Activity>> GetActivity(Guid id) 
            {
                return await Mediator.Send(new Details.Query{Id = id});
            }

            [HttpPost]
            public async Task<IActionResult> CreateActivity(Activity activity) 
            {
                return Ok(await Mediator.Send(new Create.Command {Activity = activity}));
            }
        }
    }

Crie a classe 'Edit.cs' em 'Application.Activities'.

    using System.Threading;
    using System.Threading.Tasks;
    using Domain;
    using MediatR;
    using Persistence;

    namespace Application.Activities
    {
        public class Edit
        {
            public class Command : IRequest
            {
                public Activity Activity { get; set; }
            }

            public class Handler : IRequestHandler<Command>
            {
                private readonly DataContext _context;

                public Handler(DataContext context) 
                {
                    _context = context;
                }
                public async Task<Unit> Handle(Command request, CancellationToken cancellationToken)
                {
                    var activity = await _context.Activities.FindAsync(request.Activity.Id);

                    activity.Title = request.Activity.Title ?? activity.Title;

                    await _context.SaveChangesAsync();

                    return Unit.Value;
                }
            }
        }
    }

E crie o endpoint 'EditActivity' em 'ActivitiesController'.

    [HttpPut("{id")]
    public async Task<IActionResult> EditActivity(Guid id, Activity activity)
    {
        activity.Id = id;
        return Ok(await Mediator.Send(new Edit.Command {Activity = activity}));
    }

Instale o pacote 'AutoMapper.Extensions.Microsoft.DependencyInjection' no projeto 'Application'. O AutoMapper é usado para mapear automaticamente propriedades de um objeto para outro.

Configure o 'AutoMapper' no middleware da aplicação incluindo a linha abaixo no método 'ConfigureServices' da classe 'Startup.cs'.

    services.AddAutoMapper(typeof(MappingProfiles).Assembly);

Utilize o AutoMapper nas classes do projeto 'Application.Activities' para mapear as propriedades de Activity enviadas com o 'Command request', para as propriedades do domínio obtidas com 'Context'.

    public class Handler : IRequestHandler<Command>
    {
        private readonly DataContext _context;
        private readonly IMapper _mapper;

        public Handler(DataContext context, IMapper mapper) 
        {
            _context = context;
            _mapper = mapper;
        }
        public async Task<Unit> Handle(Command request, CancellationToken cancellationToken)
        {
            var activity = await _context.Activities.FindAsync(request.Activity.Id);

            _mapper.Map(request.Activity, activity);

            await _context.SaveChangesAsync();

            return Unit.Value;
        }
    }

O código acima contém uma injeção de dependência no construtor para o AutoMapper. E no método 'Handle' utilize '_mapper.Map' para mapear as propriedades. 

O 'CancellationToken' é usado para cancelar um request. Envie o 'CancellationToken' como parâmetro para o método 'ToListAsync' do Entity Framework.

Considere uma situação hipotética utilizando a classe 'List.cs' na pasta 'Activities' do projeto 'Application'. No caso de um processamento demorado onde houvesse algum tipo de loop sendo executado em uma lista de registros, utilize o handler para tratar o token de cancelamento 'ThrowIfCancellationRequested'.

    ...
    // 
    public async Task<List<Activity>> Handle(Query request, CancellationToken cancellationToken)
    {
        try
        {
            for (var i = 0; i < 10; i++) 
            {
                cancellationToken.ThrowIfCancellationRequested();
                await Task.Delay(1000, cancellationToken);
            }
        } 
        catch (Exception ex) when (ex is TaskCancellationException)
        {
            _logger.LogInformation($"Erro: {ex}")
        }

        return await _context.Activities.ToListAsync();
    }
    
No Controller é necessário receber o 'CancellationToken' e enviá-lo para o método que executará o processamento. O método 'Mediator.Send' recebe o token de cancelamento.

    [HttpGet]
    public async Task<ActionResult<List<Activity>>> GetActivities(CancellationToken ct) 
    {
        return await Mediator.Send(new List.Query(), ct);
    }

No projeto cliente (client-app), vamos adotar uma estrutura de arquivos baseadas em features. A opção alternativa apontada na documentação oficial é utilizar uma abordagem por tipo, por exemplo, 'components, api, etc'.

Crie as pastas 'features' e 'app' dentro de 'src'. Dentro de 'app' crie a pasta 'layout' e mova o arquivo 'App.tsx' e 'index.css' para esta pasta. Altere o nome do arquivo de estilos para 'styles.css'. 

Remova os arquivos 'App.css', 'App.test.tsx' e 'logo.svg' da pasta 'src'. E no arquivo 'App.tsx' remova as referências para o logo e o arquivos de estilos 'App.css' não mais utilizados.

Utilize o Swagger disponibilizado no projeto 'API' para obter um json da estrutura de dados da entidade 'Activity', que pode ser convertida para TypeScript (pesquise por 'json to ts'), e então utilizada para criar a respectiva interface no projeto.

Crie a pasta 'app/models' e dentro dela o arquivo 'activity.ts' com o objeto gerado a partir do json alterando o nome da interface para Activity.

    export interface Activity {
        id: string;
        title: string;
        date: string;
        description: string;
        category: string;
        city: string;
        venue: string;
    }

No arquivo 'App.tsx' altere a linha que define o 'state' para a lista de 'activities' para um array tipado com a interface criada acima.

    const [activities, setActivities] = useState<Activity[]>([]);

Remova a tipagem 'any' da linha onde é feito o map das atividades '.map((activity: any)'

    {activities.map((activity) => (

Defina o tipo 'Activity[]' para o método 'axios.get'.

    useEffect(() => {
        axios.get<Activity[]>('http://localhost:5000/api/activities').then(response => {
            setActivities(response.data);
        })
    }, []);

Crie o arquivo 'NavBar.tsx' na pasta 'layout' com o seguinte código.

    import React from 'react';
    import { Button, Container, Menu } from 'semantic-ui-react';

    export default function NavBar() {
        return (
            <Menu inverted fixed='top'>
                <Container>
                    <Menu.Item header>
                        <img src="/assets/logo.png" alt="Logo" style={{ marginRight: '10px' }} />
                        PersonActivities
                    </Menu.Item>
                    <Menu.Item name='Activities' />
                    <Menu.Item>
                        <Button positivity content='Adicionar Atividade' />
                    </Menu.Item>
                </Container>
            </Menu>
        )
    }

Defina uma margem direita de '10px' na imagem de logo.

    <img src="/assets/logo.png" alt="Logo" style={{marginRight: '10px'}} />

No arquivo 'App.tsx' substitua o elemento '<Header>' pelo componente 'NavBar'.

    <NavBar />

Substitua os estilos do arquivo 'styles.css' pelo seguinte.

    body {
        background-color: #eaeaea;
    }
    .ui.inverted.top.fixed.menu {
        background-image: linear-gradient(135deg, rgb(24,42,115) 0%, rgb(33,138, 174) 69%, rgb(32,167, 172))
    }

Ainda no arquivo 'App.tsx', coloque a elemento '<List>' dentro de um elemento '<Container>' com um estilo de margem.

    <Container style={{marginTop: '7em'}}>
        <List>
          {activities.map((activity: any) => (
            <List.Item key={activity.id}>
              {activity.title}
            </List.Item>
          ))}
        </List>
    </Container>

O elemento '<Container>' cria um espaçamento do padding para seu conteúdo.

Substitua a tag '<div>' raiz, primeira do 'return', por '<Fragment>' do react para que este elemento que é de uso específico da biblioteca, seja removido na geração da estrutura html.

    ...
    return (
        <Fragment>
            <NavBar />
            <Container style={{marginTop: '7em'}}>
    ...
        </Fragment>
    ...

Você pode utilizar a versão mais resumida de '<Fragment>' simbolizada por '<>'.

Crie o componente 'ActivityDashboard' com o código abaixo.

    import React from 'react';
    import { Grid, List } from 'semantic-ui-react';
    import { Activity } from '../../../app/models/activity';

    interface Props {
        activities: Activity[];
    }

    export default function ActivityDashboard({activities}: Props) {
        return (
            <Grid>
                <Grid.Column width='10'>
                    <List>
                        {activities.map((activity: any) => (
                            <List.Item key={activity.id}>
                                {activity.title}
                            </List.Item>
                        ))}
                    </List>
                </Grid.Column>
            </Grid>
        )
    }

E substitua o 'return' de 'App.tsx' para fazer uso do novo componente.

    ...
    return (
        <Fragment>
        <NavBar />
        <Container style={{marginTop: '7em'}}>
            <ActivityDashboard activities={activities} />
        </Container>
        </Fragment>
    );
    ...

Crie o componente 'ActivityList.tsx' com o seguinte código.

    import React from 'react';
    import { Button, Item, Label, Segment } from 'semantic-ui-react';
    import { Activity } from '../../../app/models/activity';

    interface Props {
        activities: Activity[];
    }

    export default function ActivityList({activities}: Props) {
        return (
            <Segment>
                <Item.Group divided>
                    {activities.map(activity => (
                        <Item key={activity.id}>
                            <Item.Content>
                                <Item.Header as='a'>{activity.title}</Item.Header>
                                <Item.Meta>{activity.date}</Item.Meta>
                                <Item.Description>
                                    <div>{activity.description}</div>
                                    <div>{activity.city}, {activity.venue}</div>
                                </Item.Description>
                                <Item.Extra>
                                    <Button floated='right' content='View' color='blue' />
                                    <Label basic content={activity.category} />
                                </Item.Extra>
                            </Item.Content>
                        </Item>
                    ))}
                </Item.Group>
            </Segment>
        )
    }

O atributo 'divided' em '<Item.Group>' adiciona uma linha divisória entre os itens da lista.

Substitua o código que lista as atividades no componente 'ActivityDashboard' para fazer uso do novo componente.

    export default function ActivityDashboard({activities}: Props) {
        return (
            <Grid>
                <Grid.Column width='10'>
                    <ActivityList activities={activities} />
                </Grid.Column>
            </Grid>
        )
    }

Crie a pasta 'details' na pasta 'activities' junto com a pasta 'dashboard'. Crie o componente 'ActivityDetails.tsx' com o código a seguir.

    import React from 'react';
    import { Button, Card, Icon, Image } from 'semantic-ui-react';
    import { Activity } from '../../../app/models/activity';

    interface Props {
        activity: Activity
    }

    export default function ActivityDetails({activity}: Props) {
        return (
            <Card fluid>
                <Image src={`/assets/categoryImages/${activity.category}.jpg`} />
                <Card.Content>
                    <Card.Header>{activity.title}</Card.Header>
                    <Card.Meta>
                        <span>{activity.date}</span>
                    </Card.Meta>
                    <Card.Description>
                        {activity.description}
                    </Card.Description>
                </Card.Content>
                <Card.Content extra>
                    <Button.Group widths='2'>
                        <Button basic color='blue' content='Editar' />
                        <Button basic color='grey' content='Cancelar' />
                    </Button.Group>
                </Card.Content>
            </Card>
        )
    }

No exemplo acima, o atributo 'fluid' faz com que toda extensão lateral seja ocupada e o atributo 'widths' foi usado para garantir que os dois botões permaneçam lado a lado.

Modifique o componente 'ActivityDashboard.tsx' para utilizar o novo componente.

    import React from 'react';
    import { Grid, List } from 'semantic-ui-react';
    import { Activity } from '../../../app/models/activity';
    import ActivityDetails from '../details/ActivityDetails';
    import ActivityList from './ActivityList';

    interface Props {
        activities: Activity[];
    }

    export default function ActivityDashboard({activities}: Props) {
        return (
            <Grid>
                <Grid.Column width='10'>
                    <ActivityList activities={activities} />
                </Grid.Column>
                <Grid.Column width='6'>
                    {activities[0] && (
                        <ActivityDetails activity={activities[0]} />
                    )}
                </Grid.Column>
            </Grid>
        )
    }

No exemplo acima, o primeiro registro do array de atividades foi passado como registro selecionado.

Crie a pasta 'form' dentro de activities. Em seguida crie o componente 'ActivityForm.tsx' com o código abaixo.

    import React from 'react';
    import { Button, Form, Segment } from 'semantic-ui-react';

    export default function ActivityForm() {
        return (
            <Segment clearing>
                <Form>
                    <Form.Input placeholder='Título' />
                    <Form.TextArea placeholder='Descrição' />
                    <Form.Input placeholder='Categoria' />
                    <Form.Input placeholder='Data' />
                    <Form.Input placeholder='Cidade' />
                    <Form.Input placeholder='Local' />
                    <Button floated='right' positive type='submit' content='Enviar' />
                    <Button floated='right' type='button' content='Cancelar' />
                </Form>
            </Segment>
        )
    }

Substitua o código do componente 'App.tsx' pelo código abaixo.

    import { Fragment, useEffect, useState } from 'react';
    import { Container } from 'semantic-ui-react';
    import { Activity } from '../models/activity';
    import NavBar from './NavBar';
    import ActivityDashboard from '../../features/activities/dashboard/ActivityDashboard';
    import { v4 as uuid } from 'uuid';
    import agent from '../api/agent';

    function App() {

    const [activities, setActivities] = useState<Activity[]>([]);
    const [selectedActivity, setSelectedActivity] = useState<Activity | undefined>(undefined);
    const [editMode, setEditMode] = useState(false);

    useEffect(() => {
        agent.Activities.list().then(response => {
        setActivities(response);
        })
    }, []);

    function handleFormOpen(id?: string) {
        id ? handleSelectedActivity(id) : handleCanceledSelectActivity();
        setEditMode(true);
    }

    function handleFormClose() {
        setEditMode(false);
    }

    function handleSelectedActivity(id: string) {
        setSelectedActivity(activities.find(o => o.id === id))
    }

    function handleCanceledSelectActivity() {
        setSelectedActivity(undefined);
    }

    function handleCreateOrEditActivity(activity: Activity) {
        activity.id
        ? setActivities([...activities.filter(o => o.id !== activity.id), activity])
        : setActivities([...activities, {...activity, id: uuid()}]);
        setEditMode(false);
        setSelectedActivity(activity);
    }

    function handleDeleteActivity(id: string) {
        setActivities([...activities.filter(x => x.id !== id)]);
    }

    return (
        <Fragment>
        <NavBar openForm={handleFormOpen} />
        <Container style={{ marginTop: '7em' }}>
            <ActivityDashboard
            activities={activities}
            selectedActivity={selectedActivity}
            handleSelectedActivity={handleSelectedActivity}
            handleCanceledActivity={handleCanceledSelectActivity}
            editMode={editMode}
            openForm={handleFormOpen}
            closeForm={handleFormClose}
            createOrEdit={handleCreateOrEditActivity}
            deleteActivity={handleDeleteActivity}
            />
        </Container>
        </Fragment>
    );
    }

    export default App;


Atualize o componente 'ActivityDashboard.tsx' para receber a nova propriedade 'selectedActivity' e as funções.

    import React from 'react';
    import { Grid, List } from 'semantic-ui-react';
    import { Activity } from '../../../app/models/activity';
    import ActivityDetails from '../details/ActivityDetails';
    import ActivityForm from '../form/ActivityForm';
    import ActivityList from './ActivityList';

    interface Props {
        activities: Activity[];
        selectedActivity: Activity | undefined;
        handleSelectedActivity: (id: string) => void;
        handleCanceledActivity: () => void;
    }

    export default function ActivityDashboard({
            activities,
            selectedActivity,
            handleSelectedActivity,
            handleCanceledActivity }: Props) {
        return (
            <Grid>
                <Grid.Column width='10'>
                    <ActivityList activities={activities} handleSelectedActivity={handleSelectedActivity} />
                </Grid.Column>
                <Grid.Column width='6'>
                    {selectedActivity && (
                        <ActivityDetails 
                            activity={selectedActivity} 
                            handleCanceledActivity={handleCanceledActivity} 
                        />
                    )}
                    <ActivityForm />
                </Grid.Column>
            </Grid>
        )
    }

Atualize o componente 'ActivityList.tsx' para fazer uso do método 'handleSelectedActivity'.

    ...
    interface Props {
        activities: Activity[];
        handleSelectedActivity: (id: string) => void;
    }
    ...
    <Button onClick={() => handleSelectedActivity(activity.id)} floated='right' content='View' color='blue' />
    ...

Atualize o componente 'ActivityDetails.tsx' para fazer uso do método 'handleCanceledActivity'.

    ...
    interface Props {
        activity: Activity;
        handleCanceledActivity: () => void;
    }
    ...
    <Button onClick={handleCanceledActivity} basic color='grey' content='Cancelar' />
    ...

Crie o método 'handleCreateOrEditActivity' em 'App.tsx' que verifica o valor de 'activity.id' para remover o registro existente utilizando '.filter' e incluir a atividade atualizada, ou simplesmente adicionar a Atividade na lista de atividades caso não haja um 'id' definido.

    function handleCreateOrEditActivity(activity: Activity) {
        activity.id 
            ? setActivities([...activities.filter(o => o.id !== activity.id), activity])
            : setActivities([...activities, activity]);
        setEditMode(false);
        setSelectedActivity(activity);
    }

Em seguida inclua o novo método como parâmetro 'createOrEdit' do componente 'ActivityDashboard', na tag ainda em 'App.tsx'.

    ...
    <ActivityDashboard
          activities={activities}
          selectedActivity={selectedActivity}
          handleSelectedActivity={handleSelectedActivity}
          handleCanceledActivity={handleCanceledSelectActivity}
          editMode={editMode}
          openForm={handleFormOpen}
          closeForm={handleFormClose}
          createOrEdit={handleCreateOrEditActivity}
        />
    ...

 Na interface 'Props' no componente 'ActivityDashboard'.
        
    interface Props {
        activities: Activity[];
        selectedActivity: Activity | undefined;
        handleSelectedActivity: (id: string) => void;
        handleCanceledActivity: () => void;
        editMode: boolean;
        openForm: (id: string) => void;
        closeForm: () => void;
        createOrEdit: (activity: Activity) => void;
    }

No 'destructure' dos parâmetros do componente.

    ...
    export default function ActivityDashboard({
            activities,
            selectedActivity,
            handleSelectedActivity,
            handleCanceledActivity,
            editMode,
            openForm,
            closeForm,
            createOrEdit,
        }: Props) {
        return (
    ...

E de maneira semelhante, envie esta propriedade para o componente 'ActivityForm'.

    {editMode && (
        <ActivityForm 
            closeForm={closeForm} 
            activity={selectedActivity} 
            createOrEdit={createOrEdit} 
        />
    )}

No componente 'ActivityForm' inclua a propriedade na interface e nos parâmetros do componente. E inclua a chamada para função no método 'handleSubmit'.

    function handleSubmit() {
        createOrEdit(activity);
    }

Neste ponto temos a funcionalidade para editar e em seguida vamos instalar o módulo npm 'uuid' usado para gerar hashes únicos que usaremos como identificadores ao inserir registros.

No terminal, aterne para pasta do projeto react (client-app) e execute o comando abaixo.

    > npm install uuid

Abra o arquivo 'App.tsx' e inclua o import para a lib 'uuid'.

    import { v4 as uuid } from 'uuid';

Algumas libs não possuem arquivos de definição de tipos para o TypeScript e podem apresentar um erro (ou warning). No caso da lib 'uuid', uma mensagem de erro pode ser exibida informando o problema e orientando para a solução. Veja no exemplo a seguir.

    'uuid' is declared but its value is never read.
    Could not find a declaration file for module 'uuid'...
    Try 'npm i --save-dev @types/uuid' if it exists or add a new declaration (.d.ts) file containing declare module 'uuid';

O arquivo de definição de tipos existe no módulo 'uuid' e será instalado com sucesso.

Em seguida, altere o método 'handleCreateOrEditActivity' em 'App.tsx' para fazer uso do módulo 'uuid' e gerar um identificador único para novos registros.

    function handleCreateOrEditActivity(activity: Activity) {
        activity.id
        ? setActivities([...activities.filter(o => o.id !== activity.id), activity])
        : setActivities([...activities, {...activity, id: uuid()}]);
        setEditMode(false);
        setSelectedActivity(activity);
    }

Crie o arquivo 'agent.ts' em '/src/app/api' com o código abaixo.
    
    import axios, { AxiosResponse } from 'axios';
    import { Activity } from '../models/activity';

    const sleep = (delay: number) => {
        return new Promise((resolve) => {
            setTimeout(resolve, delay)
        })
    }

    axios.defaults.baseURL = 'http://localhost:5000/api'

    axios.interceptors.response.use(async response => {
        try {
            await sleep(1000);
            return response;
        } catch (error) {
            console.log(error);
            return await Promise.reject(error);
        }
    })

    const responseBody = <T> (response: AxiosResponse<T>) => response.data;

    const requests = {
        get: <T> (url: string) => axios.get<T>(url).then(responseBody),
        post: <T> (url: string, body: {}) => axios.post<T>(url, body).then(responseBody),
        put: <T> (url: string, body: {}) => axios.put<T>(url, body).then(responseBody),
        del: <T> (url: string) => axios.delete<T>(url).then(responseBody),
    }

    const Activities = {
        list: () => requests.get<Activity[]>('/activities')
    }

    const agent = {
        Activities
    }

    export default agent;


...

Pacotes necessários para implementar MobX em aplicações React.

    npm install mobx mobx-react-lite



