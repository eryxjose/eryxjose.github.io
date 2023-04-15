---
layout: post
author: Eryx
title: Arquitetura de arquivos e padrões para estilos em aplicações web
date: 28/08/2022
---

Uma arquitetura de arquivos de estilos é fundamental para manutenção de interfaces gráficas de aplicações web porque ajuda com a organização e entendimento do código, reduz o risco de conflitos entre estilos, possibilita combinar diferentes módulos para compor interfaces gráficas personalizadas, entre outros.

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

O primeiro passo é manter arquivos de estilo individuais para cada elemento ou bloco de elementos do site. 

Crie uma pasta 'modules' dentro da pasta styles para armazenar os arquivos de estilo de cada elemento ou bloco de elementos.

Vamos utilizar o prefixo '_' no nome dos arquivos de módulos css para indicar que são estilos parciais a serem utilizados em uma composição de estilos que montam a interface gráfica da aplicação.

Crie o arquivo '_large-hero.css' dentro da pasta modules com o seguinte código.

    .large-hero {
        position: relative;
    }

Ainda na pasta styles, crie uma pasta com nome 'base' e dentro dela o arquivo '_global.css' contendo estilos globais da aplicação, por exemplo:

    body {
        background-color: #ececec;
    }

A estrutura de pastas e arquivos em styles ficará conforme ilustrado abaixo.

    ...
    styles/
      base/
        _global.css
      modules/
        _large-hero.css
      styles.css
    ...

Em seguida, faça referência aos arquivos 'modules/_large-hero.css' e 'base/_global.css' de dentro do arquivo 'styles.css'.

    @import 'base/_global.css';
    @import 'modules/_large-hero.css';

Para fazer o import de pacotes npm, referenciados na propriedade 'dependencies' do arquivo package.json, basta informar o nome do pacote. Por exemplo: 

    @import 'normalize.css';

O pacote npm 'normalize.css' realiza um processo de css reset onde são definidos estilos padrões para todos os elementos html de maneira a garantir maior consistência da interface gráfica.

Relevante observar que esta abordagem mantém o código css organizado, facilita o reaproveitamento de código e a criação de arquivos de estilo que combinam módulos relacionados para compor novos componentes de tela e interfaces gráficas.

Também será necessário instalar e configurar o pacote npm 'postcss-import' para que seja feita uma única requisição http para carregar todos os arquivos de estilos.

    npm install postcss-import --save-dev

Em seguida, inclua uma referência para o pacote 'postcss-import' junto com demais plugins no arquivo de configurações webpack conforme exemplo abaixo.

    const path = require('path')

    const postCSSPlugins = [
        require('postcss-import'),
        require('postcss-simple-vars'),
        require('postcss-nested'),
        require('autoprefixer')
    ]

    module.exports = {
        entry: './app/assets/scripts/App.js',
        output: {
            filename: 'bundled.js',
            path: path.resolve(__dirname, 'app')
        },
        mode: 'development',
        watch: true,
        module: {
            rules: [
                {
                    test: /\.css$/i,
                    use: ['style-loader', 'css-loader', {loader: 'postcss-loader', options: {postcssOptions: {plugins: postCSSPlugins}}}]
                }
            ]
        }
    }

O padrão Block Element Modifier (BEM) garante organização e consistência na implementação de classes de estilos para aplicações web.

De maneira resumida, blocos podem ser quaisquer elementos html ou entidades abstratas como models e controllers. Em nosso exemplo, utilizamos o nome 'large-hero' para especificar um bloco.

Um Elemento pode ser qualquer elemento DOM dentro de um bloco. Um elemento não pode ter dependências em outros blocos ou elementos.

Modifiers são usados para alterar aparência, comportamento ou estado, separados do bloco/elemento através de prefixo '--'.

Veja o exemplo disponível na [site ofical](http://getbem.com/naming/) do padrão BEM.

HTML
    <form class="form form--theme-xmas form--simple">
        <input class="form__input" type="text" />
        <input
            class="form__submit form__submit--disabled"
            type="submit" />
    </form>
    
CSS
    .form { }
    .form--theme-xmas { }
    .form--simple { }
    .form__input { }
    .form__submit { }
    .form__submit--disabled { }

No código de nosso exemplo, podemos pensar em um elemento 'large-hero__text-content'.





