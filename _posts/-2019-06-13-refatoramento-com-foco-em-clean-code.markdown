---
layout: post
author: Eryx
title: Refatoramento com foco em Clean Code
date: 22/05/2019
---

# Nomeclatura

Nomes tornam o código compreensível. Os exemplo abaixo demonstram nomes que não contribuem para o entendimento do código. Ao contrário, torna o trabalho de manutenção mais difícil porque será necessário avaliar diferentes trechos de código, possivelmente em diferentes arquivos, para compreender a lógica utilizada. O tempo necessário para quaisquer alterações aumenta drasticamente assim como o custo envolvido. 

Evite:

    SqlDataReader dr1;

    int od;

    void Button1_Click() {...}

    class Page1 {}

## Nomes com significado (que deixe claro a intenção)

Dar nomes para variáveis, classes, métodos, etc., é considerado uma das grandes diculdades da área de desenvolvimento de software. A implementação das recomendações apresentadas neste texto para um código que atenda aos princípios SOLID também contribui para facilitar elaboração de nomes porque passamos a ter uma única função para cada método (Single Responsability Principle), fechamos as classes para alteração e permitimos que elas sejam estendidas (Open Close Principle), e esta organização ajuda a definir nomes.

No exemplo acima, o método Button1_Click deixa claro que o evento refere-se a um botão com o nome Button1 mas isto não acrescenta nada para o entendimento da funcionalidade que este código executa. É recomendado utilizar um nome que expresse o que acontecerá quando o botão for pressionado, por exemplo.

    void CheckProductInStock() {...}

ou 
    void CheckAvailability() {...}

## Nomes Extensos

Nomes muito extensos também dificultam o entendimento do código mesmo que não haja diferença em relação a perfomance uma vez que internamente as variaveis são substituídas por endereços de memória.

## Convenções

É recomendado seguir as convenções conhecidas e utilizadas pelo mercado porque facilita para novos desenvolvedores, e também é recomendado manter um documento com as orientações adotadas pela equipe ou empresa.

## Nomes com prefixos

Durante muitos anos foi utilizado o padrão Hungarian Notation que acrescenta o tipo de dados como um prefixo na frente do nome do método. Atualmente não é recomendado utilizar prefixos para indicar o tipo de dados. 

Evite:

    int iMaxRequests;

ou

    var m_objectCollection = new StringCollection();

Ao invés disso, utilize um nome que informe o que será armazenado nesta variável, por exemplo, no caso de uma coleção de países podemos utilizar.

    var countryNames = new StringCollection();

## Nomes ambíguos

Nos exemplos abaixo não está claro o que estas propriedades e métodos fazem.

    int? incidentNameId;

    bool MultiSelect() {}

## Nomes extensos

    Customer theCustomer;

    List<Customer> listOfApprovedCustomers;

Ao invés disso utilize.

    Customer customer;

    List<Customer> approvedCustomers;

## Resumo

* Evite nomes muito extensos e muito curtos
* Utilize nomes com significado
* Crie nomes que informem o que será feito (intenção)
* Escola um nome com base no domínio da solução


# Muitos parâmetros em métodos





# Referências:

Best Practices for Variable and Method Naming
https://dzone.com/articles/best-practices-variable-and
