---
layout: post
author: Eryx
title: Desenvolvimento com TypeScript
date: 04/02/2021
---

TypeScript é usado no desenvolvimento, suporta todos os recursos do JavaScript e fornece um 'Type System'. O maior benefício de um 'Type System' é possibilitar a identificação de erros durante o desenvolvimeto utilizando Type Annotations para analisar o código.

Todo código escrito em TypeScript passa por um processo de compilação antes de ser executado no navegador. Este processo converte todo código TypeScript para 'Plain Old JavaScript', ou seja, JavaScript compatível com navegadores antigos.

Veja exemplos do código JavaScript gerado a partir do TypeScript em [TypeScriptLang.org/play](https://www.typescriptlang.org/play).

## Configurações de ambiente

Instale o compilador TypeScript.

    $ sudo npm install -g typescript ts-node

O módulo 'ts-node' possibilita executar compilação de código TypeScript utilizando o terminal.

Teste a instalação no terminal com o comando 'tsc --help'. O comando 'tsc' (typescript compiler) é disponibilizado pelo módulo typescript instalado.

## VSCode

Depois de instalar o vscode, você pode personalizar configurações, instalar extensões, alterar o thema utilizado, entre outros.

## Simple App

* Avaliar API http://jsonplaceholder.typicode.com/
* Criar uma pasta para o projeto
* Criar package.json
* Instalar a lib axios para fazer requisições
* Codificar

Acesse o url http://jsonplaceholder.typicode.com/ e localize Resources. Clique no item 'todos' para visualizar um exemplo do json retornado. O mesmo url pode ser usado no código para retornar os dados json. Passe o id de um dos registros no url para carregar um registro específico.

Crie uma pasta para aplicação. Por exemplo, 'fetchjson'.

Em seguida, crie o arquivo package.json utilizando o comando 'npm init -y'.

Instale a lib axios com o comando 'npm i axios'.

Crie o arquivo index.ts e cole o código abaixo.

    import axios from 'axios';

    const url = 'http://jsonplaceholder.typicode.com/todos/1';

    axios.get(url).then(response => {
        console.log(response.data);
    });

No terminal, entre com o comando 'tsc index.ts' e pressione 'enter'. Verifique que o arquivo index.js foi criado na mesma pasta. 

Para executar o app, entre com o comando 'node index.js' no terminal.

Utilize o comando 'ts-node index.ts' para executar os dois passos (compilar o index.ts e executar o index.js) de uma vez.
