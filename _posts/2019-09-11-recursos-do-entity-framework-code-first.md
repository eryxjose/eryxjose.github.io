---
layout: post
author: Eryx
title: Resumo sobre Entity Framework Code First
date: 11/09/2019
---

Visão geral das ações realizadas quando utilizando a abordagem Code First para definição do modelo de dados da aplicação.

# Criar entidades do modelo de dados ou domínio da aplicação

As entidades são definidas através de classes podem conter propriedades e métodos. 

Classes e propriedades podem ser decoradas com atributos para estender suas características e comportamentos.

Propriedades de navegação são utilizadas para representar entidades relacionadas.

O modificador virtual pode ser utilizado nas propriedades de navegação para possibilitar o carregamento posterior dos respectivos dados.

# Configurar objeto de conexão com a base de dados utilizando DbContext

O objeto de conexão é utilizado para intermediar as operações de carregamento ou manipulação de dados na base.

O objeto de conexão da aplicação é uma classe que herda de DbContext e contém um conjunto de propriedades DbSet que representam os dados de cada entidade da aplicação. Também pode conter métodos para sobrescrever e configurar relacionamentos entre as entidades.

	...
	
	public class AppContext : DbContext
	{
		public DbSet<Entidade1> Entidade1Collection { get; set; }
		public DbSet<Entidade1> Entidade1Collection { get; set; }
		public DbSet<Entidade1> Entidade1Collection { get; set; }
		
		/*
		Ao definir a propriedade name da connection string com um valor diferente do nome do dado ao objeto DbContext (ex.: AppContext), será
		necessário utilizar : base("name=DefaultConnection") para informar o nome da conexão a ser utilizada.
		*/
		
		public AppContext() 
			: base("name=DefaultConnection")
		{
		}
	}
	
	...

# Configurar a string de conexão 

A string de conexão com o banco de dados é definida no arquivo de configurações (ex.: app.config) na chave <connectionStrings>

Cada string de conexão contém uma propriedade name, connectionString e providerName no caso de aplicações que utilizam a abordagem Code First.

	<connectionStrings>
		<add name="DefaultConnection" connectionString="data source=.\SQLEXPRESS; initial catalog=NomeDoBancoDados; integrated security=SSPI" providerName="System.Data.SqlClient" /> 
	</connectionStrings>

# Habilitar Migrations em uma nova aplicação

Exiba o painel Package Manager Console através do menu Tools - NuGet Package Manager, e entre com o commando abaixo. Este comando é executado apenas uma vez para habilitar o uso de Migrations na aplicação. 

	> enable-migrations
	
Adicione uma migration inicial que será gerada a partir das propriedades DbSet definidas no seu objeto de conexão (ex.: AppContext). Utilize um nome representativo para cada migration adicionada.

	> add-migration initial-model

Todas as migrations contém métodos Up() e Down() gerados automaticamente a partir das propriedades DbSet definidas no objeto de contexto AppContext no exemplo. O comando CreateTable é utilizado na criação de tabelas e recebe como parâmetros o nome da tabela a ser criada e uma função anônima com as propriedades de configuração da mesma.

	...
	
	public overide void Up() 
	{
		...
		
		CreateTable(
			"dbo.Authors",
			c => new 
				{
					Id = c.Int(nullable: false, identity: true),
					Name = c.String(),
				})
			.PrimaryKey(t => new { t.Id });
		
		CreateTable(
			"dbo.Courses",
			c => new 
				{
					Id = c.Int(nullable: false, identity: true),
					Title = c.String(),
					Description = c.String(),
					Level = c.Int(nullable: false),
					FullPrice = c.Single(nullable: false),
					Author_Id = c.Int(),
				})
			.PrimaryKey(t => new { t.Id });
			.ForeignKey("dbo.Authors", t => t.Author_Id)
			.Index(t => t.Author_Id);

		...

	}
	
	...

O segundo exemplo define uma chave estrangeira Author_Id com a tabela Authors e um índice também para Author_Id para agilizar a obtenção de todos os cursos de um determinado autor.

O exemplo abaixo mostra uma relação muitos para muitos entre as tabelas Tags e Courses. Neste caso, o Entity Framework gera automaticamente uma tabela de relacionamento. Observe que a chave primária em uma relação muitos para muitos é composta. A propriedade cascadeDelete define a exclusão dos registros relacionados. 

	...
	
	CreateTable(
		"dbo.TagCourses",
		c => new 
			{
				Tag_Id = c.Int(nullable: false),
				Course_Id = c.Int(nullable: false),
			})
		.PrimaryKey(t => new { t.Tag_Id, t.Course_Id } );
		.ForeignKey("dbo.Tags", t => t.Tag_Id, cascadeDelete: true)
		.ForeignKey("dbo.Courses", t => Course_id, cascadeDelete: true)
		.Index(t => t.Tag_Id)
		.Index(t => t.Course_Id);

	...

Utilize o comando update-database para atualizar o banco de dados.

	> update-database

# Habilitar Migrations para uma aplicação com banco de dados existente

Utilize o comando "Add New Item" do menu de contexto para adicionar um novo "ADO.NET Entity Data Model" ao projeto.

* Clique com o botão direito sobre o nome do projeto no painel Solution Explorer, aponte para Item e clique em New Item.
* Selecione ADO.NET Entity Data Model na caixa de diálogo e entre com um nome para o objeto de conexão (ex.: AppContext) e clique em Avançar.
* Selecione "Code First from database" e clique em Avançar.
* Clique no botão "New Connection..." para criar uma nova string de conexão utilizando a tela de propriedades da conexão e clique em Ok.
* Clique em avançar para exibir a tela onde você poderá selecionar as tabelas e views para geração das classes que representam o modelo de dados deixando de fora a tabela __Migrations. 
* Clique em Fihish para concluir e observe que o Entity Framework cria as classes para representar as tabelas e relacionamentos do banco de dados.

Observe que as propriedades de navegação geradas automaticamente nas classes a partir dos relacionamentos das tabelas são definidas com o tipo ICollection<>. O tipo ICollection<> e consequentemente o IList<> permitem acessar itens utilizando um índice. O modificador de acesso "virtual" possibilita o uso de Lazy Loading na propriedade de navegação Courses.

	public virtual ICollection<Course> Courses { get; set; }

Observe que as propriedades de navegação também são inicializadas no método construtor.

	...
	
	public partial class Tag 
	{
		public Tag() 
		{
			Courses = new HashSet<Course>();
		}
	}
	
	...

# Sobrescrevendo padrões 

Sobrescreva o método de inicialização do modelo de dados no ciclo de vida do Entity Framework para modificar os padrões pré-definidos de nomeclaturas e outras propriedades. O exemplo abaixo define o relacionamento lógico entre as tabelas Author e Courses, e o relacionamento muitos pra muitos entre as tabelas Course e Tags.

	...
	
	protected overide void OnModelCreating(DbModelBuilder modelBuilder)
	{
		modelBuilder.Entity<Author>()
			.HasMany(e => e.Courses)
			.WithOptional(e => e.Author)
			.HasForeignKey(e => e.Author_Id);
		
		modelBuilder.Entity<Course>()
			.HasMany(e => e.Tags)
			.WithMany(e => e.
	}

	...

# Migrations

Utilize o atributo -Force para sobrescrever uma Migration.

	> add-migration added-categories-table -Force

Crie migrations para alterações simples e pequenas. Não inclua múltiplas alterações em uma única Migration.

Utilize o método Sql para executar comandos SQL. Você pode utilizar este comando para inserir dados iniciais nas tabelas.

	...
	
	CreateTable(
		"dbo.Authors",
		c => new 
			{
				Id = c.Int(nullable: false, identity: true),
				Name = c.String(),
			})
		.PrimaryKey(t => new { t.Id });

	Sql("INSERT INTO Authors VALUES ('Robert C. Martin')");
	Sql("INSERT INTO Authors VALUES ('Martin Fowler')");
	
	...

# Configurações para Migrations

Utilize a classe Configuration para alterar a propriedade AutomaticMigrationsEnable. Esta propriedade não existirá mais a partir da versão 7 do Entity Framework.

Utilize Configuration para definir ou alterar as propriedades MigrationsAssembly, MigrationsDirectory ou MigrationsNamespace.

	...
	
	public Configuration() 
	{
		AutomaticMigrationsEnable = false;
		this.MigrationsDirectory = "Database\\Migrations";
		this.MigrationsNamespace = "MyApp.Database.Migrations";
	}
	
	...

Este texto apresenta um resumo dos recursos do Entity Framework Code First com alguns exemplos de código.

# Referências

[Running & Scripting Migrations from Code](https://romiller.com/2012/02/09/running-scripting-migrations-from-code/)

[Tutorial: Use EF Migrations in an ASP.NET MVC app and deploy to Azure](https://docs.microsoft.com/en-us/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application)


[ASP.NET Web Deployment using Visual Studio: Introduction](https://docs.microsoft.com/en-us/aspnet/web-forms/overview/deployment/visual-studio-web-deployment/introduction)


