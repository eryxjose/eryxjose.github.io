---
layout: post
author: Eryx
title: Deploy aplicação ReactJS rodando no Docker com Integração Contínua
date: 31/12/2020
---

Este texto descreve o fluxo de publicação para uma aplicação Docker com integração contínua utilizando "Travis CI" em um repositório GitHub com deploy para AWS Elastic Beanstalk.

As funcionalidades serão implementadas na branch "feature" e então será feito um merge request para a branch "master". O merge request iniciará um processo de testes que no caso de sucesso fará o merge com a branch master e então a publicação no AWS Elastic Beanstalk. Neste contexto, veremos que o Docker facilita algumas tarefas relacionadas ao fluxo de deploy.

Utilizaremos uma aplicação ReactJS sem alterações no código da aplicação apenas para demonstrar o deploy e a integração contínua, foco deste post. 

Crie a aplicação ReactJS.

    $ npx create-react-app frontend

Uma aplicação ReactJS semelhante a outras aplicações desenvolvidas em ambiente NodejS, possui um arquivo package.json com scripts para executar, testar e publicar respectivamente uma aplicação.

    $ npm run start
    - Inicia um servidor web usado no desenvolvimento.

    $ npm run test
    - Executa os testes associados com o projeto.

    $ npm run build 
    - Cria uma versão da aplicação para produção.

Crie o arquivo Dockerfile.dev na pasta raiz do projeto e copie o código abaixo.

    FROM node:alpine
    WORKDIR '/app'
    COPY package.json .
    RUN npm install
    COPY . .
    CMD ["npm", "run", "start"]

Utilize o parâmetro -f com o comando docker build para especificar o nome do arquivo docker a ser utilizado. Em nosso exemplo Dockerfile.dev.

    $ docker build -f Dockerfile.dev .

Remova a pasta node_modules na pasta do projeto React. O container Docker carregará os módulos node quando for executado.

Execute o comando docker run com o parâmetro -p para especificar o mapeamento entre a porta local e a porta do container.

    $ docker run -it -p 3000:3000 <id-container>

Vamos configurar Docker Volume para fazer com que as alterações no código da aplicação sejam refletidas no container sem a necessidade de fazer um novo build. O parâmetro -v permite fazer o mapeamento entre os volumes. No exemplo abaixo, "-v $(pwd):/app" faz o mapeamento entre a pasta local obtida com o pwd (present working directory) e a pasta /app no container.

    $ docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <id-container>

O mapeamento de volume para os usuários do Windows Home utilizando Docker Toolbox precisa passar uma variável de ambiente com a correção diretamente no comando run.

    -e CHOKIDAR_USEPOLLING=true

Ou criar um arquivo .env na pasta raiz do projeto com a correção.

    CHOKIDAR_USEPOLLING=true

Veja exemplos para diferentes terminais abaixo e lembre de verificar de alterar os caminhos apropriadamente.

    Docker Quickstart (recommended)

    docker run -it -p 3000:3000 -v /app/node_modules -v ${PWD}:/app -e CHOKIDAR_USEPOLLING=true <id-container>

    GitBash

    winpty docker run -it -p 3000:3000 -v /app/node_modules -v "/$(PWD)":/app -e CHOKIDAR_USEPOLLING=true <id-container>

    PowerShell

    docker run -it -p 3000:3000 -v /app/node_modules -v /c/Users/username/frontend:/app -e CHOKIDAR_USEPOLLING=true <id-container>

Execute a aplicação com o mapeamento de volume, acesse com o navegador e faça uma alteração no arquivo /src/App.js para confirmar que as alterações estão sendo replicadas automaticamente.

    $ docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <id-container>

Crie um arquivo docker-compose.yme na pasta raiz do projeto com o código ilustrado a seguir. 

    version: '3'
    services:
        web: 
            build: 
                context: .
                dockerfile: Dockerfile.dev
            ports: 
                - "3000:3000"
            volumes:
                - /app/node_modules
                - .:/app

Utilize o comando docker run para executar os testes definidos para o projeto.

    $ docker run -it <id-imagem> npm run test

Utilize docker exec para rodar os testes via terminal para que as alterações nos testes sejam reconhecidas automaticamente.

    $ docker exec -it <id-container> npm run test

Uma maneira alternativa ao uso do docker exec em um segundo terminal para rodar os testes sem que for feita qualquer alteração na aplicação, aproveitando o mapeamento de volume previamente definido, é criar um segundo serviço no docker-compose.yml conforme exemplo abaixo.

    version: '3'
    services:
    web: 
        build:
            context: .
            dockerfile: Dockerfile.dev
        ports: 
            - "3000:3000"
        volumes:
            - /app/node_modules
            - .:/app
    tests:
        build:
            context: .
            dockerfile: Dockerfile.dev
        volumes:
            - /app/node_modules
            - .:/app
        command: ["npm", "run", "test"]

Execute o docker-compose up com o parâmetro --build, faça alterações nos testes e confirme que os testes executaram novamente.

    $ docker-compose up --build

Observe que utilizando o Docker Compose não será possível obter acesso aos comandos do prompt para executar novamente, filtrar por nome de arquivo, ou pelo nome do teste. Os testes serão executados na inicialização e quando forem feitas alterações somente. Para obter a interação com o prompt de comando, utilize a primeira opção discutida.

Crie o arquivo Dockerfile que será usado para gerar o deploy de produção, na pasta raiz do projeto. Utilize o código a seguir para executar um build em múltiplos passos. Cada passo é iniciado pelo comando FROM e pode ser referenciada utilizando --from=<índice-passo> conforme exemplo a seguir. Esta estratégia possibilita criar e utilizar recursos de múltiplas imagens em conjunto. Em nosso exemplo, node:alpine e nginx. O comando copy na última linha do exemplo, obtém o /app/build resultado do primeiro passo e copia para a pasta padrão utilizada pelo nginx.

    FROM node:alpine
    WORKDIR '/app'
    COPY package.json .
    RUN npm install
    COPY . .
    RUN npm run build

    FROM nginx
    COPY --from=0 /app/build /usr/share/nginx/html

Execute a aplicação com docker run.

    $ docker run -p 8080:80 <id-imagem>

Crie um repositório no GitHub para o projeto e copie o url gerado. Em seguida abra o terminal na pasta raiz do projeto e inicialize o repositório local.

    $ git init

Adicione todos os arquivos ao controle de versão com o comando git add.

    $ git add .

Faça commit.

    $ git commit -m "Primeiro commit"

Utilize o git remote para víncular o repositório criado no GitHub com a url copiada anteriormente.

    $ git remote add origin <url-respositorio>

Faça o push para branch master.

    $ git push origin master

Pode ser necessário informar seu usuário e senha do GitHub.

Em seguida, veremos como configurar o Travis CI para receber notificações sobre alterações em nosso repositório GitHub e realizar ações de teste e deploy.

Acesse https://travis-ci.org/ e faça Sign In. Sua lista de repositórios aparece no painel lateral esquerdo, onde você encontra um botão para adicionar novos repositórios. Ao clicar no botão adicionar, śerá exibida sua lista de repositórios no GitHub para você selecionar. 

Crie o arquivo .travis.yml na pasta raiz do projeto e copie o código abaixo.

    language: generic 
    sudo: required
    services:
        - docker
    before_install:
        - docker run -e CI=true <user-docker>/docker-react npm run test
    script:
        - docker run -e CI=true <user-docker>/docker-react npm run test

[Veja mais sobre CI=true](https://facebook.github.io/create-react-app/docs/running-tests#linux-macos-bash)

[Veja mais sobre variáveis de ambiente](https://docs.docker.com/engine/reference/run/#env-environment-variables)

Adicione as alterações ao controle de versão.

    $ git add .

Faça commit e push das alterações.

    $ git commit -m "Adicionado arquivo .travis.yml
    $ git push origin master

A página do seu repositório em travis-ci.org mostrará o resultado dos testes. Também é exibido um log de execução e algumas ações possíveis, como por exemplo, visualizar o commit, comparar as alterações, acessar a branch master no github, informações do tempo gasto, botão para reiniciar o build, entre outros. E você também pode receber um email com o resultado.

Em seguida, acesse seu Console de Gerenciamento AWS e pesquise e acesse Elastic Beanstalk. Clique no botão "Create Application" para acessar o formulário "Criar um aplicativo Web". Entre com um nome, por exemplo, docker-react. Selecione a plataforma Node.js, e no campo "Ramificações da plataforma" selecione "Node.js running on 64bit Amazon Linux" para fazer com que o arquivo Dockerfile seja usado ao invés do docker-compose.yml. Clique no botão "Criar Aplicativo". Depois de concluído, acesse o link Aplicativos para visualizar informações, incluindo link para exibir sua aplicação no navegador.

Inclua a seção deploy no arquivo .travis.yml conforme exemplo. Você pode visualizar a região (region) onde está hospedada sua aplicação no url gerado no passo anterior pelo serviço Elastic Beanstalk. No mesmo local, você também visualiza a coluna Nome do Ambiente cujo valor deve ser informado na propriedade env. Você encontrará o nome do bucket criado para sua aplicação na lista de Buckets disponível na tela de gerenciamento do serviço S3. Pesquise por S3 e em seguida localize o nome que contém a região de sua aplicação. Utilize o nome da aplicação na propriedade bucket_path. A propriedade branch define quando o deploy será feito.

    deploy:
        provider: elasticbeanstalk
        region: "us-west-2"
        app: "docker-react"
        env: "DockerReact-env"
        bucket_name: "elasticbeanstalk-us-west-2-670490603314"
        bucket_path: "docker-react"
        on:
            branch: master

A tela Identity and Access Management (IAM), mostra as chaves de API cadastradas. Clique em Adicionar Usuário e entre com um nome de usuário por exemplo docker-react-travis-ci. Marque a opção "Acesso Programático" e clique em "Próximo: Permissões". Na tela "Definir Permissões", clique em "Anexar políticas existentes de forma direta", pesquise e selecione AWSElasticBeanstalkFullAccess. Clique no botão "Próximo" para tags, confirme as configurações e então clique no botão "Criar Usuário". Será exibida uma mensagem de sucesso e o novo usuário será exibido em uma tabela de usuários. Exiba e copie a chave da coluna "Chave de acesso secreta". Em seguida acesse a tela do seu repositório travis-ci, clique em "More Options" e então "Settings". Na lista de variáveis de ambiente adicione as chaves AWS_ACCESS_KEY e AWS_SECRET_KEY com seus respectivos valores. Por fim, inclua as chaves access_key_id e access_key_secret no arquivo .travis.yml conforme exemplo a seguir.

    access_key_id: $AWS_ACCESS_KEY
    secret_access_key:
        secure: "$AWS_SECRET_KEY"

Faça commit e push das alterações e então acesse o Dashboard Travis CI para visualizar o andamento do processo.

Também será necessário adicionar a propriedade EXPOSE ilustrada abaixo no arquivo Dockerfile, logo abaixo de FROM nginx que define o segundo passo no processo multi passos de deploy. O serviço AWS Elastic Beanstalk utiliza a propriedade EXPOSE para mapear a porta da aplicação no container Docker.

    ...
    FROM nginx
    EXPOSE 80
    COPY --from=0 /app/build /usr/share/nginx/html
    ...






