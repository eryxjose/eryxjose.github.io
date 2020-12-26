---
layout: post
author: Eryx
title: Instalando Docker no Linux Ubuntu
date: 26/12/2020
---

Este texto descreve a instalação do Docker no Linux Ubuntu utilizando o terminal.

Primeiro é necessário atualizar o índice de pacotes disponíveis e então instalar pacotes para permitir o uso de HTTPS para apt.

    $ sudo apt-get update

    $ sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common

Em seguida, adicione a chave GPG para Docker.

    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

Adicione o repositório Docker.

    $ sudo add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"

Atualize o índice de pacotes e instale o Engine do Docker.

    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io

No linux é necessário instalar o Docker Compose manualmente.

    sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

Atribua permissão de execução para o docker-compose.

    $ sudo chmod +x /usr/local/bin/docker-compose

Verifique a instalação.

    $ docker-compose --version

Faça login sua conta Dockerhub. Você pode criar sua conta de usuário em https://hub.docker.com/signup, caso ainda não tenha feito isso.

    $ docker login

Verifique a instalação.

    $ sudo docker run hello-world

Utilize docker exec para executar comandos no container. O parâmetro -it habilita o uso do terminal para o referido container. No exemplo abaixo, o comando redis-cli está sendo executado no container com id <id-container>.

    $ docker exec -it <id-container> redis-cli

Execute sh no container utilizando docker exec para obter acesso ao prompt (shell).

    $ docker exec -it <id-container> sh

Você pode ter acesso ao prompt de comando sh de um container.

    $ docker run -it busybox sh

Os links abaixo, explicam como executar o docker sem sudo e configurar para executar na inicialização do sistema.

[Executar o comando docker sem sudo](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)

[Executar na inicialização](https://docs.docker.com/install/linux/linux-postinstall/#configure-docker-to-start-on-boot)

