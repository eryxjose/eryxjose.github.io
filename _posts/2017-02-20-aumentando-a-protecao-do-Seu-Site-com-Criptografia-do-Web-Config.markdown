---
layout: post
author: Eryx
title: Aumentando a Proteção do Seu Site com Criptografia do Arquivo de Configurações
date: 20/02/2017
---

A tentativa de acesso não autorizado ao arquivo de configurações do seu site é uma das estratégias de ataque mais utilizadas para aplicativos e sites na internet.
As aplicações e sites que rodam sobre a infraestrutura de serviços da plataforma Microsoft, utilizam arquivos de configuração para determinar uma série de parâmetros do seu funcionamento, inclusive informações denominadas sensitivas, tais como senhas, servidores, contas de e-mail, entre outras. 

O acesso a este arquivo e respectivas informações não está disponível por meios normais via internet, mas existem meios de obter acesso não autorizado. Por esta razão, estas informações precisam ser mantidas em um formato criptografado, que proteja dados sigilosos mesmo que haja acesso direto ao referido arquivo de configurações.

Veja uma ilustração do arquivo de configurações antes e depois de modificado.



Do ponto de vista do desenvolvedor, a estratégia é criptografar o arquivo web.config antes de enviá-lo para o ambiente de produção.

Para aplicar a encriptação a uma determinada seção do arquivo de configurações em um projeto de site web, abra o Prompt de Comando e utilize o comando "cd" para alternar para o diretório da versão de instalação do Framework .NET em c:\windows\Microsoft.NET\Framework\( a versão instalada no seu computador ) ou utilizar o Explorador de Arquivos para navegar até o diretório indicado e clicar com o botão direito em uma área vazia da janela com a tecla Shift pressionada para que seja exibida a opção "Abrir janela de comando aqui", ilustrada abaixo.



Uma vez que a janela do prompt de comando mostrar o caminho correto do Framework .NET, entre com o comando: aspnet_regiis -pef (nome da seção) (caminho do aplicativo), substituindo (nome da seção) pelo nome da seção do arquivo de configurações que você quer encriptar, em nosso exemplo, connectionStrings, e substituindo (caminho do aplicativo) pelo caminho físico raiz do seu projeto. Observe que no caso de haver espaços vazios no caminho do arquivo, como por exemplo "c:\Meus Projetos", será necessário colocar o caminho entre aspas.

Veja na ilustração abaixo, os comandos utilizados para criptografar e descriptografar a seção connectionStrings em nosso sistema de loja virtual.
.


Você pode obter mais detalhes sobre os parâmetros do comando aspnet_regiis para criptografia digitando o comando seguido por /?, ex. aspnet_regiis /?
A criptografia é feita gerada por algoritmo RSA com base em chaves assimétricas, amplamente utilizado pelo mercado e considerado seguro. 

Mais informações sobre o algoritmo RSA podem ser obtidas em <https://pt.wikipedia.org/wiki/RSA> e mais sobre os parâmetros relacionados a encriptação no Framework .NET nos artigos <http://www.codeproject.com/Articles/877258/How-to-Encrypt-Web-config-Using-aspnet-regiis-exe> e <https://msdn.microsoft.com/en-us/library/zhhddkxy.aspx>. 
