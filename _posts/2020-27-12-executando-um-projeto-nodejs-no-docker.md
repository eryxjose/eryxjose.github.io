---
layout: post
author: Eryx
title: Executando um projeto NodeJS no Docker
date: 27/12/2020
---

Este texto descreve os passos gerais para utilizar o Docker para executar uma aplicação NodeJS em um container.

Os passos gerais incluem:

* Criar uma aplicação NodeJS
* Criar o Dockerfile
* Gerar a imagem a partir do Dockerfile
* Executar o container a partir da imagem
* Utilizar o navegador para acessar a aplicação web no container

Inicialmente crie uma pasta para aplicação NodeJS, por exemplo SimpleServer. Em meu sistema, tenho uma pasta dev dentro da minha pasta pessoal no Linux Ubuntu. E utilizo esta pasta para todos os repositórios, estudos e projetos temporários.

    $ mkdir SimpleServer

Acesse a pasta criada com o comando cd. Lembrando que você pode começar a digitar o nome da pasta e pressionar tab para auto-completar o caminho.

    $ cd SimpleServer/

Abra o VSCode com a pasta atual selecionada utilizando o comado "code .". O parâmetro "." indica o caminho para pasta atual onde o comando está sendo executado.

    $ code .

Crie o arquivo package.json da sua aplicação NodeJS. E defina algumas dependências e um script start para inicializar a aplicação.

    {
        "dependencies": {
            "express": "*"
        },
        "scripts": {
            "start": "node index.js"
        }
    }

Crie o arquivo index.js e configure uma aplicação express para responder com um texto qualquer nas requisições para localhost:8080.

    const express = require('express');
    const app = express();

    app.get('/', (req, res) => {
        res.send('Response from /');
    });

    app.listen(8080, () => {
        console.log("Listening on 8080.");
    });

Adicione o arquivo Dockerfile na pasta da sua aplicação Node. No snipet abaixo, a propriedade WORKDIR especifica uma pasta padrão dentro do container para os arquivos copiados no comando COPY, e resolve o erro do tipo "npm ERR!" ao executar o docker build. A cópia do arquivo package.json antes dos demais arquivos evita que o comando npm install seja executado a cada alteração em arquivos da aplicação.

    FROM node:alpine
    WORKDIR /usr/app
    COPY ./package.json ./
    RUN npm install
    COPY ./ ./
    CMD ["npm", "start"]

O comando docker build abaixo inclui o parâmetro -t para definir uma tag e o respectivo valor com nome de usuário no DockerHub seguido do nome do repositório.

    docker build -t eryxjose/simpleserver .

Execute a aplicação a partir da imagem criada utilizando o parâmetro -p para definir uma "port forward" de localhost para o container.

    docker run -p 8080:8080 eryxjose/simpleserver

Relevante comentar que você pode definir números diferentes ao mapear a porta local para porta da aplicação express no container. Lembrando de atualizar a porta definida no arquivo index.js.

Você pode executar o prompt de comando (shell) sh com o comando exec utilizando o id do container.

Obtenha o id do container.

    $ docker ps

E então utilize o comando exec com parâmetro -it passando o id do container. O parâmetro -it conecta o canal de input/output do container com formatações pré-definidas ao prompt sh.

    $ docker exec -it <id-container> sh

O prompt sh por padrão mostrará o caminho para pasta definida na propriedade WORKDIR do Dockerfile.

