---
layout: post
author: Eryx
title: Configurar Cookie Antiforgery no Postman
date: 10/07/2019
---

A proteção contra ataques do tipo Cross-Site Request Forgery e vulnerabilidades relacionadas ao uso de cookies em aplicações web é obtida com o uso de tokens Antiforgery. 

Ao testar uma API utilizando o Postman para métodos que possuem atributo de validação de token Antiforgery, será necessário definir o Cookie de autenticação manualmente a partir dos dados gerados por algum formulário web da aplicação.

Você pode utilizar as ferramentas de desenvolvedor do seu navegador para localizar e copiar as informações de token Antiforgery válidas. Na aba Network, localize e selecione a requisição do formulário web que contém o token. Localize e copie o nome do Cookie Antiforgery e respectivo valor na seção Request Headers no painel de detalhes ao lado da lista de requisições.
	
	...
	
	Cookie: .AspNetCore.Antiforgery.p76We6nqQ=CfDJ8Mo2vr5w1pFChtAsncFgr__p-pkCFxDLy26t9rlot984CMX3N8wFA5bXnD-LxqXdEdCnAKtsCo4P_FpC-9mJvbuxszr5qr-NZpTn8AV0eUaTIkIpv9oBahy9TdW-P34XfJKOj53XKaWBpq2ipc
	
	...

No Postman, acesse a aba Headers e clique no link Cookies, localizado no canto direito superior da janela abaixo do botão Send para exibir a tela Manage Cookies. Clique no botão Add Cookie para exibir uma caixa de texto onde você irá colar a chave e valor do cookie, por exemplo, ".AspNetCore.Antiforgery.xyz=CfDJ8Mo2vr5w1pFChtAsn". Em seguida, clique no botão Save para concluir a operação e teste o envio da sua requisição clicando no botão Send.

## Referências

O url abaixo aborda os riscos relacionados ao uso de cookies em aplicações web, descreve o uso de autenticação via token e opções relacionadas ao uso de Antiforgery Tokens.
https://docs.microsoft.com/en-us/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.2
