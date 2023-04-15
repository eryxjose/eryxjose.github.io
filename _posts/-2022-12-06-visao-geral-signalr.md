---
layout: post
author: Eryx
title: Visão Geral SignalR
date: 06/12/2022
---

SignalR oferece recursos para comunicação em tempo real para aplicações utilizando diferentes tecnologias de transporte para conexão entre cliente e servidor, incluindo WebSockets, Server-sent Events, e Long Pooling. Ideal para aplicações de chat, dashboard e monitoramento.

SignalR implementa o conceito de Hub que disponibiliza um objeto Context que disponibiliza uma ConnectionId para cada conexão estabelecida, UserIdentifier que obtém informações de id para usuários utilizando tokens jwt, User para representar a entidade UserPrincipal utilizada na autenticação .NET, GetHttpContext para obter acesso a querystrings, etc. E um objeto Clients que fornece acesso ao cliente conectado em sí, groups de clientes, o caller que fez a requisição de conexão, entre outros.

De maneira geral, o Hub permite que clientes possam executar métodos no servidor, e permite enviar notificações para todos os clientes conectados.

...

Configure uma aplicação React/TypeScript para se comunicar com a API SignalR inicialmente definindo uma interface, por exemplo, 'comment.ts'.

    export interface ChatComment {
        id: number;
        createdAt: Date;
        body: string;
        username: string;
        displayName: string;
        image: string;
    }

Crie uma store mobx específica para lógica de serviços de comunicação com a API com o nome 'commentStore.ts' com o código abaixo.

    import { HubConnection, HubConnectionBuilder, LogLevel } from "@microsoft/signalr";
    import { makeAutoObservable, runInAction } from "mobx";
    import { ChatComment } from "../models/comment";
    import { store } from "./store";

    export default class CommentStore {
        comments: ChatComment[] = [];
        hubConnection: HubConnection | null = null;

        constructor() {
            makeAutoObservable(this);
        }

        createHubConnection = (activityId: string) => {
            if (store.activityStore.selectedActivity) {
                this.hubConnection = new HubConnectionBuilder()
                    .withUrl(process.env.REACT_APP_CHAT_URL + '?activityId=' + activityId, {
                        accessTokenFactory: () => store.userStore.user?.token!
                    })
                    .withAutomaticReconnect()
                    .configureLogging(LogLevel.Information)
                    .build();

                this.hubConnection.start().catch(error => console.log('Error establishing the connection: ', error));

                this.hubConnection.on('LoadComments', (comments: ChatComment[]) => {
                    runInAction(() => {
                        comments.forEach(comment => {
                            comment.createdAt = new Date(comment.createdAt + 'Z');
                        })
                        this.comments = comments
                    });
                })

                this.hubConnection.on('ReceiveComment', (comment: ChatComment) => {
                    runInAction(() => {
                        comment.createdAt = new Date(comment.createdAt);
                        this.comments.unshift(comment)
                    });
                })
            }
        }

        stopHubConnection = () => {
            this.hubConnection?.stop().catch(error => console.log('Error stopping connection: ', error));
        }

        clearComments = () => {
            this.comments = [];
            this.stopHubConnection();
        }

        addComment = async (values: any) => {
            values.activityId = store.activityStore.selectedActivity?.id;
            try {
                await this.hubConnection?.invoke('SendComment', values);
            } catch (error) {
                console.log(error);
            }
        }
    }

Será necessário intalar o pacote npm para SignalR no projeto Client.

    $ npm install @microsoft/signalr

Adicione 'commentStore' em 'store.ts'.

    ...
    commentStore: CommentStore;
    ...


