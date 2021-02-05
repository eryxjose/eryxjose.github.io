---
layout: post
author: Eryx
title: O papel da arquitetura no ciclo de vida do desenvolvimento de software
date: 16/01/2021
---

O desafio que guia a arquitetura de software é manter o máximo de opções em aberto o maior tempo possível. Manter em aberto as opções para todos os detalhes relacionadas ao software, por exemplo, a escolha do banco de dados ou o servidor web.

Software entrega valor em sua estrutura e em seu comportamento. A capacidade do software ser atualizado rapidamente com o menor impacto possível é o que caracteriza uma boa arquitetura.

Veja como a arquitetura pode impactar diferentes etapas do ciclo de vida do software.

* Desenvolvimento - Minimizar o custo e maximizar a produtividade.
* Distribuição - Possibilitar o deploy a partir de uma ação única.
* Operações - Evidênciar para desenvolvedores os casos de uso, recursos e comportamentos do sistema.
* Manutenção - Reduzir o tempo necessário para o entendimento do código e o risco de alterações causarem problemas.

A primeira prioridade da arquitetura é dar suporte aos casos de uso. Tornar evidente a localização e a função de cada elemento do sistema. De maneira que o desenvolvedor não tenha dúvidas sobre onde implementar cada funcionalidade. 

Também é prioridade da arquitetura, manter as opções em aberto em relação as demandas por desempenho, por exemplo, suportar um número x de transações por segundo. Sistemas monolíticos não permitem isolar seus componentes e podem inviabilizar o atendimento a demanda requerida.

Em relação ao desenvolvimento, é importante garantir que diferentes times possam trabalhar de maneira independente e portanto, particionar o desenvolvimento do sistema em componentes independentes.

A identificação e separação dos componentes do sistema por suas inter-relações e similaridades é feita utilizando princípios. Os componentes podem ser linkados em um mesmo executável, dispostos em conjunto (arquivo .war por exemplo), ou podem ser distribuídos de maneira independente na forma de plugins. Uma ou mais classes farão parte de cada componente.

Os princípios que regem a criação de componentes consideram os aspectos de coesão e acoplamento. 

## Princípios de Coesão

Os Princípios da Coesão orientam sobre fatores de inter-relacionamento entre componentes.

Reuse/Release Equivalence Principle (REP) - As classes e módulos que compõem o componente fazem parte de um agrupamento, possuem um propósito comum, e são distribuídos como uma unidade para cada versão.

Common Closure Principle (CCP) - Determina que você agrupe os componentes que possuem classes alteradas pelo mesmo motivo. Um componente não deve ter mais de um motivo para mudar.

Common Reuse Principle (CRP) - Classes e módulos que são utilizados em conjunto pertecem ao mesmo componente. Portanto, que possuem dependências mútuas e não dependem de outros componentes.

## Princípios de Acoplamento

Acyclic Dependencies Principle (ADP) - Você elimina a dependência cíclica e está com conformidade com ADP quando seu componente não precisa mudar porque uma nova versão de um componente relacionado está disponível. A alteração do componente com relação de dependência, não impacta seu componente. E você pode implementar a nova versão quando e se for necessário.

Stable Dependencies Principle (SDP) - As setas de dependências apontam para o componente mais estável. Estabilidade neste caso, refere-se a componentes que mudem raramente e qualquer mudança envolve múltiplas alterações em componentes dependentes.

Stable Abstractions Principle (SAP) - Componentes devem ser abstratos na mesma proporção em que são estáveis. Os componente que encapsulam as regras de mais alto nível do sistema são os mais estáveis e abstratos.

