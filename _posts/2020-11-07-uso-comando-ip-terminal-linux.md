---
layout: post
author: Eryx
title: Exemplos de uso do comando ip no terminal do linux
date: 07/11/2020
---


O comando ifconfig está sendo descontinuado e já está indisponível em algumas versões do Linux e dando lugar ao uso do comando ip.

Este texto contém descrições resumidas para ações comuns relacionadas a configurações de rede utilizando o comando ip.

## Exiba todas as interfaces de rede e respectivas configurações

    $ ip a

## Configure um endereço de IP estático (IPv4)

Abra o aquivo /etc/network/interfaces e adicione as informações (address, netmask e gateway). 

    auto eth0
    iface eth0 inet static
    
    # configurações adicionadas
    address 192.168.50.2
    netmask 255.255.255.0
    gateway 192.168.50.1

Em seguida reinicie o serviço de rede.

    $ sudo /etc/init.d/networking restart

## Atribua um endereço de IP para interface específica

As alterações abaixo serão perdidas quando o sistema reiniciar.

    $ sudo ip addr add 192.168.50.5 dev eth1

## Verifique a configuração de IP atual

    $ sudo ip addr show

## Remova um endereço de IP atribuído a uma interface

    $ sudo ip addr del 192.168.50.5/24 dev eth1

## Habilite/Desabilite uma interface de rede

    $ sudo ip link set eth1 up

    $ sudo ip link set eth1 down

## Verifique a tabela de roteamento

    $ sudo ip route show

## Adicione uma rota estática 

Utilize uma rota estática para evitar que o tráfego passe pelo gateway.

    $ sudo ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0

## Remova uma rota estática

    $ sudo ip route del 10.10.20.0/24

## Rota estática permanente

Para que a rota seja adicionada de maneira permanente, altere o arquivo /etc/network/interfaces conforme exemplo.

    auto eth0
    iface eth0 inet static
    address 192.168.50.2
    netmask 255.255.255.0
    gateway 192.168.50.100
    #########{Static Route}###########
    up ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0

E em seguida, reinicie o serviço de rede.

    $ sudo /etc/init.d/network restart

## Adicione um gateway padrão

    $ sudo ip route add default via 192.168.50.100


## Altere o endereço MAC em interfaces de rede

    # ip link set dev eth0 address 00:0c:29:33:4e:ac

## Altere o MTU da interface de rede

    # ip link set dev eth0 mtu 2000

## Habilitar/Desabilitar a flag de multicast

    # ip link set dev eth0 multicast on

## Habilite/Desabilite modo promíscuo

    # ip link set dev eth0 promisc on

## Habilite/Desabilite o protocolo ARP

    # ip link set dev eth0 arp on


