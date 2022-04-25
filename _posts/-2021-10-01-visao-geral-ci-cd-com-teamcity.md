---
layout: post
author: Eryx
title: Visão Geral CI/CD com TeamCIty
date: 01/10/2021
---


TeamCIty é uma ferramenta para automatizar a integração e publicação de códigos para diferentes linguagens de programação e tipos de servidores.

Características do TeamCity:

* Build de múltiplos projetos de maneira concorrente para diferentes plataformas. Cada projeto pode incluir código de diferentes linguagens de programação e o build pode ser feito para diferentes sistemas operacionais.
* Otimização do ciclo de integração de código a garantir que nenhum código quebrado seja enviado para o repositório.
* Executar testes unitários e ver os resultados dos testes a medida que são executados.
* Executar 'code coverage' e 'code analysis' em projetos .NET e Java.
* Os 'Build Agents' podem ser desativados para evitar custos quando não estiverem em uso.
* Fácil aprendizado e configuração.

Arquitetura:

O usuário TeamCity acessa a interface web do servidor e executa o build que conecta/executa um 'Build Agent'que verifica alterações do código fonte no repositório, faz o build e o deploy dos artefatos criados.

É altamente recomendável não instalar 'Build Agents' e o TeamCity na mesmo servidor. Existem diferentes razões para manter o servidor TeamCity separados dos 'Build Agents'. Primeiramente, é recomendado que o servidor tenha uma única responsabilidade por razões de desempenho, mas também porque dependendo do 'Build Agent', podem haver diferentes requisitos de infraestrutura e ambiente. Por exemplo, se o TeamCity está em um servidor Linux mas o 'Build Agent' precisa acontecer em um servidor Windows.

Em relação a arquitetura, é composta de um servidor TeamCity (Windows/Linux/Unix), os servidores com 'Build Agents' e o repositório de código (source control).

O TeamCity é composto de um servidor criado em Java, portanto dependente do JDK, e de um servidor 'Build Agent' também dependente do JDK. Os 'Build Agents' recebem um comando do TeamCity para iniciar o build. 

Em ambientes com múltiplos projetos e 'Build Agents' é recomendado utilizar um 'Load Balancer'. Também é recomentado configurar uma base de dados externa para que todos os projetos estejam visíveis independente da instância do TeamCity em uso. Você pode criar um cluster de bases de dados para garantir a disponibilidade dos projetos. A AWS disponibiliza o recurso 'Auto Scale Group' que pode gerar novos servidores automaticamente com base em diferentes critérios, por exemplo, limite na capacidade de processamento do servidor.

Instalando o TeamCIty

Baixe o instalador em https://www.jetbrains.com/teamcity.

Instalando Build Agents

    https://confluence.jetbrains.com/display/TCD18/Setting+up+and+Running+Additional+Build+Agents?_ga=2.186109225.1088896524.1633202232-1656525369.1633202232#SettingupandRunningAdditionalBuildAgents-installingBuildAgentsZip

...

Configurando Build Agents na núvem AWS

Inicie criando um 'Security Group' que será usado para possibilitar a conexão com o servidor TeamCity. Acesse 'Security Groups' no dashboard EC2 AWS e clique no botão 'Create Security Group'. Entre com um nome, descrição e selecione o VPC marcado como (default) na lista de opções. Na aba 'Inbound' selecione 'RDP' na coluna 'Type'. Selecione 'Anywhere' na coluna 'Source' para definir a fonte de tráfego. Clique no botão 'Add Rule' e selecione 'Custom TCP' na coluna 'Type' e entre com a porta '9090' e altere a coluna 'Source' para 'Anywhere'. Clique no botão 'Create' para concluir.

Em seguida, crie um usuário para conexão do TeamCity com o serviço Amazon Web Services, e utilizar imagens personalizadas para gerar e executar agentes. No dashboard AWS, selecione IAM (Identity and Access Management) e então 'Roles' e no botão 'Create Role'. O primeiro passo para criar uma 'Role' é selecionar o serviço AWS 'EC2' e o mesmo para seção 'Select your use case'. Clique em 'Avançar' para a tela 'Attach permissions policies'. Pesquise por 'codedeploy' e selecione 'AWSCodeDeployDeployerAccess' na lista de policies. Clique em 'Next' para a tela 'Review'. Entre com um nome e clique no botão 'Create' para concluir.

O próximo passo é adicionar um usuário. Acesse 'Users' e clique em 'Add User'. Entre com um 'User name' e marque a opção 'Programmatic access' na seção 'Access type'. Clique em 'Next' para abrir a tela 'Permissions' e selecione 'Attach existing policies directly' para exibir uma lista de policies pré-definidas ou adicione uma nova policy clicando no botão 'Create policy'. Você pode selecionar a policy 'AmazonEC2FullAccess' se for apenas um teste. Clique em 'Next' e então em 'Create user' para concluir. A tela de confirmação exibe apenas uma vez (neste momento) a coluna 'Access key ID' e a coluna 'Secret access key'. Clique no botão 'Download .csv' para salvar estas informações que serão utilizadas nas configurações de acesso entre o TeamCity e o servidor com os 'Build Agents'.

Em seguida, será necessário criar uma nova instância EC2 e instalar componentes e software para compilar e fazer deploy do código fonte. No dashboard EC2 clique em 'Launch Instance' para exibir uma lista de imagens de máquinas pré-definidas e em seguida em 'Community AMIs'. Você pode filtrar pelo sistema operacional e então selecionar uma dimensão específica na tela 'Choose an Instance Type'. Clique em 'Next' para exibir a tela 'Step 3: Configure Instance Details'. Selecione 'Enable' no campo 'Auto-assign Public IP' e selecione 'TCBuildAgentRole' (Role criada anteriormente) no campo 'IAM Role'. Depois clique em 'Next: Storage' e faça alterações de armazenamento caso seja necessário. A tela seguinte 'Step 5: Add Tags', permite definir informações e rótulos que identifiquem facilmente a imagem (máquina). Na tela 'Step 6: Configure Security Group', selecione 'Select an existing security group' e selecione o grupo criado anteriormente. Na última tela 'Step 7: Review Instance Launch', clique no botão 'Launch' e selecione 'TeamCity' na lista 'Select a key pair'. Clique no botão 'Launch Intance'.

O próximo passo é logar na máquina criada para os Build Agents' e atualizar o JDK. Pesquise por 'download java se 9' e baixe o instalador jdk-9 para o sistema operacional da instância EC2 criada. Execute a instalação e então acesse sua instalação do TeamCity server utilizando o navegador e clique em 'Install Build Agents' no canto superior direito da tela de administração do TeamCity. Baixe e execute a instalação para o sistema operacional em uso, no caso do Windows, será instalado o serviço 'Windows Service' do 'Build Agent' no caminho padrão 'c:\BuildAgent'. Na tela seguinte do assistente de instalação, entre com as informações relativas ao servidor TeamCity: 'serverUrl', 'name', 'ownPort', 'systemDir', 'workDir', 'tempDir', 'authorizationToken'. Na próxima tela, marque para utilizar uma conta de sistema para executar o TeamCity. Clique em 'Next' e deixe marcada a opção 'Start Build Agent service'. Clique Next e então Finish. Abra o arquivo 'teamcity-agent' na pasta 'c:\BuildAgent\logs' e verifique que não há entradas de erros no final do arquivo.

Em seguida, será necessário instalar softwares e pacotes necessários para executar o build e deploy do código para plataforma desejada. No caso de aplicações .NET em ambiente Windows pesquise por 'msbuild tools 2019'. Localize, baixe e execute o instalador do msbuild. É recomendado instalar também o software 'aws tools for windows powershell'. 

Execute 'Ec2LaunchSettings' para configurar uma imagem do sistema operacional que será instanceiada múltiplas vezes.

...

Utilize o comando 'services' no menu Iniciar para abrir a lista de serviços do Windows. Pesquise por 'TeamCity Build Agent' na lista de serviços em execução do Windows e verifica o status. Se o serviço não iniciar automaticamente, pode ser necessário instalar a versão correta do JDK. Depois de instalar o JDK, será necessário reiniciar o referido serviço.

...












