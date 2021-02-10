---
layout: post
author: Eryx
title: Criando aplicação Node/Express/React/MongoDB para enviar pesquisas por email
date: 04/02/2021
---

Este tutorial descreve a criação de uma aplicação web para enviar pesquisas por email.

## Arquitetura

ReactJS Web Application
Express/NodeJS API
MongoDB

## Express/Node API

Crie a estrutura de pastas e arquivos iniciais relacionados abaixo.

    /MailSurvey/server
      - index.js

Abra a pasta 'server' no terminal e execute o comando 'npm i --save express' para instalar a lib.

Inclua o código abaixo no arquivo index.js.

    const express = require('express');
    const app = express();

    app.get('/', (req, res) => {
        res.send({ hi: 'there'});
    });

    app.listen(5000);

Checklist para deploy em Heroku:

* Dynamic Port Binding - configurar na aplicação a porta configurada pelo servidor heroku
* Specify Node Environment - informar ao servidor heroku a versão do node a ser usada
* Specify Start Script - definir o comando a ser executado para iniciar o servidor
* Create .gitignore - não enviar dependências porque serão geradas no servidor

Checklist Primeiro Deploy

* Crie uma conta de usuário
* Faça commit do seu código para o git
* Instale a CLI Heroku e Create App
* Faça deploy da App com o Git
* Heroku faz deploy do projeto

Checklist Deploys Seguintes

* Faça commit do código para o Git
* Faça deploy com o Git

## Dynamic Port Binding

Heroku disponibiliza uma porta específica para executar sua aplicação. 

Inclua a linha a seguir no arquivo index.js. A linha abaixo, obtém a porta definida na varável de ambiente 'PORT'. Esta informação será preenchida automaticamente pelo servidor Heroku quando a aplicação for iniciada. Caso a variável não esteja definida, o valor '5000' será utilizado.

    const PORT = process.env.PORT || 5000;

## Specify Node Environment

Informe a versão do NodeJS utilizada em sua aplicação. No arquivo 'package.json', inclua a propriedade 'engines' conforme exemplo a seguir.
    
    ...
    "engines": {
        "node": "15.0.0",
        "npm": "7.4.0"
    },
    ...

## Specify Start Script

Também no arquivo 'package.json', inclua o script 'start'.

    ...
    "start": "node index.js"
    ...

## Create .gitignore

Não faça commit das dependências node. Crie o .gitignore e inclua a linha abaixo e salve o arquivo.

    node_modules

## Inicialize o Git

Com o terminal aberto na pasta da aplicação, entre com o comando 'git init' para inicializar o controle de versão.

Adicione todos os arquivos do projeto para o controle de versão com o comando 'git add .'.

Em seguida faça commit dos arquivos utilizando o comando 'git commit' conforme exemplo a seguir.

    $ git commit -m "First commit"

## Instale o CLI Heroku

O CLI será utilizado para criar um novo projeto Heroku. No Linux Ubuntu, utilize o comando abaixo para fazer a instalação.

    $ sudo snap install --classic heroku

O url abaixo mostra o processo de instalação para diferentes sistemas operacionais.

    https://devcenter.heroku.com/articles/heroku-cli

Entre com o comando 'heroku -v' no terminal para verificar que a instalação foi bem sucedida.

## Login Heroku

Faça login na sua conta Heroku utilizando o comando 'heroku login' no terminal. Uma janela do navegador será exibida com um botão para clicar e fazer login. O terminal mostrará uma mensagem de sucesso ao concluir.

## Crie a aplicação Heroku

Entre com o comando 'heroku create' para inicializar uma aplicação Heroku na pasta em sua aplicação. Este comando retorna o URL para visualizar sua aplicação na web seguido do url para vincular o repositório ao projeto. Por exemplo:

    $ heroku create
    Creating app... done, ⬢ damp-fortress-33176
    https://damp-fortress-33176.herokuapp.com/ | https://git.heroku.com/damp-fortress-33176.git

## Deploy da aplicação utilizando Git

Utilize o comando abaixo para vincular sua aplicação ao repositório.

    $ git remote add heroku https://git.heroku.com/damp-fortress-33176.git 
    $ git push heroku master

O comando 'git push' envia os arquivos para o repositório remoto e inicia uma série de procedimentos de deploy realizados pelo servidor Herolu e listados no terminal.

Utilize o comando 'heroku open' ou o url da aplicação discutido anteriormente para testar o acesso no navegador.

Caso sejam exibidas mensagens de erro, utilize o comando 'heroku logs' para visualizar o log gerado pelo servidor Heroku e obter mais detalhes sobre o problema.

Para um novo deploy depois de fazer alterações, adicione os arquivos alterados ao controle de versão 'git add .', e faça commit e push novamente conforme exemplo a seguir.

    $ git commit -m "Alteração teste"
    $ git push heroku master

## Google Auth Process

O fluxo de autenticação inicia em um endpoint de login na sua aplicação, por exemplo 'localhost:5000/auth/google'. Este endpoint redireciona a requisição para o url da api de autenticação do Google junto com um identificador da sua aplicação por exemplo 'google.com/auth?appId=123'. A api do Google então solicita o consentimento do usuário para validar o acesso, e retorna para um url de callback da sua aplicação junto com o código identificador da aplicação que obtém o código e envia uma nova requisição para a api com o código retornado anteriormente. A api do Google verifica o código e retorna as informações do usuário caso a verificação seja bem sucedida. A aplicação recebe as informações do usuário e cria um novo registro na base de dados. Depois de registrar o usuário, a aplicação registra um Cookie para identificar este usuário e redireciona a requisição para exibir sua página pessoal na aplicação. As próximas requisições, verificam a existência do cookie de autenticação para validar o usuário.

## PassportJS

A biblioteca PassportJS contém dois módulos. O módulo principal habilita o uso da lib na aplicação e o módulo 'passport strategy' é específico para o api sendo utilizada, em nosso exemplo, Google Auth API.

Veja mais detalhes em [Passport](http://www.passportjs.org/). 

Instale o módulo principal e o módulo para autenticação.

    $ npm i --save passport passport-google-oauth20

## Google Project Setup

Acesse a página Gerenciar Recursos no console do Google Cloud Platform. Clique em 'Criar Projeto' e entre com um nome para o projeto e clique no botão 'Criar'. Depois de criado, clique para acessar o projeto no link exibido junto com a mensagem de sucesso exibida ao final do processo. Aponte para o menu 'API e Serviços' e selecione 'Tela de Consentimento OAuth'. No campo 'User Type' selecione 'Externo' na tela exibida e clique no botão 'Criar'. Preencha o campo 'Nome do app' e no final do formulário o campo 'Email' em 'Dados Contato Desenvolvedor'. Então clique no botão 'Salvar e Continuar'. Em seguida clique em 'Credenciais' no painel lateral e então 'Criar Credenciais' e 'ID do cliente OAuth' na parte de cima da tela. Será exibido o formulário 'Criar ID do cliente OAuth'. Adicione o url 'http://localhost:5000' ao campo 'Origens JavaScript Autorizadas', e 'http://localhost:5000/auth/google/callback' ao campo 'URIs de redirecionamento autorizados'. E então clique no botão 'Criar'. Uma tela de confirmação será mostrada, contendo o ID e uma chave secreta do cliente criado. Guarde as informações ID e chave de segurança em local seguro.

Veja mais detalhes em [Como criar e gerenciar projetos](https://cloud.google.com/resource-manager/docs/creating-managing-projects#console)

## Protegendo chaves de segurança

Crie uma pasta com o nome 'config' na raiz do projeto e dentro dela o arquivo 'keys.js' com o código ilustrado abaixo.

    module.exports = {
        googleClientID: '<sua-client-id>',
        googleClientSecret: '<sua-client-secret>',
        callbackURL: '/auth/google/callback'
    };

Adicione o nome do arquivo 'keys.js' ao arquivo .gitignore para que este arquivo não seja enviado para o repositório.

## Utilizando keys.js

Altere o arquivo index.js para fazer uso das informações registradas no arquivo keys.js.

    const express = require('express');
    const passport = require('passport');
    const GoogleStrategy = require('passport-google-oauth20').Strategy;
    const keys = require('./config/keys');
    const app = express();

    passport.use(new GoogleStrategy(
        {
            clientID: keys.googleClientID,
            clientSecret: keys.googleClientSecret,
            callbackURL: keys.callbackURL
        }, (accessToken) => { // Error callback
            console.log(accessToken);
        }
    ));

    const PORT = process.env.PORT || 5000;

    app.listen(PORT);

GoogleStrategy recebe uma function como segundo argumento que será usada posteriormente para tratar o código de autenticação retornado pela api do google auth.

Inclua uma rota para iniciar o processo de autenticação Passport logo abaixo das configurações de 'passport.use' no arquivo index.js. O parâmetro 'google' passado para o método 'passport.authenticate' identifica internamente o GoogleStrategy configurado anteriormente. A propriedade 'scope' informa o tipo de acesso que a aplicação solicita consentimento do usuário.

    app.get('/auth/google', passport.authenticate('google', {
        scope: ['profile', 'email']
    }));

Teste a aplicação acessando 'localhost:5000/auth/google' e verifique que a autenticação está sendo feita. Uma mensagem de página de callback não encontrada será exibida proque não definimos uma rota de callback até o momento.

Adicione uma nova rota para o callback configurado no console do Google Auth API anteriormente. O método authenticate do passport desta vez verifica a existência do parâmetro 'code' retornado após o sucesso da autenticação anterior.

    app.get('/auth/google/callback', passport.authenticate('google'));

Execute novamente a aplicação, acesse o url de autenticação, selecione uma conta de usuário caso seja solicitado, e verifique no console o token retornado para o callback. Anteriormente, configuramos o método de callback da url de autenticação para mostrar no console o accessToken retornado pela api.

## Access e Refresh Tokens

Altere a função de callback do método de configuração passport conforme exemplo abaixo. O código foi alterado para mostrar as informações 'accessToken', 'refreshToken', e 'profile' retornadas pela api. 

    passport.use(new GoogleStrategy(
        {
            clientID: keys.googleClientID,
            clientSecret: keys.googleClientSecret,
            callbackURL: keys.callbackURL
        }, (accessToken, refreshToken, profile, done) => { // Error callback
            console.log(accessToken);
            console.log(refreshToken);
            console.log(profile);
        }
    ));

O accesstoken retornado pela api pode ser armazenado e utilizado em futuras requisições.

## Nodemon

Nodemon é um módulo que reinicia a aplicação node sempre que houver uma alteração no código. Utilize o comando 'npm i --save nodemon' para instalar o Nodemon.

Adicione o script 'dev' ilustrado abaixo ao arquivo package.json.

    ...
    "scripts": {
        "start": "node index.js",
        "dev": "nodemon index.js"
    },
    ...

Utiliza o comando 'npm run dev' no terminal para executar o respectivo script. Faça alguma alteração no código e observe as mensagens mostradas no terminal indicando a reinicialização da aplicação em razão de mudanças.

## Refatoramento da index.js

É recomendado criar uma pasta 'routes' para agrupar e organizar todas as rotas da aplicação. E uma pasta 'services' para organizar helpers e lógica de negócios.

Crie uma pasta 'routes' e dentro dela o arquivo 'authRoutes.js' contendo o código abaixo. 

    const passport = require('passport');

    module.exports = app => {

        app.get('/auth/google', passport.authenticate('google', {
            scope: ['profile', 'email']
        }));
        
        app.get('/auth/google/callback', passport.authenticate('google'));
        
    };

O módulo acima, será importado e inicializado em index.js conforme exemplo abaixo. Observe que authRoutes é referênciado e depois executado após express() ser instanciado na constante app. Carregando então todas as rotas definidas em authRoutes.js.

    const express = require('express');
    require('./services/passport');
    const authRoutes = require('./routes/authRoutes');
    const app = express();
    authRoutes(app);
    const PORT = process.env.PORT || 5000;
    app.listen(PORT);

Observe também que a referência para 'services/passport' não precisa ser atribuída a nenhuma variável. Apenas referênciada para que os serviços definidos em passport.js sejam inicializados.

Em seguida refatore o código de index.js para eliminar a variável 'authRoutes' e passar o parâmetro 'app' junto com o comando "require('./routes/authRoutes')".

    const express = require('express');
    require('./services/passport');
    const app = express();
    require('./routes/authRoutes')(app);
    const PORT = process.env.PORT || 5000;
    app.listen(PORT);

## Autenticação na web

Cada requisição http é única. Isso significa que não há compartilhamento de dados entre as diferentes requisições. A estratégia usada para manter uma sessão ativa e possibilitar o compartilhamento de informações entre as diferentes requisições, é reenviar dados a cada requisição que podem conter algum tipo de identificação. 

Cookies são pequenos arquivos de texto gerenciados pelo navegador. O retorno de uma requisição pode conter um cabeçalho especial do tipo 'Set-Cookie' contendo um valor que o navegador identifica e passa a reenviar a cada requisição posterior. O servidor então pode verificar a presença do Cookie na requisição para obter os dados persistidos.

## MongoDB

O banco de dados MongoDB será utilizado nesta aplicação para armazenar dados de usuários, incluindo o identificador obtido através do processo de autenticação Google Auth comentado anteriormente. A lib mongoose.js será utilizada para facilitar a interação da API com a base de dados MongoDB.

Mongo armazena as informações em coleções (ex.: users, posts, payments) que por sua vez armazenam registros (Records). Cada registro é um objeto json contendo pares de chaves e seus respectivos valores. Por exemplo:

    {
        id: 1,
        name: 'eryx',
        height: 181
    }

MongoDB não utiliza Schemas e permite que os registros tenham campos diferentes. 

## Mongoose

Mongoose utiliza 'Model Class' para representar as coleções de uma base MongoDB e contém os métodos para realizar as operações de atualização na base de dados. Mongoose também fornece 'Model Instance' que tem acesso a cada registro da coleção.

Instale a lib mongoose com o parâmetro '--save' como dependência para execução.

    $ npm install --save mongoose

Modifique o código da 'index.js' conforme exemplo a seguir. Observe as referências para lib mongoose e para o arquivo './config/keys.js' contendo as informações sensitivas do app. E em seguida, o comando 'mongoose.connect' recebendo o url de conexão com a base mongodb como parâmetro.

    const express = require('express');
    const mongoose = require('mongoose');
    const keys = require('./config/keys');
    require('./services/passport'); // referência sem retorno para o carregamento das configurações
    mongoose.connect(keys.mongoURI);
    const app = express();
    require('./routes/authRoutes')(app);
    const PORT = process.env.PORT || 5000;
    app.listen(PORT);


Crie uma pasta 'models' na rais do projeto, e dentro dela, o arquivo 'User.js'. Este arquivo será utilizado para criar uma classe de modelo mongoose para representar uma coleção mongodb. As propriedades e respectivos tipos de dados das coleções são definidas utilizando a propriedade 'Schema'. O comando 'mongoose.model' registra o Schema como uma classe de modelo de dados que representa a coleção (MongoDB Collection) Users. 

    const mongoose = require('mongoose');
    const { Schema } = mongoose;

    const userSchema = new Schema({
        googleId: String
    });

    mongoose.model('users', userSchema);

Em seguida, adicione o 'require' para 'User' em index.js para que seja carregado junto com a inicialização da aplicação mas antes de carregar o módulo './services/passport'.

    ...
    require('./models/User');
    require('./services/passport');
    ...

Abra o arquivo './services/passport.js', adicione uma referência para lib 'mongoose' e em seguida carregue o model 'users' na variável 'User'. Lembre-se que atribuímos o nome 'users' para 'userSchema' em 'User.js'. 'User' no código ilustrado a seguir, representa uma 'Model Class' utilizada para interagir com a coleção 'users' na base de dados MongoDB.

    ...
    const mongoose = require('mongoose');
    const User = mongoose.model('users');
    ...

Modifique o método 'passport.use' do mesmo arquivo 'passport.js' para criar uma instância de 'User' com o id obtido do Google Auth. O método '.save()' grava o registro na base. A Collection 'users' será criada automaticamente caso ainda não exista. No código abaixo, verificamos a existência de um usuário utilizando 'User.findOne' passando um objeto com o 'profile.id' do usuário autenticado pelo Google Auth. No caso de já existir um usuário com o mesmo 'googleId', indicamos o final da requisição utilizando o método 'done(null, existingUser)'. Caso seja um novo usuário, e a operação 'save()' não retorne erro, indicamos o sucesso chamando o método 'done(null, user)'. Caso a gravação tenha sido bem sucedida, o callback 'user' contém os dados do novo usuário criado.

    const passport = require('passport');
    const GoogleStrategy = require('passport-google-oauth20').Strategy;
    const mongoose = require('mongoose');
    const keys = require('../config/keys');
    const User = mongoose.model('users');

    passport.use(new GoogleStrategy(
        {
            clientID: keys.googleClientID,
            clientSecret: keys.googleClientSecret,
            callbackURL: keys.callbackURL
        }, 
        (accessToken, refreshToken, profile, done) => {
            User.findOne({ googleId: profile.id }).then( existingUser => {
                if (existingUser) {
                    done(null, existingUser);
                } else {
                    new User({ googleId: profile.id })
                        .save()
                        .then(user => done(null, user));
                }
            });
        }
    ));

O próximo passo é implementar a serialização/desserialização do objeto 'User' e configurar 'express' e 'passport' para utilizar Cookies. A serialização é feita com o método 'passport.serializeUser'. Antes de 'passport.use', inclua o código a seguir.

    passport.serializeUser((user, done) => {
        done(null, user.id);
    })

No exemplo acima, 'user' é o parâmetro de callback que retorna o registro inserido e 'user.id' contém o respectivo identificador único na base MongoDB.

Em seguida, adicione 'passport.deserializeUser' e utilize o 'id' para encontrar o registro na coleção 'User'. 

    passport.deserializeUser((id, done) => {
        User.findById(id).then(user => { 
            done(null, user);
        });
    });

'done' é um padrão JavaScript para chamadas assíncronas utilizado pela lib 'passport' para informar a conclusão de requisições. Este padrão adota uma abordagem 'error first' onde o primeiro parâmetro é usado para exceções e os demais parâmetros são objetos de dados.

Instale a lib 'cookie-session' para habilitar o uso de cookies para autenticação do usuário em nossa aplicação 'express'.

    $ npm install --save cookie-session

Para configurar o uso de Cookies, adicione referências para as libs 'cookie-session' e 'passport' no arquivo 'index.js' conforme exemplo a seguir.

    ...
    const cookieSession = require('cookie-session');
    const passport = require('passport');
    ...

Em seguida, 'app.use' recebe 'cookieSession' com um objeto de configuração contendo 'maxAge' (expiração dos Cookies) e 'keys' (utilizado na encriptação).

    ...
    app.use(cookieSession({
        maxAge: 30 * 24 * 60 * 60 * 1000,
        keys: [keys.cookieKey]
    }));
    ...

Também é necessário fazer configurações para a lib 'passport'.

    ...
    app.use(passport.initialize());
    app.use(passport.session());
    ...

Isso conclui todas as configurações para o fluxo de autenticação utilizando as libs 'passport' e 'CookieSession' em uma aplicação 'express'. De maneira geral quando uma requisição for feita para aplicação, 'CookieSession' verifica a existência de Cookies e decripta as informações caso existam, 'passport' obtém o id do usuário dos dados extraídos, a função 'deserializeUser' então instancia o objeto User e esta instância é então vinculada ao objeto request como 'req.user'.

Para testar a autenticação vamos criar uma nova rota que retorne o objeto 'req.user'. No arquivo 'authRoutes.js' inclua a rota abaixo.

    app.get('/api/current_user', (req, res) => {
        res.send(req.user);
    });

Crie uma nova rota em 'authRoutes.js' para ação de logout. Passport automaticamente adiciona uma função 'logout()' no objeto 'req' que pode ser executada conforme exemplo a seguir.

    app.get('/api/logout', (req, res) => {
        req.logout();
        res.send(req.user); // req.user enviado para comprovar o signout
    });

Acesse o url 'http://localhost:5000/api/logout' para confirmar que nada será exibido porque 'req.user' está vazio.
