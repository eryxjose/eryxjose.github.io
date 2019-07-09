---
layout: post
author: Eryx
title: O Framework .NET Core e o Identity Server
date: 22/05/2019
---

# Resumo



Plugins Google Chrome

* Json Formater
* Postman

# Instalação .NET Core 

Link para download do Framework .NET Core
https://dotnet.microsoft.com/download


# Criar Blank Solution

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



# Identity Server 4

NuGet Packages para projeto IdentityServer

* IdentityServer4 e updates
* IdentityServer4.EntityFramework v2.3.2
* IdentityServer4.EntityFramework.Storage v2.3.1

NuGet Packages para projeto Client

* IdentityModel

    https://github.com/IdentityModel/IdentityModel



# Referências

[OpenID Connect Core 1.0 incorporating errata set 1](https://openid.net/specs/openid-connect-core-1_0.html)

[Learn .NET Core and the .NET Core SDK tools by exploring these Tutorials](https://docs.microsoft.com/pt-br/dotnet/core/tutorials/)

[.NET Core Guide](https://docs.microsoft.com/pt-br/dotnet/core/)

http://docs.identityserver.io/en/latest/topics/clients.html

[Identity Server Defining Clients](http://docs.identityserver.io/en/latest/topics/clients.html)

[Identity Server Grant Types](http://docs.identityserver.io/en/latest/topics/grant_types.html)

[About IdentityServer4](https://github.com/IdentityServer/IdentityServer4)


