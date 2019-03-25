---
layout: post
author: Eryx
title: Quebra de página com CSS
date: 05/02/2016
---
A específicação oficial do W3.org determina o uso das propriedades 'page-break-before', 'page-break-after' e 'page-break-inside' para controle de quebra de página em documentos html para impressão.

Para fazer seção específica de uma página html iniciar em uma nova página quando este documento for impresso, utilize a propriedade page-break-before com o valor always. 

Caso queira que a seção exemplificada acima, comece numa nova página de impressão apenas quando não houver espaço para exibí-la, o mais indicado é utilizar a propriedade page-break-inside com o valor avoid. 

Neste caso, o navegador irá manter o conteúdo da seção na mesma página com outra seção apenas se houver espaço para que ela seja exibida por inteiro. 

Caso não haja espaço para exibir a seção junto com o conteúdo existente na página em questão, será incluída uma quebra de página antes da seção definida com a referida propriedade.

No exemplo abaixo, observe que uma nova página é criada mesmo que haja espaço para exibir a seção na mesma página.

    <div style="page-break-before: always;">
    ...
    </div>



Caso o seu objetivo principal seja manter o conteúdo da seção Informações Adicionais sem quebra de página, a melhor opção é utilizar a propriedade page-break-inside com o valor avoid, para que o navegador aplique a quebra de página apenas se o espaço disponível na página não for suficiente para mostrar todo conteúdo da referida seção. Veja na ilustração abaixo, que havendo espaço para exibir todo conteúdo da seção Informações Adicionais, nenhuma quebra de página é aplicada.




A referência oficial do W3.org para quebra de páginas nas impressões de documentos html pode ser acessada em https://www.w3.org/TR/CSS21/page.html#propdef-page-break-before. 
