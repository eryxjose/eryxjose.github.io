---
layout: post
author: Eryx
title: Criando projeto web Ethereum com Solidity e React
date: 02/10/2021
---

Este texto descreve a criação de uma aplicação web para financiamento comunitário de campanhas (crowd funding) utilizando 'Smart Contracts' Ethereum com Solidity e frontend ReactJS.

...

Crie uma pasta para o projeto e dentro dela uma pasta 'ethereum' com as subpastas 'build' e 'contracts', e o arquivo 'compile.js'.

A compilação dos contratos ocorrerá ao executar o arquivo 'compile.js' e executará os seguintes passos:

* Excluir a pasta build
* Carregar o conteúdo do arquivo 'Contratos.sol' da pasta 'contratos'.
* Compilar os contratos utilizando o compilador solidity.
* Direcionar a saída da compilação na pasta 'build'.

Inicialize um projeto npm para criar o arquivo package.json.

    > npm init

Instale as bibliotecas

    > npm install --save ganache-cli mocha solc fs-extra web3

...

O código abaixo contém as dependências necessárias para compilar, fazer deploy, testar, e se conectar a uma rede Ethereum.

    // package.json 
    {
        "name": "inboxethereum",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
            "test": "mocha"
        },
        "author": "",
        "license": "ISC",
        "dependencies": {
            "ganache-cli": "^6.12.2",
            "mocha": "^8.3.2",
            "solc": "^0.4.17",
            "truffle-hdwallet-provider": "^0.0.3",
            "web3": "^1.3.5"
        }
    }



...

Utilizaremos NextJS para iniciar um projeto React com diversos recursos adicionais (ex.: routing, server-side rendering, hot module reload) que facilitam a construção de aplicações multi-páginas na web.

Páginas/Telas: 

* Lista de campanhas
* Criar campanha
* Detalhes da campanha
* Requisições de gastos para campanha
* Criar requisição de gasto de campanha

Instale as bibliotecas.

    npm install next react react-dom

A página inicial lista as campanhas cadastradas.

* Utilize um provider Metamask para configurar a lib web3
* Configure a cópia publicada do contrato 'CampaignFactory' na lib web3
* Utilize a instância Factory para recuperar a lista de campanhas publicadas
* Utilize React para exibir informações sobre as campanhas

Crie o arquivo 'web3.js' na pasta 'ethereum' com o código a seguir. O código abaixo utiliza o objeto 'window.ethereum' disponibilizado pelo fato do Metamask estar instalado como extensão do navegador.

    import Web3 from "web3";
    
    window.ethereum.request({ method: "eth_requestAccounts" });
    
    const web3 = new Web3(window.ethereum);
    
    export default web3;

Crie o arquivo 'factory.js' também na pasta 'ethereum'.

...

Semantic UI React

    npm install --save semantic-ui-react

E respectivo módulo css

    npm install --save semantic-ui-react semantic-ui-css

...

    Important Note About Installing next-routes
    In the upcoming lecture, we will be installing the next-routes library. If you see some error like ERESOLVE unable to resolve dependency tree errors, you'll need to pass the --legacy-peer-deps flag:

    npm install next-routes --legacy-peer-deps

...
    Required Web3 Update - Do Not Skip
    In the upcoming lecture, we will be creating a web3 module to connect with Metamask in our client application.

    The code used in the lecture has since been fully deprecated. Some of the fixes mentioned in the QA using ethereum.enable() have also now been deprecated:

    https://docs.metamask.io/guide/ethereum-provider.html#legacy-methods

    The web3.js module will need to be updated to include the current method for requesting access to Metamask:

    import Web3 from "web3";
    
    window.ethereum.request({ method: "eth_requestAccounts" });
    
    const web3 = new Web3(window.ethereum);
    
    export default web3;
    Note - this is still not 100% correct as we will be refactoring again to add a conditional in a few lectures.

...

    Required Update for gasPrice - Error: transaction underpriced
    In the upcoming lecture, we will be writing some code to deploy our contract to the Rinkeby network. Due to some changes caused by the EIP 1599 hard fork, you will see an error in the terminal:

    Error: transaction underpriced

    To prevent this, we will need to specify a gasPrice with an amount high enough to ensure the transaction will get mined quickly:

    .send({ gas: '1000000', gasPrice: '5000000000', from: accounts[0] });

...

    Next.js Required Version Update
    In the upcoming lecture, we will be installing a specific version of Next.js. Unfortunately, some dependencies included are completely broken. Do not install the version shown in the lecture.

    Instead, run the following:

    npm install next react react-dom

