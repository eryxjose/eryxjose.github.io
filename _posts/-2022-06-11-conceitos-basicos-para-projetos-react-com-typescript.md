---
layout: post
author: Eryx
title: Conceitos básicos para projetos React com TypeScript
date: 11/06/2022
---

Inicie um projeto com o comando abaixo, substituindo '<app-name>' por um nome que faça sentido para seu projeto. Você pode utilizar letras minúsculas, hífen, underline e números. O parâmetro '--template typescript' disponibiliza o TypeScript no projeto. 

    $ npx create-react-app <app-name> --template typescript

Pode ser necessário remover 'create-react-app' caso haja uma versão antiga previamente instalada. Se for o caso, entre com o comando 'npm uninstall -g create-react-app' para desinstalar.

Depois que a geração do projeto estiver concluída, verifique que os arquivos de componentes iniciais do template padrão tem a extensão '.tsx' ao invés de '.jsx'. Observe que a extensão '.tsx' será usada somente quando houver código JavaScript Extension (JSX) no arquivo, caso contrário, utilize a extenção '.ts'.

Em seguida, remova todos os arquivos da pasta 'src' e então adicione um novo arquivo 'index.tsx'. Este arquivo será usado para renderizar os exemplos de componentes que serão criados neste tutorial.

O exemplo abaixo ilustra o código básico inicial para o arquivo 'index.tsx' apenas para testarmos o funcionamento do projeto. 

    import ReactDOM from 'react-dom'

    const App = () => {
        return (
            <div>
                <h1>Main Title</h1>
            </div>
        )
    }

    ReactDOM.render(<App />, document.querySelector("#root"))

Execute o comando 'npm start' no terminal na pasta onde o projeto foi criado. Se estiver tudo certo, o navegador padrão mostrará uma página em branco apenas com o título 'Main Title'.

Lembre que o elemento html '#root' referenciado no código acima, está no arquivo 'index.html' da pasta 'public'.

Exiba as 'Ferramentas de Desenvolvedor' do navegador e verifique que não há erros ou warnings.

Ao criar um projeto React com TypeScript habilitado, o código passa a ser verificado em relação aos tipos utilizados em Props, States e Event Handlers.

Em relação a tipagem de Props, primeiramente é importante lembrar que props é o nome dado as informações passadas de componentes pai para componentes filho. Para definir a tipagem de props esperadas ou requeridas em componentes filho é necessário definir uma interface contendo nome das props e respectivos tipos conforme exemplo a seguir:

    