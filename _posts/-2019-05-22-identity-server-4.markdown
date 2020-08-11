---
layout: post
author: Eryx
title: Visão Geral do Identity Server 4
date: 22/05/2019
---

Visão Geral do IdentityServer4

# Resumo

O IdentityServer4 é um framework OpenID Connect e OAuth 2.0 para ASP.NET Core.

* Suporta OpenID e OAuth 2.0
* APS.NET Core 2
* Autenticação centralizada (Autenticação como serviço)
* Diferentes tipos de aplicação (Web Apps, Native Apps, Web Services, Web APIs, etc.) podem utilizar o mesmo serviço de autenticação
* Single Sign On (SSO)
* APIs Access Control
* Open Source instalado via NuGet 
* Registro de diferentes tipos de clientes, usuários e recursos, publicação de tokens e autenticação centralizada.

# Interação dos Componentes

           Identity Server
                  |
                  |
    Users ---- Clients ---- Resources

# Plugins Google Chrome 

* Json Formater
* Postman (aplicativo para instalação)

# Instalação .NET Core 

Link para download do Framework .NET Core
https://dotnet.microsoft.com/download

# NuGet Packages

* IdentityServer4 e updates
* IdentityServer4.EntityFramework v2.3.2
* IdentityServer4.EntityFramework.Storage v2.3.1

# Blank Solution

* Execute o Visual Studio.
* Clique em File / New Project.
* Selecione o template Other Project Types / Visual Studio Solution / Blank Solution.
* Informe um nome para o Projeto, selecione o local, o nome da Solution e clique no botão Ok.
* Adicione novos projetos à solution

Observações:

* É recomendado criar uma solução separada para o Identity Server.

# Criar Projeto Web API

* Clique com o botão direito na Solution e selecionar Add / New Project.
* Selecione o template ASP.NET Web Application
* Em seguida selecionar API e desmarcar caixa Configure for HTTPS

# Pasta Models

A pasta Models contém as classes de entidades da aplicação e a classe de acesso ao objeto de dados DbContext no caso de utilizar o Entity Framework para encapsular o acesso a dados.

Veja um exemplo de classe para entidade Clientes.

    public class Cliente 
    {
        public long Id { get; set; }
        public string PrimeiroNome { get; set; }
        public string Sobrenome { get; set; }
    }

Veja um exemplo de classe de encapsulamento de dados (Context).

    public class AppContext : DbContext
    {
        public AppContext(DbContextOptions<AppContext> options)
            : base(options)
        {
        }

        public DbSet<Cliente> Clientes { get; set; }
    }

Em aplicações profissionais destinadas ao ambiente de produção é recomendado empregar o padrão Repository para encapsular os componentes de acesso a dados.

# Pasta Controllers


# Properties / launchSettings.json

O valor da propriedade launchUrl devem referenciar api/nome-do-controller. Para o controller CustomersController por exemplo.

    ...
    "profiles": {
        "IIS Express": {
        "commandName": "IISExpress",
        "launchBrowser": true,
        "launchUrl": "api/customers",
        "environmentVariables": {
            "ASPNETCORE_ENVIRONMENT": "Development"
        }
        },
    ...


# Startup.cs


* IdentityModel

    https://github.com/IdentityModel/IdentityModel



# Referências

[Learn .NET Core and the .NET Core SDK tools by exploring these Tutorials](https://docs.microsoft.com/pt-br/dotnet/core/tutorials/)

[.NET Core Guide](https://docs.microsoft.com/pt-br/dotnet/core/)

http://docs.identityserver.io/en/latest/topics/clients.html

[Identity Server Defining Clients](http://docs.identityserver.io/en/latest/topics/clients.html)

[Identity Server Grant Types](http://docs.identityserver.io/en/latest/topics/grant_types.html)

[About IdentityServer4](https://github.com/IdentityServer/IdentityServer4)


