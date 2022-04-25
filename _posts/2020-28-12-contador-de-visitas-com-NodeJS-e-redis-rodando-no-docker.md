---
layout: post
author: Eryx
title: Contador de Visitas com NodeJS e Redis rodando no Docker Compose
date: 28/12/2020
---

Este texto descreve a implementação de um contador de visitas NodeJS que armazena informações em um servidor Redis em containers diferentes para ilustrar aspectos de integração.

A separação da aplicação Node e do servidor Redis em containers diferentes fornece a maior escalabilidade.

O arquivo package.json contém referências para módulos express e redis.

    {
        "dependencies": {
            "express": "*",
            "redis": "2.8.0"
        },
        "scripts": {
            "start": "node index.js"
        }
    }

O arquivo index.js utiliza o módulo redis para obter e atribuir valores ao servidor redis. O método createClient recebe um objeto de configuração com o atributo host com o url ou caminho local do servidor redis.

    const express = require('express');
    const redis = require('redis')

    const app = express();
    const client = redis.createClient({
        host: 'redis-server' // nome definido em docker-compose.yml 
    });

    client.set('visits', 0); // valor de visits ao inicializar a aplicação

    app.get('/', (req, res) => {
        client.get('visits', (err, visits) => {
            res.send('Number of visits is: ' + visits);
            client.set('visits', parseInt(visits) + 1);
        });
    });

    app.listen(8080, () => {
        console.log("Listening on 8081.");
    });

O arquivo Dockerfile para imagem NodeJS copia o package.json antes dos demais arquivos da aplicação para evitar a reinstalação desnecessária de pacotes npm ao alterar código da aplicação. Desta forma, o comando npm install será re-executado somente quando as dependências do projeto forem alteradas no arquivo package.json.

    FROM node:alpine
    WORKDIR /app
    COPY package.json .
    RUN npm install
    COPY . .
    CMD ["npm", "start"]

Utilize o comando docker build para gerar a imagem.

    docker build -t <user-dockerhub>/visits:latest .

Execute o comando 'docker run redis' para obter o container do servidor redis onde as informações das visitas da aplicação NodeJS serão armazenadas.

    docker run redis

 O Docker CLI fornece um recurso nativo para criar uma conexão de rede entre os containers, pouco utilizado em razão da complexidade de implementação.

Docker Compose é utilizado para inicializar múltiplos containers com recursos de conectividade habilitados automaticamente e automatizar parâmetros passados para o 'docker run'.

Crie um arquivo docker-compose.yml conforme snipt abaixo.

    version: '3'
    services:
        redis-server:
            image: 'redis'
        node-app:
            build: .
            ports:
            - "8081:8081"

Utilize o comando 'docker-compose up' para gerar os containers definidos no arquivo docker-compose.yml. 

    $ docker-compose up

Inclua o parâmetro '-build' quando houverem alterações nos arquivos e seja necessário recriar os containers.

    $ docker-compose up -build

Utilize 'docker-compose up -d' para inicializar os containers em background.

    $ docker-compose up -d

Você pode parar todos os containers em execução com o comando 'docker-compose down'.

    $ docker-compose down

Adicione o atributo "restart: always" ao container da aplicação NodeJS no arquivo docker-compose.yml para que seja reiniciado automaticamente caso algum problema ocorra.

    version: '3'
    services:
    redis-server:
        image: 'redis'
    node-app:
        restart: always
        build: .
        ports:
        - "8082:8082"

Com esta configuração, caso algum erro ocorra na aplicação NodeJS, o container será reiniciado e a aplicação estará disponível novamente. Veja abaixo as opções de restart (ou Restart Policies).

* "no" - Não tenta reiniciar o container caso ocorra algum problema. Precisa ser entre aspas porque no tem significado false no yml.
* always - Reinicia o container sempre que ocorrer qualquer problema.
* on-failure - Reinicia o sistema apenas quando houver um código de erro (diferente de zero).
* unless-stopped - Sempre reinicia a menos que seja finalizado pelo desenvolvedor com o comando docker stop.

Utilize 'docker-compose ps' na mesma pasta onde está armazenado o arquivo docker-compose.yml.

    $ docker-compose ps



