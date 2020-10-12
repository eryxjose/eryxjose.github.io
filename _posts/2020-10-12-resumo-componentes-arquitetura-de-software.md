---
layout: post
author: Eryx
title: Resumo sobre Componentes na Arquitetura de Software
date: 12/10/2020
---

Todo software é composto de componentes. Para linguagem Java, os arquivos .jar, para linguagem Ruby, os arquivos .gem, e para o .NET, os arquivos .dll. São arquivos binários que podem ser linkados em um único executável (.exe) ou agrupados em um único arquivo, por exemplo .war, ou distribuídos individualmente na forma de plugins independentes. São específicos para cada plataforma de hardware, por isso um sistema feito para rodar no Windows não é executado no Linux. É desejavel que os componentes possam ser distribuídos ou desenvolvidos de maneira independente.

No passado, os programadores precisavam definir os endereços de memória onde cada componente seria carregado. Funções em bibliotecas externas utilizadas no software, eram compiladas separadamente e incluídas como parte da distribuição.

Quando o tamanho do software demandou a fragmentação do aplicativo em diferentes segmentos de memória, os problemas com desempenho forçaram uma mudança na arquitetura computacional para permitir a relocação dos binários em memória utilizando um recurso chamado smart loader que possibilitava aos programadores informar o endereço de memória para carregar a aplicação e funções de bibliotecas utilizadas. O loader, depois chamado de link loader possibilitou separar os programas em segmentos carregados e compilados conforme necessários. Esta estratégia eventualmente possibilitou o surgimento do Active-X e o início do JVM e onde nasce a arquitetura de componentes plugin. Estes arquivos linkados em tempo de execução que podem ser plugados, são os componentes de software da arquitetura.

Uncle Bob no livro Clean Architecture, relaciona um conjunto de princípios para definir o acoplamento e a coesão na criação de componentes. Em relação a coesão, os princípios ajudam a definir quais classes pertencem a quais componentes. E os princípios para o acoplamento, orientam sobre a relação entre os componentes. O autor destaca que a arquitetura do software não é criada no início, mas evolui e é construída conforme o sistema se modifica e evolui.

> "The component structure cannot be designed from the top down. It is not one of the first things about the system that is designed, but rather evolves as the system grows and changes."

Então resumindo, componentes são arquivos binários individuais que podem ser carregados em tempo de execução quando são necessários. É comum por exemplo, separar as regras de negócio da sua aplicação em um componente e as funções de acesso a dados em outro. A criação e o relacionamento entre estes componentes é regida pelos princípios citados anteriormente.


