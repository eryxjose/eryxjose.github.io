---
layout: post
author: Eryx
title: Revertendo uma string com JavaScript
date: 17/01/2021
---

Esta demonstração aborda diferentes maneiras de reverter strings com comentários sobre boas práticas.

## Configurações Básicas

Crie uma pasta para este tutorial, por exemplo, "reversestring". Abra o terminal na pasta criada e entre com o comando "npm init -y" para criar o package.json e desta forma configurar as bases para executar aplicações nodejs.

    $ npm init -y

Disponibilize o módulo Jest em sua aplicação. O parâmetro --save-dev é usado para configurar este módulo para uso local (desenvolvimento).

    $ sudo npm i --save-dev jest

Veja mais sobre Jest em [Getting Started](https://jestjs.io/docs/en/getting-started).

## Utilizando reduce()

Crie o arquivo index.js e cole o código ilustrado a seguir. O código abaixo implementa um módulo JavaScript (ES2015) e utiliza a função reduce() para inverter a ordem dos caracteres passados no parâmetro str.

    function reverse(str) {
        return str.split('').reduce((rev, char) => char + rev, '');
    }

    module.exports = reverse;

O uso da função reduce() é recomenado porque demonstra conhecimento dos recursos mais modernos da linguagem.

Veja abaixo a versão mais extensa da função acima com a estrutura completa da Arrow Function passada como parâmetro para o reduce(). Repare o uso de chaves {} e o comando return que podedm ser omitidos.

    function reverse(str) {
        return str.split('').reduce((reversed, character) => {
            return character + reversed;
        }, '');
    }

Veja mais sobre o uso de reduce() em [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

## Testando

Crie o arquivo test.js e cole o código ilustrado abaixo.

    const reverse = require('./index');

    test('Reverse function exists', () => {
        expect(reverse).toBeDefined();
    });

    test('Reversed abcd string', () => {
        expect(reverse("abcd")).toBe("dcba");
    });

Entre com o comando jest no terminal para rodar todos os testes. O Jest identifica automaticamente e executa todos os testes encontrados.

    $ jest

Inclua o parâmetro --watchAll executar os testes automaticamente a cada alteração feita no código. 

    $ jest --watchAll
    
Neste caso, utilize Ctrl + C para interromper a execução automática dos testes.

## Utilizando for()

Outra maneira de implementar a funcionalidade para reverter strings é utilizar o comando "for".

Utilize preferencialmente "for of" ao invés do "for" tradicional. A sintaxe do "for" tradicional possui mais pontos de falha por fazer uso de diferentes elementos.

Veja abaixo o exemplo utilizando "for of".

    function reverse(str) {
        let reversed = '';
        for (let character of str) {
            reversed = character + reversed;
        }
        return reversed;
    }

Compare com a versão utilizando o "for".

    function reverse(str) {
        let reversed = '';
        for (var i = 0; i < str.length; i++) {
            reversed = str[i] + reversed;
        }
        return reversed;
    }

## Utilizando reverse()

O JavaScript possui um método reverse() que implementa a funcionalidade para reverter a ordem dos elementos de um array. Desta forma, você pode usar o comando split() para obter um array a partir da string passada como parâmetro, utilizar o comando reverse no array gerado e então utilizar o comando join() para transformar o array invertido novamente em uma string.

    function reverse(str) {
        return str
            .split('')
            .reverse()
            .join('');
    }

## Resumo

Este texto descreve as configurações iniciais para uma aplicação nodejs com recursos de teste habilitados utilizando o módulo Jest. Compara diferentes funções para implementar um algoritmo para inverter a ordem dos caracteres de uma string.


