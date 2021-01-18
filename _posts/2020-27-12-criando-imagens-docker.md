---
layout: post
author: Eryx
title: Criando imagens Docker
date: 27/12/2020
---

O Dockerfile é composto de uma imagem base, dependências ou programas adicionais, e comando para ser executado na inicialização. É usado para criar imagens que serão usadas para montar e executar containers.

O comando "docker build" é usado para gerar a imagem a partir do arquivo Dockerfile.

    $ docker build .

O atributo -t permite definir um identificador para a imagem no padrão  <user-docker>/<tag-imagem>:<versão>. Veja exemplo a seguir.

    $ docker build -t eryxjose/redis:latest .

Você pode utilizar a tag ao invés do id para executar o container.

    $ docker run eryxjose/redis

Ou o Id quando não houver uma tag definida. Por exemplo:

    $ docker run 7f908679c0d5

As imagens podem ser geradas através do Dockerfile ou de containers. Para gerar imagens a partir de um container em execução, é necessário ter acesso ao prompt sh do referido container, fazer as configurações desejadas e então utilizar o comando "docker commit". 

    $ docker run -it apine sh

Assim que o prompt sh estiver disponível, adicione o redis utilizando o gerenciador de pacotes (apk).

    $ apk add --update redis

Finalize o container e utilize o comando docker commit para gerar uma imagem a partir do container especificado.

    $ docker commit -c 'CMD ["redis-server"]' <id-container>

Relevante observar que você pode utilizar apenas os primeiros números do identificador da imagem gerada.


