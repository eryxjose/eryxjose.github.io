---
layout: post
author: Eryx
title: Visão Geral GeoServer
date: 04/09/2020
---


# Configurações para instalação do GeoServer

Crie uma pasta Java na pasta "Arquivos de Programas" do Windows e extraia o arquivo jdk-11.0.1.rar dentro da mesma. 

Em seguida crie uma variável de ambiente "JAVA_HOME" com o caminho para o jdk (ex.: c:\Arquivos de Programas\Java\jdk-11.0.1).

Link de download do GeoServer na versão estável.

	http://geoserver.org/release/stable/
	
Url de download do binário da versão 2.17.2 em SourceForge.

	http://sourceforge.net/projects/geoserver/files/GeoServer/2.17.2/geoserver-2.17.2-bin.zip

A versão binária é feita para funcionar em qualquer sistema operacional onde haja o java instalado.

Acesse a pasta bin e execute o arquivo startup.bat para iniciar o GeoServer. Uma tela de console será exibida com o andamento da inicialização do GeoServer.

Acesse o geoserver no navegador utilizando o url abaixo:

	http://localhost:8080/

Utilize usuário e senha padrão para fazer login.

	Usuário: admin
	Senha: geoserver

A pasta geoserver/data_dir/data contém alguns arquivos de shape para exemplos.




TODO: 

* Verificar se jdk-11.0 é ainda requerido para instalação do GeoServer

	