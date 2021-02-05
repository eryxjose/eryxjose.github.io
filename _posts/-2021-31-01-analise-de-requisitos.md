---
layout: post
author: Eryx
title: Análise de Requisitos
date: 31/01/2021
---

A análise de requisitos produz uma relação de todas as funcionalidades esperadas para o sistema. Especificações definem detalhes técnicos relacionados a cada requisito.

Requisitos podem ser classificados em funcionais e não-funcionais. E os requisitos não-funcionais podem ainda ser classificados em: Requisitos de Produtos, Requisitos Organizacionais e Requisitos Externos.

Requisito de produto - restrições para o produto:

* O projeto será desenvolvido utilizando a linguagem Java

Requisito Organizacional - políticas da empresa, padrões, estilos e regras:

* Utilizar encriptação AES256
* Utilizar a metodologia SCRUM

Requisito Externo - Leis, regulamentações, tendências, etc.:

* Utilizar SSL para estar compatível com a lei XYZ.

## World Requirements Specifications Program Machine (WRSPM) Model

O modelo de referência WRSPM fornece uma visão dos relacionamentos entre requisitos, especificações e o mundo real.

Desenhe dois círculos com uma interseção entre eles. De um lado temos o ambiente do outro lado o sistema e no meio a interface de ligação entre os dois grupos. World e Requirements pertecem ao grupo ambiente enquanto Program e Machine fazem parte do sistema, e Specifications é a interseção e liga os dois grupos.

Quiosque para impressão de fotos.

Pré-suposições sobre o ambiente (World):

* Rede elétrica disponível
* Internet disponível
* Ambiente com temperatura controlada
* Usuários receberão fotos em tamanhos padrões pré-definidos
* Usuários serão capazes de ler prompts e tutoriais disponíveis 
* Haverá reposição frequente de materiais
* Usuários fornecerão cartões de crédito comuns no mercado
* Cartões de débito e crédito são amplamente utilizados

Requisitos (Requirements):

* Usuários podem conectar seu smartphone no sistema
* Usuários podem editar suas fotos
* O smartphone pode transferir fotos para impressão
* As fotos podem ser impressas pela máquina
* O quiosque pode receber pagamento
* Usuários podem alterar o idioma

Especificações (Specifications):

* O sistema utulizará tecnologia Bluetooth para conexão com smartphones
* O quiosque utiliza padrão de energia do mercado 
* Será utilizado protocolo seguro para conexões Bluetooth
* O sistema disponibilizará uma suite de tratamento e manipulação de fotos
* O sistema utilizará tecnologia de encriptação e conexão por chip para contactar APIs de transações financeiras
* O sistema utilizará dispositivo padrão para impressão de fotos

Exemplo de utilização do modelo World Requirement Specification para uma feature de um aplicativo fictício de jogo de Xadrez para monetizar.

Pré-suposições de ambiente (World Assumptions):

* Existem sistemas para pagamento via smartphone
* Existem maneiras de monetizar com apps
* A aplicação é extensível
* Haverá disponibilidade de internet para conexão com a instituição financeira
* Haverá disponibilidade de recursos de segurança necessários para transação
* O smartphone terá os recursos de processamento necessários para realizar a requisição
* Diferentes moedas poderão ser aceitas
* O pagamento via smartphone é legal

Requisitos:

* A feature PRO removerá anúncios para o usuário
* A feature aceitará pagamentos dos usuários
* A feature fornecerá os recursos PRO após a confirmação de transação bem sucedida
* A feature revogará recursos PRO quando a inscrição se tornar inválida
* A feature irá fornecer um botão para cancelar inscrição
* A feature irá extender o código existente
* A feature não ultrapassará 5MB de dados baixados do aplicativo
* A feature precisa completar a operação rapidamente

Especificações: 

* A feature será codificada em Java
* As transações serão feitas através da loja Android Play Store
* A validação de assinaturas será feita diariamente utilizando api do google
* Os anúncios AdMob serão desligados com a confirmação de sucesso para novas assinaturas
* A transação precisa acontecer em tempo menor que 500ms

