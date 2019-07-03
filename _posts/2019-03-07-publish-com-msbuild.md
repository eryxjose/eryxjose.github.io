---
layout: post
author: Eryx
title: Deploy de aplicações utilizando linha de comando
date: 03/07/2019
---

Você pode definir as propriedades de publicação de sua aplicação no Visual Studio e então utilizar o utilitário MSBuild.exe através de linha de comando para agilizar e ou automatizar o processo de geração dos arquivos para deploy. 

Comando msbuild para geração dos arquivos de deploy para a solution D:\repos\survey\adm\SurveyADM.sln no diretório D:\ClickOnceDeploymentFiles\surveyadm\.

	msbuild D:\repos\survey\adm\SurveyADM.sln /target:publish -property:PublishDir=D:\ClickOnceDeploymentFiles\surveyadm\

O atributo /target pode receber múltiplos valores separados por ponto e vírgula. Para recompilar a aplicação antes de publicar utilize:

	msbuild D:\repos\survey\adm\SurveyADM.sln /target:rebuild;publish -property:PublishDir=D:\ClickOnceDeploymentFiles\surveyadm\

## Referências

Você pode utilizar o utilitário Mage.exe para atualizar o manifesto ClickOnce, utilizar Trusted Application Deployment, Authenticode, Full Trust, entre outros. O artigo [Walkthrough: Manually deploy a ClickOnce application](https://docs.microsoft.com/en-us/visualstudio/deployment/walkthrough-manually-deploying-a-clickonce-application?view=vs-2019) contém referências e informações adicionais.

Consulte o artigo [First look at deployment in Visual Studio](https://docs.microsoft.com/en-us/visualstudio/deployment/deploying-applications-services-and-components?view=vs-2019#create-an-installer-package-windows-desktop) para os recursos e configurações de deploy disponíveis.

Consulte o artigo [Deploying a WPF Application (WPF)](https://docs.microsoft.com/en-us/dotnet/framework/wpf/app-development/deploying-a-wpf-application-wpf) para outras opções de deploy (XCopy, Windows Installer, e ClickOnce).

Visite a [documentação do Mono](https://www.mono-project.com/docs/about-mono/releases/5.0.0/#msbuild) para informações sobre a utilização do MSBuild no Linux.



