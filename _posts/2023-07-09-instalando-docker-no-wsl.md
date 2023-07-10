---
layout: post
author: Eryx
title: Instalando Docker no WSL Ubuntu
date: 09/07/2023
---

Para instalar o Docker no Windows Subsystem for Linux (WSL), siga estas etapas:

Abra o terminal do WSL no seu sistema.

Certifique-se de que você está usando uma distribuição do WSL compatível, como o Ubuntu. Se você estiver usando outra distribuição, pode ser necessário fazer ajustes nas etapas de instalação específicas para essa distribuição.

Execute os seguintes comandos no terminal do WSL, um de cada vez, para atualizar os pacotes e instalar as dependências necessárias:

    sudo apt update
    sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

Adicione a chave GPG oficial do Docker:

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

Adicione o repositório oficial do Docker ao seu sistema:

    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

Atualize novamente os pacotes do sistema:

    sudo apt update

Instale o Docker Engine:

    sudo apt install -y docker-ce docker-ce-cli containerd.io

Após a instalação, verifique se o Docker está em execução:

    sudo systemctl status docker

Se o status mostrar que o Docker está em execução, a instalação foi concluída com sucesso.

Agora você deve ter o Docker instalado e em execução no seu WSL. Lembre-se de que você pode precisar reiniciar o terminal do WSL para que as alterações tenham efeito.

