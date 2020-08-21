---
layout: post
author: Eryx
title: Visão Geral de Vanila Javascript
date: 29/09/2018
---


# Configurações de ambiente

* Instale o NodeJS disponível para download em https://nodejs.org/en/ 
* Instale o git disponível em https://git-scm.com/book/en/v2/Getting-Started-Installing-Git 
* Instale o VSCode disponível em https://code.visualstudio.com/
* Instale a extensão Live Server no VSCode
* Crie uma pasta para seus projetos (ex.: c:\projetos)
* Faça um clone git ou download do repositório de exemplos utilizando o comando abaixo 

    git clone https://github.com/eryxjose/vanilla_javascript.git

# Fundamentos do Javascript

JavaScript é uma linguagem amplamente utilizada na atualidade, disponível em diversos tipos de dispositivos com capacidade de execução no cliente e no servidor.

Você pode codificar diretamente no seu navegador, utilizar um editor online (ex.: http://jsbin.com/, http://codepen.io/) ou utilizar um editor de textos de sua preferência. Estou utilizando o Visual Studio Code (https://code.visualstudio.com/) que possui versões para diferentes sistemas operacionais.

# Declaração de variáveis

O comando var é utilizado para declarar uma variável de qualquer um dos tipos listados acima, e você pode alterar o tipo de informação armazenada. Veja alguns exemplos:

    var x = 1;

A declaração da variável x com o valor 1 está sendo utilizada para demonstrar a maneira mais simples de criar uma variável e ao mesmo tempo definir seu valor inicial que poderá ser alterado posteriormente. Você também pode declarar a variável (x em nosso exemplo) sem atribuir um valor inical. Veja a seguir:

    var x;

Você pode alterar o valor da variável x por um valor literal ou por uma chamada de função que retorne um valor. Veja alguns exemplos:

    x = 12; // Atribui o valor 12 a valirável x.
    x = 12 + 3; // Atribui o resultado da soma dos valores 12 e 3.
    x = function () { return 3; } // Necessário x() para obter o valor.
    x = (function () { return 3; })(); // Alternativa auto-executável.
    x = "abc"; // Alterando o valor da variável x para string "abc".
    x = [2,44,14,89,41,63]; // Array de números
    x = ["a","b","c"]; // Array de strings
    x = [{id:1,titulo:'a'},{id:2,titulo:'b'},{id:3,titulo:'c'}]; // Array de objetos

Todas as atribuições ilustradas acima também valem para os comandos let e const disponibilizados oficialmente no ECMAScript 6/2015. A diferença entre os comandos de atribuição refere-se ao escopo e a permissão de alteração do valor. O comando let é válido apenas para o bloco onde foi declarado e o comando const determina que o valor não possa ser alterado.

Relevante comentar que a problemática em torno do uso de let e const ocorre porque estes comandos foram incluídos oficialmente na versão ECMAScript 6/2015 e portanto funcionarão apenas para as versões de navegadores lançadas posteriormente. Caso haja determinação para sua aplicação suportar versões mais antigas dos navegadores, uma das soluções possívels é utilizar um transpiler para gerar diferentes versões do seu código. Abordaremos este tema adiante.


# Literais

Os literais são utilizados para simplificar a criação de tipos comuns da linguagem. Por exemplo:

    var x = {};

O literal {} acima equivale ao construtor ilustrado abaixo:

    var x = new Object();

Da mesma forma, '', "", e ´´ são literais para o tipo String, true e false são literais para o tipo Boolean, os números inteiros 1, 2, 3, etc., são literia para o tipo Number. No exemplo de atribuições para x do tópico Declaração de Variaveis acima, foram utilizados literais e expressões.

# Tipos 

* Value Types (Primitives) ou Tipos por Valor (Primitivos): Number, String, Boolean, Symbol, undefined, null;
* Reference Types ou Tipos por Referência: Object, Function, Array;

 Variáveis independentes (value type):

    let x = 10;
    let y = x;
    x = 20;
    console.log(x, y);

Referência ou apontamento de memória:

    let x = { value: 10 };
    let y = x;
    x.value = 20;
    console.log(x, y);

# Document Object Model - DOM

O DOM pode ser definido uma representação em memória da página web carregada no navegador. É através deste objeto que o javascript consegue manipular conteúdo e estrutura além de definir comportamentos para diferentes eventos.

Importante observar que o objeto DOM precisa estar carregado antes de quaisquer comandos JavaScript. Por esta razão, é necessário definir ou referenciar seus scripts no final do documento html, imediatamente antes de fechar a tag body.

Você também pode verificar se o DOM já foi carregado utilizando a propriedade readyState do objeto document e definir um método para ser executado quando o evento onload for disparado sinalizando o carregamento concluído. Veja o exemplo abaixo:

    if(document.readyState === "complete") {
        // DOM carregado e pronto para uso!
    } else {
        window.addEventListener("onload", function () {/* ... */}, false);
        // ou document.addEventListener("DOMContentLoaded", function () {/* ... */}, false);
    }

O código acima pode ser colocado na tag head do documento html ou em qualquer posição porque estamos verificando o carregamento e definindo um método a ser disparado quando o evento onload ocorrer. 

# Programação Orientada a Objetos 

A programação orientada a objetos é um conceito ou abordagem de desenvolvimento de software, uma maneira de produzir software que cria objetos com características e comportamentos que simulam entidades e elementos do mundo real. 


# Princípios

O livro Design Patterns: Elements of Reusable Object-Oriented Software (de Gang of Four) definiu dois princípios fundamentais:

* Programe para interface e não para implementação
* Composição de objetos preferencialmente a herança

Além dos princípios fundamentais relacionados acima, existem diferentes princípios de design.

1. Identify the aspects of your code that vary and separate them from what stays the same.
2. Program to an interfce, not an implementation.
3. Favor composition over inheritance.
4. Strive for loosely coupled designs
5. Classes should be open for extension but closed for modification.
6. A class should have only one reason to change.

# Benefícios

A Programação Orientada à Objetos possibilita organizar sua solução em entidades com comportamentos e características próprias que se relacionam e interagem de maneira equivalente ao seu comportamento no mundo real.

As entidades do seu sistema representam e abstraem os diferentes perfis e papéis, assim como sua interação. Esta é uma abordagem recomendada para diversos tipos de sistemas incluindo aplicações de linha de negócios, jogos, e aplicativos.

Esta abordagem soluciona a interdependência entre funções que produzia códigos denominados frequentemente de "espagueti" que se tornavam rapidamente complexos e ineficientes quando a aplicação demandava novas funcionalidades.

# Conceitos 

* Encapsulation ou encapsulamento
* Abstraction ou abstração
* Inheritance ou herança
* Polymorphism ou polimorfismo


# Objetos Literais

Objetos Literais podem conter propriedades e métodos (funções) mas caso seja necessário haver outros objetos do mesmo tipo em uma aplicação seria necessário criar múltiplas cópias do mesmo e isto causaria retrabalho no caso de alterações além de aumentar a chance de bugs para o método draw do exemplo. Por esta razão, este tipo de objeto é frequentemente utilizado apenas para passar valores em propriedades.

    const circle = {
        radius: 1,
        location: {
            x: 1,
            y: 1
        },
        draw: function {
            console.log('draw!');
        }
    };
    circle.draw();

### Objetos criados com Factories e Constructors

Factory Function

    function createCircle(radius) {
        return {
            radius: radius,
            draw: function() {
                console.log('draw');
            }
        }
    }
    const circulo1 = createCircle(1);

Constructor Function

    function Circle(radius) {
        this.radius = radius;
        this.draw = function() {
            console.log('draw');
        }
    }
    const circulo2 = new Circle(1); 


## Abstração e Encapsulamento

Ao esconder a propriedade indice e o método multiplicarIndiceRadius, estamos fazendo uso do princípio da Abstração. E ao agrupar todos os elementos que compõem o objeto Circle em uma unidade, utilizamos o princípio do Ecapsulamento.

Utilize o princípio da Abstração (Abstraction) para ocultar os detalhes da implementação e disponibilizar apenas o principal. Veja a criação de uma propriedade privada utilizando como base o exemplo anterior.

    function Circle(radius) {            
        // props públicas
        this.radius = radius;
        this.draw = executaDraw;
        // props privadas
        var indice = 1.2;
        var calculaIndice = executaCalculoIndice;
        ///////////////////////////////////////
        function executaCalculoIndice(r) {
            return r * indice;
        }
        function executaDraw() {
            console.log(calculaIndice(this.radius));
        }
    }
    const circulo2 = new Circle(1); 
    circulo2.draw();

No código ilustrado acima, é relevante observar que precisamos passar a propriedade this.radius como parâmetro para o método privado calculaIndice que não pode utilizar diretmente as propriedades públicas do objeto.

Em objetos, as variáveis são chamdas de propriedades e função é método. A construção de uma unidade com características e comportamentos relacionados é denominado Encapsulamento.

Demonstração: comparação entre o paradigma da programação estruturada com a orientada a objetos

O código abaixo utiliza uma abordagem estruturada (não orientada a objetos) com a declaração de algumas variáveis e uma função sem qualquer vínculo ou regra que reforce o relacionamento entre estes elementos.

    let variacaoVelocidade = 10;
    let variacaoTempo = 2;

    function calcularAceleracao(variacaoVelocidade, variacaoTempo) {
        return variacaoVelocidade / variacaoTempo;
    }

Ao implementar o código acima utilizando a abordagem orientada à objetos, definimos um objeto denominado MovimentoUniformementeVariado que encapsula as mesmas propriedades e método ilustrados acima em uma unidade denominado objeto.

    let MovimentoUniformementeVariado = {
        variacaoVelocidade: 10;
        variacaoTempo: 2;
        calcularAceleracao: function() {
            return variacaoVelocidade / variacaoTempo;
        }
    }
    MovimentoUniformementeVariado.calcularAceleracao();


### Propriedades

JavaScript é uma linguagem dinâmica ou seja, não tipada, e como consequência, propriedades podem ser criadas ou removidas em tempo de execução. 

    function Circle(radius) {
        this.radius = radius;
        this.draw = function() {
            console.log('draw');
        }
    }
    const circulo1 = new Circle(10);

    // definição de propriedade utilizando sintaxe Dot Notation 
    circulo1.location = { x: 1 }; 
    console.log(circulo1);

    // definição de propriedade utilizando sintaxe Bracket Notation
    circulo1['location'] = { x: 1 };

A sintaxe Bracket Notation é útil para nomes de propriedades que utilizem caracteres especiais, espaços, utilizem sequências numeradas, entre outros.

Utilize o comando delete para remover propriedades de um objeto em tempo de execução. 

    delete circulo1.location;

Utilize for in para iterar nas propriedades de um objeto.

    for (let key in circulo1) {
        console.log(key, circulo[key]);
    }

Utilize typeof para verificar o tipo da propriedade.

    for (let key in circulo1) {
        if (typeof(circulo[key]) !== 'function')
        console.log(key, ccirculo[key]);
    }

Utilize Object.keys para obter a lista de propriedades de um objeto. Esta abordagem não permite diferenciar entre propriedades e métodos.

    const propriedades = Object.keys(circulo1);
    console.log(keys);

Utilize o operador in para verificar se uma propriedade existe no objeto.

    if ('radius' in circulo1)
        console.log('existe!');

Atributos de propriedades podem definir se a propriedade estará visível e exibida através do método for in descrito anteriormente, podem definir a possibilidade de alteração de valor, ou a possibilidade de exclusão da propriedade.

Os tributos podem ser obtidos através do método Object.getOwnPropertyDescriptor passando o prototype e a propriedade (ou método) que deseja visualizar os atributos. Veja o exemplo abaixo para exibir os atributos da propriedade (função) toString.

    let person = { name: 'eryx'};
    let objectBase = Object.getPrototypeOf(person);
    let descriptor = Object.getOwnPropertyDescriptor(objectBase, 'toString');
    console.log(descriptor);


## Prototype e Prototypical Inheritance 

Todo objeto (função) em JavaScript possui um objeto pai denominado prototype, cujas propriedades são herdadas e podem ser modificadas. Consequentemente, você pode utilizar novas propriedades para objetos conforme ilustração abaixo:

    function Circle(radius) {
        // Propriedades da Instância
        this.radius = radius;
    }

    // Propriedades Prototype
    Circle.prototype.draw = function() {
        console.log('draw');
    };

    // Sobrescrevendo a propriedade toString do objeto base Object
    Circle.prototype.toString = function() {
        return 'Círculo com raio ' + this.radius;
    }

    const c1 = new Circle(1);
    const c2 = new Circle(1);

Object.keys retornará somente as propriedades próprias ou da instância do objeto, aquelas definidas dentro do construtor utilizando this. 

Utilize for in par retornar todas as propriedades, incluindo de instância e prototype.

    // c1 => objeto Circle criado no exemplo anterior
    for (let key in c1) console.log(key);

Utilize hasOwnProperty para saber se uma propriedade é de instância.

    c1.hasOwnProperty('radius');

Evite modificar objetos que não sejam de sua autoria porque podem ocorrer conflitos e ou inconsistência do comportamento dos mesmos.
