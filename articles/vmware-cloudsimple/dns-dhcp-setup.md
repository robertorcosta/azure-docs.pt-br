---
title: Solução Azure VMware by CloudSimple - Configure dNS de carga de trabalho e DHCP para nuvem privada
description: Descreve como configurar DNS e DHCP para aplicativos e cargas de trabalho em execução em seu ambiente CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024680"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Configure aplicativos e cargas de trabalho DNS e DHCP em sua Nuvem Privada CloudSimple

Aplicativos e cargas de trabalho em execução em um ambiente de Nuvem Privada exigem resolução de nomes e serviços DHCP para pesquisa e atribuição de endereçoIP.  Uma infra-estrutura DHCP e DNS adequada é necessária para fornecer esses serviços.  Você pode configurar uma máquina virtual para fornecer esses serviços em seu ambiente Private Cloud.  

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de portas distribuídas com VLAN configurado
* Configuração de rota para servidores DNS no local ou na Internet
* Modelo de máquina virtual ou ISO para criar uma máquina virtual

## <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseado em Linux

O Linux oferece vários pacotes para configurar servidores DNS.  Aqui está um [exemplo de configuração da DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) com instruções para configurar um servidor BIND Bind de código aberto.

## <a name="windows-based-setup"></a>Configuração baseada no Windows

Esses tópicos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.

* [Servidor Windows como Servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Servidor Windows como Servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
