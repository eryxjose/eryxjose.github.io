---
layout: post
author: Eryx
title: Como descobrir a capacidade real da bateria do seu notebook
date: 15/07/2014
---

Provavelmente você sabe que pode verificar o percentual de carregamento da bateria do seu notebook através de um ícone na barra de notificações do Windows. No entanto, esta informação não representa a capacidade real de carga atual da sua bateria.

Normalmente, já sabemos que a bateria não está durando muito tempo em razão da sua utilização diária, mas é difícil dizer a quantidade exata. Na hora de decidir pela troca, esta informação pode ajudar a definir que o momento chegou.

Abra o prompt de comando em modo administrador clicando com o botão direito no ícone do menu Iniciar (Win 8), e selecione a opção "Prompt de comando (Admin)". Em seguida execute o comando "powercfg -energy -output c:\energy-report.html" e pressione a tecla Enter.

Veja na ilustração abaixo, a execução do comando, seguida pela mensagem indicando o início do rastreamento da capacidade, e depois de alguns segundos, o resultado geral obtido e a mensagem recomendando a leitura do relatório no local indicado para gravação.



O relatório será aberto no seu navegador padrão, e você pode pesquisar pela palavra "bateria" para localizar rapidamente as informações que procura. Veja na imagem abaixo, que a última carga total não obteve nem 60% da capacidade total original.


Você vai observar que que o relatório faz uma avaliação geral de todos os componentes de hardware do sistema além da vida útil da bateria e estas informações podem ajudar a identificar outros problemas.
