---
layout: post
author: Eryx
title: Visão Geral do AngularJS
date: 23/02/2019
---

AngularJS é um framework JavaScript para criar aplicações web.

 Possibilita criar tags ou componentes personalizados que expressam significado em si, por exemplo:

    <lista-de-letras>
        <item>A</item>
        <item>B</item>
        <item>C</item>
    </lista-de-letras>
    
O recursos de data binding e dependency injection eliminam muito do código que seria necessário para produzir o mesmo resultado utilizando jQuery ou JavaScript. 

O AngularJS ensina novos truques ao HTML através das diretivas utilizando data binding, estruturas de controle com capacidade de ocultar e exibir fragmentos DOM, suporte para formulários e validação, binding de eventos nos elementos DOM, agrupamento de elementos HTML para formar novos componentes.

O AngularJS foi construído especialmente para aplicações de negócios envolvendo muitas ações de CRUD. Aplicações com manipulação intensa de DOM, tais como jogos e editores gráficos não são recomendadas para serem feitas com este framework.

O framework utiliza e recomenda uma abordagem declarativa para construção de interfaces de usuário e conectar componentes enquanto o código imperativo é ideal para expressar lógica de negócios.

# Data binding

Data binding refere-se a sincronização automática de valores de propriedades criadas e mantidas pelo framework quando ocorrem alterações na view (html) ou no modelo de dados.

O modelo de dados ou model na prática contém um ou mais objetos (function) JavaScript criados com o padrão Constructor que define propriedades e métodos e não retorna valor.

O AngularJS possui uma etapa de compilação que cria vínculos entre o modelo de dados e elementos html de maneira que haverá uma sincronização automática quando houverem alterações nos valores das propriedades. Desta forma, o modelo de dados se torna a fonte única da verdade ou single source of truth para o estado da aplicação.

Neste modelo views são uma projeção do modelo de dados, portanto não pode haver nenhum vínculo entre controller e view, ou seja, o controller não pode conter código que manipule elementos html da view, ao invés disto, sua responsabilidade é criar as propriedades e métodos que serão utilizados na view.

    <div ng-app>
        <input ng-model='user.nome'>
        <p ng-show='user.nome'>
            Olá {{user.nome}}
        </p>  
    </div>

O código acima é um exemplo bem simples de utilização do AngularJS em que nenhum código JavaScript foi escrito para criar a funcionalidade de atualização automática do valor em {{ user.nome }} sempre que você digitar algo no elemento input. Isto ocorre porque o AngularJS registra a propriedade user.nome no objeto Scope (internamente) e atribui um $watch que será disparado sempre que a o valor da propriedade for alterado. Internamente a API $apply propaga as alterações feitas no modelo de dados e também pode ser utilizada para propagar alterações produzidas por códigos externos ao framework, por exemplo utilizando JavaScript ou jQuery. Neste caso os recursos de atualização do AngularJS não serão disparados automáticamente e será necessário utilizar o $apply. Veremos exemplos adiante.

# Expressões ou Expressions

Expressões são construções sintáticas semelhantes ao código JavaScript e utilizadas entre os símbolos {{ }} ou em resposta a eventos por exemplo, ng-click="minhaFuncao()".

Este recurso não oferece suprote a comandos de controle de fluxo ou loops, nem declaração de funções, entre outros. Veja uma lista das diferenças em https://docs.angularjs.org/guide/expression.

# Core Framework e Modules

O Módulo Principal ou Core Module do AngularJS está disponível para sua aplicação quando você inclui uma referência ao framework utilizando o atributo src da tag script conforme exemplo a seguir.

    <script src="/libs/angularjs/angular.min.js"></script>

É recomendado colocar todas as referências a arquivos de script (.js) no final da tag body do arquivo html para garantir que quaisquer ações sejam executados somente depois que todo conteúdo estiver carregado. Por exemplo:

    <!DOCTYPE html>
    <html lang="en" ng-app>
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Uso de Filters</title>
    </head>
    <body>
        
        <!-- Local recomendado referenciar arquivos .js -->
        <script src="/libs/angularjs/angular.min.js"></script>
    </body>
    </html>

Depois de referenciar o angularjs, você pode então começar a utilizar as diretivas do framework e construir suas próprias para atender a todas as necessidades da sua aplicação. Veja mais detalhes sobre diretivas na respectiva sessão em outro tópico deste documento.

Módulos são utilizados para definir um tipo de container com seus respectivos controllers, routes, factories e services.

Um módulo pode conter um elemento de configuração (Config) utilizado para definição de rotas (Routes) ou url's para diferentes views com seus respectivos controllers. Também podem ser definidos Filters, Directives, Factories (também Services, Providers, Values, Constant) e obviamente Controllers.

Conforme mensionado anteriormente, cada controller possui um objeto Scope vinculado e também disponível para a view que o referenciou. O controller produz um modelo de dados que pode ser acessado explicitamente com $scope ou utilizando controllerAs na view. Quando você utiliza controllerAs uma nova instância do controller é atribuída à variável definida. Veja.

    <div ng-controller="ConteudoPagina as vm">
        <input type="text" ng-model="termoFiltro">
        <ul>
            <li ng-repeat="cliente in vm.clientes | orderBy:'nome' | filter: termoFiltro">
                {{ cliente.nome }} {{ cliente.sobrenome }}
            </li>
        </ul>
    </div>

No exemplo acima, o AngularJS internamente executa algo semelhante ao código abaixo.

    vm = new ConteudoPagina();

Views contém ou são compostas por diretivas e Controllers que podem referenciar Factories (ou Services) que contém códigos de chamada assíncrona e funcionalidades independentes de controller ou seja, sem vínculo com um único controller específico podendo ser utilizada em múltiplos controllers.

A sintaxe básica para criação de um módulo AngularJS é:

    angular.module('minhaApp', [])

O método angular.module recebe dois parâmetros, o nome do módulo entre aspas e um array de módulos você quer utilizar. Um array vazio [] indica que não há dependência alguma.

Para referenciar o módulo criado acima para definir controllers, directives, etc., apenas omita o segundo parâmetro em relação ao código de anterior utilizado na definição do módulo.

    angular.module('minhaApp').controller(...)

# Controllers 

Controller em AngularJS é criado através de uma função ou objeto JavaScript desempenhando o papel de método construtor. Por padrão métodos construtores são utilizados para inicializar dados seja no lado cliente ou servidor. Em JavaScript utilizamos a variável this para definir as propriedades e métodos públicos do objeto. Veja o exemplo:

    function App1Controller () {
        this.clientes = [
            { nome: 'jose', sobrenome: 'martins'},
            { nome: 'joaquim', sobrenome: 'silva'},
            { nome: 'maria', sobrenome: 'coreto'}
        ];
    }

A função construtora acima define um array de objetos denominado clientes. Podemos fazer com que esta função seja um controller utilizando o método controller() do AngularJS conforme exemplo a seguir.
   
    angular
        .module('app7', [])
        .controller('ConteudoPagina', App1Controller);

Repare que utilizei nomes diferentes entre o nome ConteudoPagina e o nome da função construtora para reforçar o entendimento de cada uma mas o mais comum é utilizar o mesmo nome.

Também é relevante observar que não introduzimos o objeto $scope como argumento da função e esta tem sido a recomendação geral entre especialistas. Recomenda-se ainda que todo código esteja dentro de uma função IIFE para não incluir elementos no escopo global da aplicação.

    (function() {

        function App1Controller () {
            this.clientes = [
                { nome: 'jose', sobrenome: 'martins'},
                { nome: 'joaquim', sobrenome: 'silva'},
                { nome: 'maria', sobrenome: 'coreto'}
            ];
        }
        
        angular
            .module('app7', [])
            .controller('ConteudoPagina', App1Controller);
        
    }());


# Scope

O Scope é um objeto que representa o modelo de dados da aplicação. Podem haver múltiplos Scopes em uma aplicação AngularJS. Scopes são criados para controllers e diretivas e sua estrutura hierárquica tem relação com a estrutura DOM da aplicação e com o modelo de dados. Sua API fornece recursos para observar e propagar alterações do modelo de dados. Na prática existe um Scope principal (rootScope) e uma estrutura hierárquica que vai sendo construída a cada controller e diretiva criada ou instanciada.

É relevante observar que não é necessário na maioria das vezes utilizar o $scope explicitamente na função construtora porque o framework vincula o $scope ao controller no momento que você utiliza o comando angular.module('seu_modulo').controller('SeuController', [SeuController]). Você pode criar um objeto literal JavaScript normal utilizando this.nome_da_propriedade_ou_metodo e acessá-lo através da view utilizando um álias. Veja abaixo.

    <div ng-controller="ConteudoPagina as vm">
        <input type="text" ng-model="termoFiltro">
        <ul>
            <li ng-repeat="cliente in vm.clientes | orderBy:'nome' | filter: termoFiltro">
                {{ cliente.nome }} {{ cliente.sobrenome }}
            </li>
        </ul>
    </div>

A principal função dos controllers é preencher o Scope com um ou vários objetos de dados. 

O Scope é um componente do AngularJS disponibilizado para todo controller automaticamente mas você pode fazer uso explícito do objeto Scope inserindo $scope como argumento para o controller. Veja o exemplo a seguir.
    
    (function () {

        function User($scope) {
            
            this.user = {
                nome: 'eryx',
                sobrenome: 'guimaraes'
            };
            this.bye = function() {
                alert('Adeus: ' + this.user.nome);
            };

            $scope.scopeUser = this.user;
        }

        angular
            .module('app2', [])
            .controller('User', ['$scope', User]);
        
    }());

No código acima, temos a criação de uma função App1Controller cujo objetivo é exercer a atividade de construtor, semelhante ao que ocorre com classes na programação backend. O papel do método construtor de acordo com a orientação a objetos clássica é inicializar propriedades.

De maneira semelhante, o método construtor javascript, App1Controller() em nosso exemplo, utiliza this para definir propriedades públicas do objeto, neste caso, o array de objetos this.clientes. Vale reforçar que somente as propriedades e métodos definidos com this serão acessíveis nas views onde este controller for referenciado.

Para vincular a função construtora como um controller da aplicação (ou módulo) app7, utilize o método controller() exemplificado abaixo.

    angular.module('app7').controller('ConteudoPagina', App1Controller);

Relevante observar que o código acima, assume que o módulo app7 foi criado anteriormente em outro local uma vez que não inclui o array de dependências conforme exemplo anterior.

# Directives

Diretivas ou directives estendem as tags html existentes. A capacidade de criar novos elementos html personalizados é uma das capacidades mais importantes do framework que também disponibiliza uma extensa lista de diretivas por padrão em seu módulo principal (ng).

O exemplo mais básico de uso do AngularJS, ilustrado a seguir, apresenta a diretiva ng-app e ng-model para demonstrar o recurso de Databind. 

A diretiva ng-model cria uma propriedade nome que também é referenciada na interpolação que ocorre com a propriedade nome entre chaves duplas, e esta estrutura é formalmente chamada de Data Binding Expression.

    <div ng-app>
        <input ng-model='nome'>
        <p ng-show='nome'>
            Olá {{ nome }}
        </p>  
    </div>

É recomendado não utilizar lógica complexa dentro das chaves. 

Relevante observar que para evitar notificações de validadores de código, o HTML especifica um atributo data- que pode ser utilizado junto com o atributo ng- das diretivas. Veja o mesmo exemplo anterior com o uso de data- logo abaixo.

    <div data-ng-app>
        <input data-ng-model='nome'>
        <p data-ng-show='nome'>
            Olá {{nome}}
        </p>  
    </div>

Nota: O código acima refere-se ao arquivo /demos/demo1/index.html.

O exemplo abaixo, utiliza a diretiva ng-init para inicializar um array contendo nomes e depois o ng-repeat para listar os nomes.

    <div ng-init="nomes=['joão','maria','carolina','josé']">
        <ul>
            <li ng-repeat="nome in nomes">{{ nome }}</li>
        </ul>
    </div>

O próximo exemplo mostra o código HTML e JavaScript da pasta demo7 em arquivos individuais. Veja o código do arquivo index.html.

    <!DOCTYPE html>
    <html lang="en" ng-app="app7">
    <head></head>
    <body>
        <div ng-controller="ConteudoPagina as vm">
            <input type="text" ng-model="termoFiltro">
            <ul>
                <li ng-repeat="cliente in vm.clientes | orderBy:'nome' | filter: termoFiltro">
                    {{ cliente.nome }} {{ cliente.sobrenome }}
                </li>
            </ul>
        </div>
        <script src="/libs/angularjs/angular.min.js"></script>
        <script src="index.js"></script>
    </body>
    </html>

E a seguir o arquivo index.js contendo o código de definição do módulo e controller.

    (function() {

        function App1Controller () {
            this.clientes = [
                { nome: 'jose', sobrenome: 'martins'},
                { nome: 'joaquim', sobrenome: 'silva'},
                { nome: 'maria', sobrenome: 'coreto'}
            ];
        }
        
        angular
            .module('app7', [])
            .controller('ConteudoPagina', App1Controller);
        
    }());

Observe que utilizamos a diretiva ng-module para criar uma propriedade termoFiltro referenciada na propriedade filter dentro do loop pela coleção de registros com ng-repeat. O uso de Filters será explicado em detalhes adiante.

Veja um resumo sobre as diretivas que foram utilizadas nos exemplos acima. 

* ngApp ou ng-app

Utilize esta diretiva para referenciar o módulo principal da sua aplicação. Esta diretiva determina o elemento HTML root ou raiz que terá acesso aos recursos de sua aplicação. Também podemos dizer que define o escopo de sua aplicação.

Utilize apenas uma referência ngApp por aplicação. Para executar múltiplas aplicações em uma página html será necessário utilizar o recurso angular.bootstrap.

Não utilize ngApp com diretivas que fazem uso de transclusion (termo explicado posteriormente) por exemplo, ngIf, ngInclude e ngView.

Utilize o atributo opcional ngStrictDI para forçar a declaração 'use strict' em todas as funções da aplicação. Veja o exemplo a seguir.
    
    <div ng-app="ngAppStrictDemo" ng-strict-di>

* ngModel ou ng-model

Esta diretiva criar um vínculo de bind entre elementos de entrada de dados html (input, textarea, select, e controles personalizados) com propriedades no Scope. Caso a propriedade não exista, será criada automaticamente e adicionada ao Scope.

* ngInit ou ng-init

* ngShow ou ng-show

* Expressões {{ }} ou ng-bind

Estas são apenas algumas diretivas, visite o site da biblioteca para mais.

Outras possibilidades de uso para diretivas incluem:

Ex. 1

    <rating max="5" model="stars.average">
    </rating>

Ex. 2

    <tabs>
        <tab title="tab 1">...</tab>
        <tab title="tab 2">...</tab>
    </tabs>

Ex. 3

    <span tooltip="{{mensagens.mensagem1}}"></span>

# Diretivas Personalizadas ou Custom Directives

Diretivas também são utilizadas para reaproveitamento de funcionalidades. O exemplo a seguir foi criado para remover espaços duplicados em campos de texto de formulários.

    app.directive("removerEspacosRepetidos", function() {
        var link = function (scope, elem, attrs) {
            elem.on('blur', function() {
                elem.val(elem.val().replace(/  +/g, ' '));
            });
        };
        return {
            restrict: 'A',
            link: link
        }
    });

A relação e respectiva documentação de todas as diretivas do AngularJS estão disponíveis em (https://docs.angularjs.org/api). 

# Services (providers, factories, ...)

Serviços em AngularJS são objetos Singleton, há uma única instância por aplicação.

Utilize serviços para se comunicar com o backend ou fornecer métodos relacionados a lógica de negócios. 

O método service() retorna um objeto com propriedades definidas utilizando this.

    function UserService () {
        this.sayHello = function (name) {
            return 'Hello there ' + name;
        };
    }

    angular
        .module('app')
        .service('UserService', UserService);

Veja abaixo um exemplo de Controller com a injeção de dependência do serviço.

    function MainCtrl (UserService) {
        this.sayHello = function (name) {
            UserService.sayHello(name);
        };
    }

    angular
        .module('app')
        .controller('MainCtrl', MainCtrl);


# Filters


Filters são funcionalidades que modificam os dados para exibição sem alterar a informação original. Por exemplo, ordenar ou filtrar uma coleção, formatar letras maiúsculas ou minúsculas, formatar apresentação de data e hora, entre outros.

O código do arquivo /demos/demo5/index.html ilustra a ordenação e filtro.

    <body ng-init="clientes=[
            {id: 1,nome: 'josé',idade: 33},
            {id: 2,nome: 'maria',idade: 34},
            {id: 3,nome: 'carolina',idade: 25}
        ]">
        
        <input type="text" ng-model="termoFiltro">

        <ul>
            <li ng-repeat="cliente in clientes | orderBy:'nome' | filter: termoFiltro">
                {{ cliente.nome | uppercase }}
            </li>
        </ul>

        <script src="/libs/angularjs/angular.min.js"></script>
        
    </body>


# Referências

Ferramentas

* Batarang
* Yeoman
* Karma


UI Components

* AngularUI
* KendoUI (Pago)
* wijmo


Libraries

* Breeze
* Firebase
* SalesForce


Tutoriais e Recursos Online

* yearofmoo 

    https://www.yearofmoo.com/

* madewithangular - exemplos de sites criados com Angular.

    https://www.madewithangular.com/
    http://freezeprosoftware.com/news/top-famous-sites-built-with-angularjs.aspx#.XG4PrehKjIU
 
* Using Yeoman with AngularJS 

    https://www.airpair.com/js/using-angularjs-yeoman


Docs

* Documentação API https://docs.angularjs.org/api. 
* Styleguide oficial AngularJS https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md 


YouTube

* AngularJS Fundamentals In 60-ish Minutes  https://www.youtube.com/watch?v=i9MHigUZKEM

* Google I/O 2013 - Design Decisions in AngularJS  https://www.youtube.com/watch?v=HCR7i5F5L8c&feature=youtu.be&aGoogle

* AngularJS MTV Meetup: Best Practices (2012/12/11)  https://www.youtube.com/watch?v=ZhfUv0spHCY&feature=youtu.be&t=30m ***** com o criador do Angular


# Instalação

* Download da biblioteca 

Acesse o url abaixo e clique no botão Download AngularJS.

    https://angularjs.org/

* Instalação através do npm

Digite o comando abaixo no prompt de comando com a pasta do seu projeto selecionada.

    npm install angular@1.7.5

* Utilizando CDN

Inclua a referência abaixo em um atributo src da tag script.

    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.7.5/angular.min.js"></script>
    
* Utilizando o arquivo angular.min.js baixado

Inclua a referência na tag script.

    <script src="/libs/angularjs/angular.min.js"></script>

# Requisitos

Utilizei o Visual Studio Code disponível em https://code.visualstudio.com/download com versões para todos os sistemas operacionais.

Depois de instalar o VSCode, utilize a paleta extensões para instalar a extensão LiveServer que basicamente possibilita clicar com o botão direito em qualquer arquivo html e executá-lo.

Você também precisará do git (https://git-scm.com/downloads) e nodejs (https://nodejs.org/en/) instalados. Utilize os comandos abaixo para confirmar que as instalações foram feitas corretamente.

    git --version

O comando acima mostrará a versão instalada por exemplo: git version 2.15.1.windows.2

O comando abaixo retorna a versão do nodejs instalada.

    node --version

No Windows, a instalação do nodejs incluí automaticamente a instalação do gerenciador de pacotes npm. Verifique a versão do npm utilizando o comando abaixo.

    npm --version 

Para obter a versão mais atual do Node.js no Linux Ubuntu, é necessário primeiro adicionar o arquivo de pacotes (PPA) para o repositório nodesource.com e em seguida executar o comando de instalação.

    $ cd ~ 
    $ curl -sL https://deb.nodesource.com/setup_6.11.3 -o nodesource_setup.sh 
    $ sudo bash nodesource_setup.sh 
    $ sudo apt-get install nodejs 
    $ sudo apt-get install build-essential

## Extensões

 utilizado para instalação de extensões que fornecem funcionalidades específicas por exemplo a extensão LiveServer que habilita diretamente no editor VSCode o acesso aos seus recursos para servir páginas html, outro exemplo é a extensão emmet para html que já vem disponível no VSCode por padrão possibilitando que você crie uma tag ul com cinco li's com o código ilustrado abaixo.

    ul>li*5

Assim que você digita o código acima e pressiona um espaço, a extensão emmet entra em ação e cria sua lista. Veja outro exemplo.

    html:5

O código acima irá produzir toda estrutura html necessária para uma página senguindo os padrões modernos da versão 5 do HTML. 

O url a seguir ensina a utilizar o painel de extensões do VSCode. 

    https://code.visualstudio.com/docs/editor/extension-gallery.

