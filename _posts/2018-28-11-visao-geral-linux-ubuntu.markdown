---
layout: post
author: Eryx
title: Visão Geral do Linux Ubuntu
date: 28/11/2018
---

Ubuntu surgiu em meados de 2000 e se tornou uma das distribuições mais utilizadas do Linux. A história do Linux começa com Richard Stallman na época em que trabalhava para o MIT, iniciando o projeto GNU como uma iniciativa para produzir um sistema operacional totalmente gratuito com base no sistema operacional Unix para as pessoas utilizarem.

No final dos anos 90 havia muitos softwares GNU produzidos mas o Kernel ainda não estava concluído. Então Linus Trovalds criou o kernel Linux e utilizou diversos softwares GNU existentes para lançar o novo sistema operacional.

Em seguida, a fundação de Free Software Foundation (FSF) patrocina o lançamento da versão GNU/Linux gratuitamente para o público. O Debian cresceu para se tornar a comunidade atual.

O Ubuntu utiliza por padrão o Unity (desktop environment) como interface gráfica para o sistema operacional. 

Kubuntu (uma distribuição com base Linux) utiliza o desktop environment KDE Plasma.

O Linux Mint desenvolveu uma interface gráfica própria denominada Cinnamon (linuxmint.com).

Elementary OS é outro exemplo de interface gráfica Linux. 

# Instalação do Ubuntu

Quando você escolher instalar o Ubuntu no computador, será exibido uma tela para selecionar a unidade de disco para instalação. A opção marcada por padrão (Erase) apaga todo conteúdo do disco rígido selecionado e instala uma versão completa do Linux. A opção "Something else" permite redimensionar partições existentes no disco para instalação. 

Caso não haja uma tabela de partição definida, clique para criar uma nova partição. Depois de criada, será exibido a quantidade de espaço do disco. Em seguida, crie uma nova partição, altere o tamanho da partição para por exemplo 5 GB e selecione "Swap area" na lista "Use as". Isto cria um tipo de memória auxiliar no disco.

Depois de criar a unidade de Swap, selecione o espaço livre listado e clique para criar uma nova partição, selecione "Ext 4 journaling file system" como tipo de sistema de arquivos.

Depois de selecionar o tipo de sistema de arquivo, podemos definir o ponto de montagem (Mount point) que determina se esta partição será utilizada para instalação completa do sistema (opção /) ou uma partição específica para arquivos de usuário (/home por exemplo.)

Depois de prosseguir com a instalação, será exibida a tela para escolha do time zone, layout de teclado, e informações de login.

GTK Theme controla como os elementos visuais aparecem no Linux Ubuntu quando utilizando Unity. Acesse gonome-look.org para obter outros temas.

Para instalar temas e ícones é necessário instalar unity-tweak-tool.

    $ sudo apt-get install unity-tweak-tool

# Comandos comuns para desktop

Utilize o comando ls com o parâmetro -l para exibir o conteúdo de uma pasta e respectivos detalhes incluindo permissões, usuário, grupo, tamanho e data de criação. As permissões são agrupadas em 3 colunas. A primeira mostra as permissões para o usuário, a segunda para o grupo e a terceira para o perfil público. As permissões são representadas por letras: 

r - permissão de leitura
w - permissão de escrita
d - usado em diretórios
x - permissão para executar

Utilize o comando chown para alterar usuário e grupo vínculados ao arquivo. O comando abaixo mostra como alterar a permissão do arquivo file.txt.

    $ sudo chown root:nick file.txt

Utilize o comando chmod para alterar as permissões de arquivos definidas através dos números a seguir.

6 - permissão de leitura e escrita
4 - permissão de leitura
7 - usado apenas para pastas

Veja o exemplo abaixo:

    $ sudo chmod 664 file.txt

O comando acima aplica permissão de escrita para usuário e grupo e de leitura para o perfil público. Caso queira garantir que somente seu usuário tem permissão de escrita, então o comando pode ser.

    $ sudo chmod 644 file.txt

Você pode criar um arquivo text em uma pasta utilizando o aplicativo "nano".

    $ sudo nano ./diretorio/file.txt

Para mudar o proprietário de todos os arquivos de uma pasta, utilize o parâmetro -R (maiúsculo) por exemplo:

    $ sudo chown -R usuario:grupo ./diretorio

Sem o parâmetro -R será alterado o proprietário da pasta.

Utilize as teclas de atalho abaixo para abrir o terminal.

    Ctrl + Alt + t 

# Serviços

Um serviço (service) é um tipo especial de processo no Linux. Utilize o utilitário service junto com os parâmetros start e stop para iniciar e parar respectivamente serviços.

    $ sudo service elasticsearch start

    $ sudo service elasticsearch stop

# Finalização de processos

Para finalizar todos os processos utilize o comando killall.

    $ sudo killall nomedoprocesso

# Instalação e configuração Apache2

Primeiro atualize o sistema utilizando apt-get ilustrado a seguir.

    $ sudo apt-get clean && sudo pat-get update && sudo apt-get upgrade -y && sudo apt-get dist-upgrade -y

Caso seja uma nova instalação, pode ser necessário reiniciar o sitema. Utilize o comando reboot.

    $ reboot

Para instalar o apache2 utilize o comando abaixo.

    $ sudo apt-get install apache2

Em seguida, abra o arquivo /etc/apache2/apache2.conf utilizando o editor de sua preferência, por exemplo:

    $ sudo nano /etc/apache2/apache2.conf 

No final do arquivo inclua a linha a seguir.

    ServerName nome_do_seu_servidor_ou_ip

No editor de textos Nano, salve o arquivo utilizando Ctrl + o e feche o arquivo utilizando Ctrl + x.

Utilize o comando abaixo para testar as configurações aplicadas.

    $ sudo apache2ctl configtest

Se tudo estiver certo, será exibida a mensagem: Syntax Ok.

Em seguida reinicie o serviço apache.

    $ sudo systemctl restart apache2

Configurações de segurança 

Verifique o proprietário atual e as permissões aplicadas a pasta /var/www.

    $ ls -l /var/www

Modifique o proprietário da pasta com o comando chown.

    $ sudo chown -R nomeusuario:nomegrupo /var/www

Em seguida, é necessário alterar as propriedades APACHE_RUN_USER e APACHE_RUN_GROUP do arquivo envvars na pasta /etc/apache2 para o usuário atual. Utilize o editor de texto nano para alterar o arquivo.

    $ sudo nano /etc/apache2/envvars

Para que as configurações tenham efeito é necessário reiniciar o serviço apache2.

    $ sudo systemctl restart apache2

Você pode testar o funcionamento do servidor web acessando o url http://localhost do seu navegador ou através do comando curl. Por exemplo:

    $ curl http://localhost

Testando instalação PHP

Para testar o funcionamento do PHP, navegue para a pasta /var/www/html, apague o arquivo index.html e crie um arquivo index.php com o utilitário nano e adicione o comando phpinfo() para que esta página mostre as informações de configuração do php. 

    $ nano index.php

Código do arquivo index.php:

    <?php
    phpinfo();
    ?>


# Instalação Firefox por linha de comando

O navegador Firefox está disponível para instalação via linha de comando depois de fazer um update em apt-get.

    $ sudo apt-get update && sudo apt-get upgrade

    $ sudo apt-get install firefox


# Instalação de programas .deb por linha de comando

A instalação de arquivos .deb é feita com o comando dpkg (debian package manager).

    $ sudo dpkg -i ./google-chrome-stable_current_amd64.deb

# Instalação e atualização Node.js

Para obter a versão mais atual do Node.js no Ubuntu, é necessário primeiro adicionar o arquivo de pacotes pessoais (PPA) para o repositório nodesource.com e em seguida executar o comando de instalação. 

    $ cd ~
    $ curl -sL https://deb.nodesource.com/setup_6.11.3 -o nodesource_setup.sh
    $ sudo bash nodesource_setup.sh
    $ sudo apt-get install nodejs
    $ sudo apt-get install build-essential

É recomendado instalar o pacote build-essential para os pacotes npm que requerem compilação do fonte.

# Instalação .NET Core

O primeiro passo é registrar o feed de produtos Microsoft com os comandos abaixo.

    $ curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg

    $ sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg

    $ sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'

O comando acima registra o pacote para versão 16.04 do Ubuntu e varia o url para as demais versões. A página oficial é https://www.microsoft.com/net/core#linuxubuntu.

Instale o .net core utilizando o comando a seguir.

    $ sudo apt-get install dotnet-sdk-2.0.0

Depois de instalado utilize o comando dotnet --help para obter a lista de comandos disponíveis.

A documentação completa está disponível no endereço abaixo que  inicia com uma explicação sobre o funcionamento da linha de comando (CLI) do .net core e dicas de migração.

(https://docs.microsoft.com/pt-br/dotnet/core/tools/index?tabs=netcore2x)

Para criar o primeiro projeto, navegue para sua pasta de projetos ou crie uma se ainda não houver. Em seguida utilize o comando abaixo substituindo hwapp (de hello world) pelo nome que quiser para sua aplicação.

    $ dotnet new console -o hwapp

Utilize o comando cd para alternar para o diretório criado com o nome da aplicação, hwapp no exemplo.

    $ cd hwapp

Veja o conteúdo do diretório com o comando ls.

    $ ls

Para visualizar o conteúdo do arquivo Program.cs você pode utilizar o editor nano como ilustrado a seguir.

    $ nano Program.cs

Caso você já tenha instalado o visual studio code, utilize o comando abaixo para abrir a pasta do seu primeiro app neste editor.

    $ code .

O ponto do comando acima representa o diretório atual.


# Atualizando com uma linha de comando

    $ sudo apt-get clean && sudo pat-get update && sudo apt-get upgrade -y && sudo apt-get dist-upgrade -y

# VirtualBox 

## Permissão de acesso pasta compartilhada vbox

Inclua seu usuário no final da linha vboxsf:x:999 do arquivo /etc/group. Utilize o comando abaixo para abrir o arquivo.

    $ sudo nano /etc/group
  
A linha ficará conforme ilustrado a seguir:

    vboxsf:x:999:yourusername

É necessário fazer novo login para que as alterações tenham efeito.

Outra possibilidade é executar o comando abaixo para relacionar o usuário logado com o vboxsf.

    $ sudo adduser $USER vboxsf

# Referências e leituras adicionais

https://code.tutsplus.com/tutorials/a-linux-developers-setup--cms-22138

https://askubuntu.com/questions/474839/the-bluetooth-is-disabled-on-ubuntu-14-04


