---
layout: post
author: Eryx
title: Criando imagens Docker
date: 27/12/2020
---

O Dockerfile é composto de uma imagem base, dependências ou programas adicionais, e comando para executar na inicialização. É usado para criar imagens que serão então utilizadas para criar containers. 

Utilize o comando docker build para gerar a imagem.

    $ docker build .

O atributo -t permite definir um identificador para a imagem no padrão  <user-docker>/<tag-imagem>:<versão>. Veja exemplo a seguir.

    $ docker build -t eryxjose/redis:latest

Então você pode utilizar a tag ao invés do id para executar o container.

    $ docker run eryxjose/redis

Utilize o id da imagem caso não seja definido uma tag.

    $ docker run 7f908679c0d5

As imagens podem ser geradas através do Dockerfile e também podem ser geradas de containers. Execute o prompt de comando sh no container.

    $ docker run -it apine sh

Assim que o prompt sh estiver disponível, adicione o redis utilizando o gerenciador de pacotes (apk).

    $ apk add --update redis

Finalize o container e utilize o comando docker commit para gerar uma imagem a partir do container especificado. O comado abaixo, gera um identificador da nova imagem criada. 

    $ docker commit -c 'CMD ["redis-server"]' <id-container>

Relevante observar que você pode utilizar apenas os primeiros números do identificador da imagem gerada.


