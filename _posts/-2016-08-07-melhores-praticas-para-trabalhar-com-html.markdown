---
layout: post
author: Eryx
title: Melhores práticas para trabalhar com HTML
date: 07/08/2016
---

Este texto apresenta uma seleção de melhores práticas relacionadas ao uso do html em sites e aplicações para web. São dicas relevantes para melhorar o ranking do site nos buscadores, para garantir a integridade do código, e também para ferramentas de validação.

## Atenção com a abertura e fechamento de tags

O não-fechamento de Tags HTML pode causar problemas de validação e compatibilidade do seu código com os padrões atuais. 

## Utilize títulos significativos para a tag Title

O título de cada página do seu site tem duas funções importantes, informar os visitantes sobre o conteúdo e possibilitar a indexação correta de suas páginas no ranking de resultados dos buscadores. 

O texto da Tag <title></title> será apresentado na barra de título do navegador quando a página estiver carregada, e também compõe o link de acesso ao seu site nos resultados das pesquisas. 

## Utilize Meta Tags descritivas

O conteúdo da tag <meta name=”description” content=”…”/> será exibido nos resultados de busca, abaixo do título. O texto apresentado deve resumir o conteúdo exibido na página.

## Utilize o atributo Meta Keyword

Os buscadores também utilizam as palavras especificadas na Tag <meta name=”keywords” content=”…”/> para indexar o conteúdo da página. É importante utilizar palavras relevantes ao conteúdo exibido.

## Limite o uso de estilos inline nas tags

Não inclua formatações CSS diretamente em tags através do atributo style (ex: style=”{ width:100px; }”). O uso de estilos inline, como é conhecida esta prática, apresenta os seguintes problemas:

* Poluição visual 
* Dificuldade para entendimento do código
* Não reaproveitamento
* Demonstra falta de planejamento no uso de estilos
* Peso desnecessário nos arquivos HTML (fora do bundle) 

A recomendação é sempre utilizar arquivos de estilo separado do seu arquivo HTML.

## Posicione as referências à arquivos javascript no final da página

É preciso posicionar as referências à seus arquivos JavaScript na parte de baixo do arquivo html, com o objetivo de garantir que todos os elementos de página tenham sido carregados antes da execução de qualquer script.

## Estruture o layout da página usando a tag Div

Utilize a tag <div> principalmente para definir os blocos principais do layout. Em uma página cuja estrutura prinicipal seja composta de cabeçalho, coluna esquerda, coluna de conteúdo, coluna direita e rodapé, utilize a tag div para especificar estes blocos principais de conteúdo.

## Utilize letras minúsculas para nomes de tags

O uso de letras maiúsculas não causa erros na renderização da página web, no entanto afeta a legibilidade do código. Por esta razão é recomendado o uso exclusivo de letras minúsculas na definição das tags. Algumas IDEs convertem para minúsculas automaticamente.

## Adicione o atributo Alt para tags Image

A tag <img> é usada para fazer referência a uma imagem dentro de um conteúdo html. O atributo alt permite que seja especificado um texto descritivo da imagem. Este texto é importante para as seguintes situações:

* Ao posicionar o mouse sobre uma imagem, o texto da propriedade alt será exibido.

* Quando qualquer problema com o carregamento da imagem ocorrer, o texto da propriedade alt será exibido no espaço reservado a imagem que não foi carregada.

* Programas leitores de tela usados por pessoas com deficiência, reproduzem verbalmente o texto atribuído a esta propriedade.

Para imagens de composição da tela sem significado relevante, recomenda-se utilizar o atributo alt com o valor vazio, como ilustrado abaixo:

    <img src="..." alt="" />

## Utilize de maneira apropriada as tags de títulos (h1, h2, h3, etc)

Os títulos das páginas são usados na indexação de páginas em sites de pesquisa como o Google, Bing e Yahoo. Existem 6 níveis de títulos, começando com <h1>, que apresenta a formatação de maior destaque, até o <h6>, que apresenta a formatação de menor destaque.

## Utilize a tag de lista para criar estruturas de navegação em menus

Seguindo várias recomendações presentes neste artigo, o uso de tags <div> deve ser limitado aos elementos principais da estrutura da página web. Com base nestas orientações, é recomendado o uso do elemento <ul> ou <ol> para implementação de elementos de navegação do site ou aplicação web.

O elemento <ol> é recomendado para os casos em que a ordenação dos itens for relevante. 

## Utilize CssReset para padronizar as formatações de tags entre diferentes web browsers

O “CSS reset” é um trecho de código de estilos que tem o objetivo de “limpar” todos as formatações pré-definidas, comuns a todos os browsers existentes. Esta estratégia ajuda a reduzir as diferenças visuais encontradas nos diferentes browsers disponíveis no mercado.
É recomendado analisar diferentes modelos de CSS Reset com o objetivo de personalizar as especificações de acordo com as necessidades específicas do seu projeto. Observe também que alguns frameworks CSS (ex.: Bootstrap) possuem implementações próprias aplicadas com seu uso.


...


13. Utilize o elemento adequado no posição correta
O conhecimento sobre o uso correto e adequado dos elementos HTML é muito importante para garantir que a interface apresente o comportamento esperado durante o seu uso. Veja abaixo alguns exemplos de elementos HTML e suas funções:
    <p>
Este elemento é usado para definir um parágrafo em um documento HTML.
    <body>
Este elemento é usado para definir o corpo do documento HTML.
    <ul>
Este elemento apresenta uma lista sem numeração. Usado para momentos em que a ordenação não importa.
    <ol>
Este elemento apresenta uma lista numerada. A lista numerada é usada quando a ordem dos itens é relevante.
    <em>
Este elemento deve ser usado para dar “enfase” a trechos do documento HTML. Para obter enfatizar ainda mais o trecho, pode-se utilizar também a tag <strong> ou <b>.
    <br>
Este elemento é usado para criar uma quebra de linha em textos. No entanto, é recomendado o uso das propriedades Margin e Padding para aumetar a separação entre parágrafos (<p>), ao invés de <br>.
14. Avalie o código-fonte de outros web sites para observar diferentes abordagens
Mesmo depois de estar dominando a linguagem HTML, ainda assim é importante observar o código fonte de diferentes web sites e aplicações, com o objetivo de avaliar outras abordagens para situações semelhantes.
15. Valide o código HTML
É importante periódicamente fazer validações no código HTML, com o objetivo de verificar a adesão do código aos padrões existentes. No entanto, não é pssível afirmar que um código que esteja totalmente validado, é um excelente código. Da mesma forma, não é possível afirmar que um código que não está totalmente validado, seja uma estrutura HTML ruim.
16. Minimize as requisições HTTP
A quantidade de elementos requisitados em uma página ou aplicação web impacta diretamente o seu tempo de carregamento. Este artigo descreve algumas das estratégias para redução do número de requisições HTTP. Entre elas estão a “Combinação de Arquivos”, CSS Sprite e “Combinação de Imagens”, descritas a seguir.
17. Combine arquivos de script e de estilos para reduzir o número de requisições
A combinação de arquivos é uma maneira de reduzir o número de requisições HTTP em aplicações e web sites. A estratégia consiste em unir os diversos arquivos de script em um único arquivo contendo todas as referências, assim como unir todos as referências à arquivos de estilos (CSS) em um único arquivo de requisição.
18. Reduza as referências a imagens com o método CSS Sprite
Este método consiste em combinar todas as imagens de background em uma única, e manipular a apresentação das diferentes partes usando os atributos background-image e background-position. Pense nas diferentes imagens que compõem um botão de menu por exemplo. Ao criar uma única imagem que contenha os formatos para os estados normal, mouse hover, mouse click e ativo, será realizada também uma única requisição HTTP, e você utilizará os atributos CSS para realizar a apresentação (posicionamento) das respectivas imagens de background do botão.
19. Combine várias imagens em uma só
A combinação de imagens em uma única, reduz o número de requisições. Esta abordagem é útil em mapas de imagens. No entanto, o mapa de imagens deve ser utilizado somente para imagens contínuas, e não deve ser usada como menu de navegação devido a problemas de acessibilidade.
20. Utilize referências CDN (Content Delivery Network)
O CDN (Content Delivery Network) consiste em distribuir componentes estratégicos de uma aplicação ou web site em locais geograficamente distribuídos com o objetivo de garantir maior disponibilidade. Esta estratégia não reduz drasticamente o tempo de acesso mas é fácil de implementar, bastando utilizar referências a bibliotecas CDN disponibilizadas por grandes empresas do setor como o Google e a Microsoft. Também é recomendado implementar alguma estratégia de garantia para o caso de indisponibilidade da conexão com a internet (fall-back).
21. Inclua cabeçalhos de controle de Expiração e de cache
Existem componentes dos servidores web que podem ser utilizados para controlar o cache dos componentes de uma aplicação ou web site. O cache armazena os componentes em memória e desta forma melhora o desempenho para as próximas requisições HTTP. Utilize o cabeçalho de controle de cache apropriado para controlar as requisições feitas ao browser. Por exemplo, se sua aplicação foi criada com C#, você pode utilizar o seguinte comando para configurar a data de expiração do cache do arquivo aspx em questão:
Response.Cache.SetExpires(DateTime.Now.AddYears(1));
A data de expiração também pode ser configurada para uma data específica, através do cabeçalho HTTP. No exemplo a seguir o cabeçalho foi configurado para expirar em 15 de abril de 2012:
Expires: Sat, 15 Apr 2017 20:00:00 GMT.
22. Utilize componentes de compactação Gzip
Este componente disponibiliza recursos para compactar a resposta HTTP do servidor, e com isto melhorar o tempo de acesso. Esta estratégia funciona da seguinte maneira:
• O cliente HTTP indica o suporte a compactação através do atributo de cabeçalho Accept-Encoding, incluído na requisição HTTP (Accept-Encoding: gzip, deflate).
• Ao receber esta informação, o servidor web inclui o atributo Content-Encoding no cabeçalho de resposta ao cliente web, que então realiza a descompactação.
Todo conteúdo textual, incluindo documentos HTML, scripts, estilos, XML, JSON, entre outros, podem tirar proveito da compactação para melhorar o tempo de resposta. Apenas os arquivos que já estão em formatos compactados como por exemplo, imagens PNG, arquivos PDF, não devem ser compactados porque já se encontram neste formato.
23. Posicione as referências a arquivos de estilo (CSS) no cabeçalho
Estudos realizados pelo Yahoo! comprovaram que o posicionamento das referências aos arquivos de estilo no cabeçalho (“<head></head>”) da página, causa a impressão de carregamento mais rápido porque o conteúdo é carregado progressivamente nas devidas posições e formatos. O carregamento progressivo fornece um feedback para os usuários sobre o conteúdo que está sendo carregado.
24. Evite usar expressões CSS (CSS expressions)
O Internet Explorer disponibilizava este recurso de expressões JavaScript em atributos CSS do IE5 até a versão IE7, mas a partir do IE8 foi descontinuado e portanto é recomendado que não seja utilizado.
25. Mantenha os arquivos JavaScript e CSS separados do html
Uma das razões pela qual é recomendado que os arquivos de script e de estilos estejam separados do documento HTML, é o fato de os browsers fazerem cache destes tipos de arquivos. Também é importante lembrar que ao incluir estilos nas tags dentro de um documento HTML, o tamanho deste arquivo será consideravelmente maior e causará impacto no desempenho do web site ou aplicação porque estes estilos serão carregados todas as vezes que o documento HTML for carregado. Mesmo que o número de requisições seja reduzido, o tamanho final do arquivo HTML que é carregado frequentemente, será bem maior.
Uma excessão a esta regra acontece em páginas iniciais (ex: Yahoo!, MSN) que são utilizadas como fonte de acesso as demais páginas do site. Neste caso recomenda-se incluir os estilos e scripts nas tags do documento HTML para reduzir o número de requisições HTTP, e fazer uso de outras técnicas para melhorar o desempenho de resposta do servidor web.
26. Reduza as referências a diferentes domínios para reduzir o uso de DNS Lookups
O servidor DNS (Domain Name System) é responsável por mapear e fornecer os endereços físicos das requisições HTTP. A resposta HTTP só será enviada ao browser depois que todos os domínios forem resolvidos pelo servidor DNS, que leva entre 20 a 120 milissegundos para cada resolução. Servidores e browsers realizam o armazenamento de endereços DNS para melhorar o desempenho de resposta. Por esta razão é importante reduzir o número de referências a diferentes domínios dentro da aplicação. Recomenda-se limitar a quatro referências a domínios diferentes no máximo, com o objetivo de reduzir o uso de resoluções de DNS.
27. Reduza o número de Javascript e CSS
A técnica chamada de “minificação” consiste em “limpar” espaços vazios, comentários, linhas embranco e tabs dos arquivos de script, estilos e HTML para reduzir o tamanho dos arquivos, e desta forma melhorar o tempo de carregamento das páginas web. Pesquisas indicam reduções em torno de 25% do tamanho dos arquivos.
28. Evite o uso de Redirects
Este recurso é usado frequentemente para redirecionar o acesso a uma nova versão de um web site, ou quando há alguma mudança no nome do domínio utilizado atualmente. Esta estratégia deve ser evitada porque prejudica o tempo de resposta do servidor. Existem outras abordagens que podem ser utilizadas com melhores resultados. É recomendada a criação de Alias com apontamento para o novo domínio no servidor web que hospeda o web site ou aplicação.
29. Remova scripts duplicados
Apesar de parecer óbvio, existem situações onde o número de pessoas em um time de desenvolvimento ou o número de arquivos de scripts existentes, podem culminar em repetições. Nestes casos, o número de repetições das requisições HTTP causará perda de perfomance. A recomendação para evitar a duplicação de scripts é usar um único arquivo de script contendo as referências para os demais, ou ainda, criar uma função na aplicação, que fará a inclusão de todos os scripts sem riscos de duplicação por ser um ponto de referência única.
30. Configure o Cache para Ajax
Uma das características da tecnologia Ajax é a capacidade de fornecer um feedback mais rápido para os usuários. No entanto, existem situações em que mesmo fazendo a requisição assíncrona de uma parte específica do conteúdo, haverá alguma espera por parte dos usuários. Para melhorar a performance das respostas Ajax, é recomendado adotar as seguintes estratégias:
• Configurar o controle de cache e expiração de conteúdo, conforme mensionado anteriormente.
• Fazer uso do componente Gzip para compactação.
• Minimizar código JavaScript
• Evitar o uso de redirects.

31. Utilize GET ao fazer requisições Ajax
O método GET utiliza apenas um pacote TCP que é enviado ao servidor para fazer a requisição Ajax, e deve ser usado sempre que não houverem dados a serem enviados. O método POST envia duas requisições para o servidor, primeiramete o cabeçalho e em seguida os dados, e portanto possui um desempenho inferior ao método GET. É relevante informar que quando é feita uma requisição POST mas não existem dados a serem enviados, o comportamento apresentado é o mesmo do método GET.
32. Utilize Post-load Components
Esta estratégia consiste em definir os elementos cujo carregamento é prioritário para a exibição da página, para em seguida carregar os elementos complementares. No caso do JavaScript por exemplo, é recomendado separar o código que é indispensável para o correto carregamento da página, do código script que disponibiliza funções de animação por exemplo. Existem ainda alguns componentes criados por grandes empresas do setor, para ajudar na implementação desta técnica, entre eles o YUI Image Loader do Yahoo!, que permite o carregamento de imagens gradativo quando elas são necessárias na interface.
33. Utilize Preload de componentes
O objetivo desta estratégia é utilizar o tempo de inatividade do browser para requisitar imagens, estilos e scripts, antecipando os componentes que serão necessários a seguir, com o objetivo de fazer com que eles estejam disponíveis em cache no momento da próxima solicitação, e desta maneira agilizando o tempo de resposta do servidor e carregamento da página.
34. Reduza o número de elementos DOM
A quantidade de elementos que compõem uma página impacta diretamente o processamento necessário para execução de códigos JavaScript. A presença de um número excessivo de elementos indica a possibilidade de melhorar a estrutura do layout, e também indica que algumas das recomendações apresentadas aqui não estão sendo seguidas. Para confirmar que o número de elementos usados é excessivo, recomenda-se observar o código de outros sites ou aplicações semelhantes e fazer uma comparação. É possível obter o número total de elementos usando o comando a seguir no console do Firebugs:
document.getElementsByTagName(‘*’).lenght
35. Reduza o número de elementos
Iframe
Este elementos permite que sejam agrupados diversas páginas HTML em uma única. Existem problemas relacionados as diversas requisições HTTP executadas para cada elemento IFrame e ao carregamento do conteúdo em blocos sem sincronia, além da questão de blocos acessados diretamente através de links apresentados em resultados de buscas onde um bloco é carregado mas sem os outros que compõem a página. Como vantagens, podemos citar a possibilidade de download paralelo de scripts, e também a conveniência de liberar o carregamento de conteúdos adicionais não relacionados ao conteúdo principal da página, como por exemplo, o carregamento de propagandas do tipo ads e banners.
36. Não exiba o erro 404
O carregamentos de uma página de erro 404 (erro de arquivo não encontrado) prejudica a experiência de usuário com uma mensagem que não tem nenhuma utilidade. Alguns web sites apresentam páginas de erro personalizadas onde são oferecidas sugestões sobre o provavel conteúdo que os usuários pretendem acessar.
37. Reduza o tamanho do Cookie
As informações armazenadas em cookies são transmitidas ao servidor web nos cabeçalhos HTTP, e seu tamanho pode impactar no tempo de resposta. Tenni Theurer e Patty Chi em seu artigo de título “When the Cookies Crumbles” apresentam as seguintes recomendações:
• Utilize os cookies que são realmente necessários para personalização e segurança
• Mantenha o tamanho dos cookies o menor possível.
• Tenha atenção ao domínio onde os cookies são configurados para que não impacte outros subdomínios da aplicação.
• Configure uma data de expiração apropriada para que não hajam solicitações desnecessárias.

38. Utilize domínios sem Cookie para Componentes
Garanta que conteúdos estáticos como imagens, estejam livres de cookies em seus respectivos cabeçalhos HTTP. É recomendado criar um sob-domínio para os componentes estáticos do web site ou aplicação, com o objetivo de manter as requisições a estes elementos livre de cookies.
39. Reduza o acesso a elementos DOM (Javascript)
Existem algumas estratégias adotadas para reduzir o tempo de resposta das páginas, relacionadas neste artigo:
• Cache de referências aos elementos acessados
• Atualização de elementos em background que são incluídos na estrutura da página depois que são processados.
• Não utilizar JavaScript para correções de layout.

40. Reduza o número de métodos de tratamento dos eventos (Javascript)
Os métodos da aplicação ou web site devem ser desenvolvidos com a capacidade de tratar o maior número de elementos semelhantes, evitando um número excessivo de métodos. Outra questão relevante para o desenvolvimento de métodos de tratamento de eventos é que pode-se utilizar o evento DOMContentLoaded, que é executado antes do evento Load, para realizar ações na estrutura da página HTML.
41. Dê preferência ao uso de <link> ao invés de @import (CSS)
O atributo @import no Internet Explorer é carregado no final da página e portanto deve ser evitado, conforme orientação anterior sobre incluir as referências aos arquivos CSS no início da página.
42. Evite o uso de Filters (CSS)
É recomendado evitar o uso do filtro proprietário da Microsoft AlphaImageLoader, que tinha o objetivo de corrigir problemas relacionados ao congelamento de imagens semi-transparentes no formato “PNG true color”, na versão 7 do navegador. Entre os problemas decorrentes de seu uso estão o consumo excessivo de memória, uma vez que é aplicado por elemento, além de congelar imagens na tela durante o carregamento. Ao invés do filtro, a sugestão é utilizar o formato PNG8 que é compatível com o Internet Explorer.
43. Utilize imagens otimizadas (imagens)
Existem algumas ações que podem ser executadas com o objetivo de otimizar ainda mais as imagens desenvolvidas para o web site ou aplicação, entre elas:
• Imagens GIF podem ser otimizadas reduzindo o número de cores da paleta quando o formato selecionado utiliza mais cores do que as utilizadas efetivamente na imagem.
• Algumas vezes as imagens no formato PNG são menores do que as mesmas imagens no formato GIF. Lembrando que o formato GIF não permite o uso de transparência.
• Utilize ferramentas para otimização de imagens PNG como por exemplo o Pngcrush.
• Utilize ferramentas para otimização de imagens JPG, como por exemplo ojpegtran.

44. Otimize as renderizações de imagens referenciadas por CSS (Imagens)
Existem ainda alguns cuidados a serem tomados para otimização de imagens:
• Utilize uma combinação que seja menor que 256 cores em imagens.
• Evite imagens grandes com espaços vazios, para que consumam menos memória de armazenamento.

45. Não escale imagens usando HTML 
Ajuste as imagens ao tamanho necessário, evitando o uso dos atributos width e height para fazer ajustes. Se você precisa que uma imagem tenha 100×100 pixels, então ela deve ser criada neste tamanho. Não reduza uma imagem maior para ficar no tamanho necessário porque o tamanho da imagem será maior e ainda poderão haver deformações.
46. Faça com que o favicon.ico seja pequeno e fique em cache
A imagem favicon.ico é uma imagem que fica armazenada no diretório raiz de web sites e aplicações. É importante que ela esteja presente uma vez que o browser executará uma requisição HTTP com erro 404 caso a imagem não esteja disponível. É importante que a imagem seja menor que 1kb, assim como configurar o cache para este elemento.
47. Evite o uso de atributo src vazios para imagens
O comportamento padrão alguns browsers, ao encontrar o atributo src vazio, realizam requisições e processamento desnecessários. E portanto, este uso do atributo deve ser evitado.
48. Sempre declare um Doctype
A declaração doctype informa ao browser o padrão XHTML que está sendo utilizado para renderização da linguagem e marcação adequadamente. Ajuda na renderização correta do HTML. Por exemplo:
    <!DOCTYPE html PUBLIC “-//W3C//DTD XHTML 1.0 Strict//EN” “http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd”&gt;
49. Utilize o atributo Title em links (sempre que necessário)
O uso do atributo title em tags de links, aumenta a acessibilidade do conteúdo. É importante lembrar que este atributo é usado para melhorar a compreenção sobre o significado dos links. Por exemplo:
    <a href=”http://blog.com/todososartigos&#8221; title=”Acesso a lista de artigos.”>Clique aqui.</a>
50. Utilize as tags Fieldset e Label em formulários web
Utilize os elementos <fieldset> e <labels> nos web forms. A tag <fieldset> é usada para criar agrupamentos lógicos no layout do web form, e a tag <legend> é utilizada em conjunto, como título do agrupamento. Por exemplo:
    <fieldset>
    <legend>Detalhes</legend>
    <label for=”name”>Primeiro Nome: </label>
    <input type=”text” id=”nome” name=”nome” />
    </fieldset>
51. Utilize comentários condicionais para correções das diferenças entre versões de browsers
Utilize o recurso de comentários condicionais, para aplicar diferentes estilos para as diferentes versões do internet explorer. Por exemplo:

    <!–[if IE 7]>
    <link rel=”stylesheet” href=”css/ie-7.css”
    media=”all”>
    <![endif]–>

52.Escreva códigos com formato consistente
Escreva códigos indentados que facilitem a visualização e compreenção do código html.
Exemplo indentado:
    <html>
        <head>
            <title>Título</title>
        </head>
…

Exemplo não-indentado:
    <html>
    <head>
    <title>Título</title>
    </head>
…
53. Evite uso excessivo de comentários
Comentários são importantes para linguagens de programação. Mas no caso do html, as tags foram criadas para serem auto-explicativas. Comentários excessivos prejudicam a compreensão da estrutura do documento html.
