---
title: Soluções VMware do Azure (AVS)-configurar o DNS de carga de trabalho e o DHCP para a nuvem privada de AVS
description: Descreve como configurar o DNS e o DHCP para aplicativos e cargas de trabalho em execução no seu ambiente de nuvem privada de AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024680"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Configurar aplicativos e cargas de trabalho de DNS e DHCP em sua nuvem privada de AVS

Aplicativos e cargas de trabalho em execução em um ambiente de nuvem privada AVS exigem resolução de nomes e serviços DHCP para pesquisa e atribuição de endereço IP. Uma infraestrutura apropriada de DHCP e DNS é necessária para fornecer esses serviços. Você pode configurar uma máquina virtual para fornecer esses serviços em seu ambiente de nuvem privada de AVS. 

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de portas distribuídas com VLAN configurado
* Configuração de rota para servidores DNS locais ou baseados na Internet
* Modelo de máquina virtual ou ISO para criar uma máquina virtual

## <a name="linux-based-dns-server-setup"></a>Instalação do servidor DNS baseado em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS. Aqui está um [exemplo de configuração do DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) com instruções para configurar um servidor DNS de ligação de software livre.

## <a name="windows-based-setup"></a>Instalação baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor do Windows como um servidor DNS e como um servidor DHCP.

* [Servidor do Windows como servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server como servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
