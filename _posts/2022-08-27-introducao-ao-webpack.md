---
layout: post
author: Eryx
title: Introdução ao WebPack 
date: 27/08/2022
---

Em resumo, podemos dizer que o [WebPack](https://webpack.js.org/) faz o bundle de imagens, scripts e estilos.

Considere uma aplicação cliente NodeJS com a seguinte estrutura

    app/
      assets/
        images/
        scripts/
          App.js
        styles/
          styles.css
      index.html
    node_modules/
    package-lock.json
    package.json

Instale o webpack

    $ npm install webpack webpack-cli --save-dev

Crie um arquivo de configuração com o nome 'webpack.config.js' na pasta raiz do projeto contendo o código a seguir.

    const path = require('path')

    module.exports = {
        entry: './app/assets/scripts/App.js',
        output: {
            filename: 'bundled.js',
            path: path.resolve(__dirname, 'app')
        },
        mode: 'development',
        watch: true
    }

Este arquivo (módulo) de configuração do webpack permite definir o caminho para os arquivos que farão parte do bundle (entry), o nome e caminho do arquivo de bundle gerado (output, filename, path), o modo ou ambiente de execução (mode), e habilitar a auto execução mediante alterações nos arquivos originais (watch).

Modifique o arquivo package.json para incluir um script 'dev' para executar o comando webpack.
    
    ...
    "scripts": {
        "dev": "webpack",
    ...

Em seguida execute o código no terminal utilizando o comando 'npm run dev'.

    $ npm run dev

Verifique que o arquivo 'bundle.js' foi criado na pasta '/aoo', mesmo local do arquivo 'index.html'.

Utilize a tag <script></script> para referenciar o arquivo de bundle gerado na mesma pasta do arquivo 'app/index.html'. Inclua a tag script no final da página para permitir o carregamento do html antes de iniciar o carregamento do bundle.

    <script src="bundled.js"></script>

Para configurar o vscode para reconhecer a sintaxe de css aninhados, exiba o painel de configurações (Ctrl + ,) e clique no botão 'Abrir Configurações' no canto superior direito da tela, e inclua o trecho abaixo no arquivo .json de configurações.

    "files.associations": {
            "*.css": "scss"
    },




