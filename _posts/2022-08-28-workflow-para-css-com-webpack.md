---
layout: post
author: Eryx
title: Workflow para CSS com WebPack
date: 28/08/2022
---

Pré-processadores CSS como Sass, Less, Stylus e PostCSS, possibilitam a utilização de recursos não suportados originalmente que simplificam o desenvolvimento e a manutenção de estilos. 

Entre estes recursos estão

* CSS Variables
* Nested CSS

Considere uma aplicação cliente NodeJS com a seguinte estrutura

    app/
      assets/
        images/
        scripts/
          App.js
        styles/
          styles.css
      bundle.js
      index.html
    node_modules/
    package-lock.json
    package.json
    webpack.config.js

O bundle de arquivos .css utilizando webpack requer a instalação dos módulos 'css-loader' e 'style-loader'.

    $ npm install css-loader style-loader --save-dev

Inclua a propriedade 'module' no arquivo webpack.config.js conforme exemplo a seguir para configurar o bundle de arquivos .css com webpack. 

    ...
    module: {
        rules: [
            {
                test: /\.css$/i,
                use: ['style-loader', 'css-loader']
            }
        ]
    }
    ...

No exemplo acima, foi definido um array de rules contendo um objeto com as propriedades test e use. A propriedade test contém uma expressão regular que identifica arquivos com a extensão .css e a propriedade 'use' define uma lista de módulos a serem executados, em nosso exemplo 'style-loader' e 'css-loader',  caso o teste da expressão regular retorne positivo.

O módulo 'css-loader' configura o webpack para ser capaz de fazer o bundle de arquivos .css enquanto o módulo 'style-loader' efetivamente carrega os estilos definidos pelo bundle no navegador.

Relevante observar que o plugin (módulo) 'css-loader' trata as imagens referenciadas nos estilos por padrão. Para alterar este comportamento, inclua uma opção adicional conforme exemplo a seguir.

    ...
    use: ['style-loader', 'css-loader?url=false']
    ...

Inclua uma referência para o arquivo style.css no início do arquivo App.js conforme exemplo.

    import '../styles/styles.css'
    ...

Inclua o script abaixo na propriedade scripts do arquivo package.json

    ...
    "scripts": {
        "dev": "webpack",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    ...

Execute a aplicação para atualizar o bundle.

    $ npm run dev

Instale o módulo 'postcss-loader' para configurar PostCSS no webpack.

    $ npm install postcss-loader --save-dev

Em seguida, instale os módulos a seguir. Os módulos abaixo, serão carregados na propriedade 'plugins' do objeto de configuração do 'postcss-loader'.

    $ npm install postcss-simple-vars postcss-nested autoprefixer --save-dev

Crie um array para fazer o require dos módulos instalados acima.

    const postCSSPlugins = [
        require('postcss-simple-vars'),
        require('postcss-nested'),
        require('autoprefixer')
    ]

Inclua um objeto de configuração no array de modulos a serem aplicados em arquivos .css e atribua o array de módulos 'postCSSPlugins' na propriedade 'plugins' do objeto de configuração.

    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader', {loader: 'postcss-loader', options: {postcssOptions: {plugins: postCSSPlugins}}}]
      }
    ]

Faça um teste no funcionamento dos módulos criando uma variável no arquivo styles.css e um elemento html que faça uso do estilo '.large-hero' do exemplo.

    ...
    $mainBlue: #2f5577
    
    .large-hero {
        h2 {
            color: $mainBlue;
        }
    }
    ...

Execute o app com o comando 'npm run dev' e verifique que o estilo foi aplicado corretamente.

[Autoprefixer](https://www.npmjs.com/package/autoprefixer) é um plugin para PostCSS que adiciona automaticamente prefixos específicos de cada navegador para regras CSS com base em valores disponibilizados pelo site [Can I Use](https://caniuse.com/).

Exemplo de regra CSS que depende de implementações específicas.

    .item {
        columns: 300px 2;
    }

Regra CSS com prefixos aplicados.

    .item {
        -webkit-columns: 300px 2;
        -moz-columns: 300px 2;
        columns: 300px 2;
    }

