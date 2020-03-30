---
title: Azure VMware Solution by CloudSimple - Configure tabelas e regras de firewall
description: Descreve como configurar tabelas e regras de firewall da Nuvem Privada para restringir o tráfego em sub-redes e VLANs.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244661"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configure tabelas de firewall e regras para nuvens privadas

As tabelas de firewall e as regras associadas permitem que você especifique restrições no tráfego para aplicar a sub-redes e VLANs específicos.

* Uma sub-rede pode ser associada a uma tabela de firewall.
* Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="add-a-new-firewall-table"></a>Adicione uma nova tabela de firewall

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **Tabelas de firewall**.
3. Selecione **Criar tabela de firewall**.

    ![Página VLAN/sub-rede](media/firewall-tables-page.png)

4. Digite um nome para a mesa.
5. Uma regra padrão para a tabela está listada. Clique **em Criar nova regra** para criar uma regra adicional. Consulte o procedimento a seguir para obter detalhes.
6. Clique **em Feito** para salvar a tabela de firewall.

> [!IMPORTANT]
> Você pode criar até duas tabelas de Firewall por Nuvem Privada.

## <a name="firewall-rules"></a>Regras de firewall

As regras do firewall determinam como o firewall trata tipos específicos de tráfego. A guia **Regras** para uma tabela de firewall selecionada lista todas as regras associadas.

![Tabela de regras do firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

1. Exibir as configurações para criar uma regra de firewall em qualquer uma dessas maneiras:
    * Clique **em Adicionar regra** ao criar uma tabela de firewall.
    * Selecione uma tabela de firewall específica na página **Tabelas de firewall > de rede** e clique em Criar nova regra de **firewall**.
2. Configure a regra da seguinte forma:
    * **Nome**. Dê um nome à regra.
    * **Prioridade**. Atribua prioridade à regra. Regras com números mais baixos são executadas primeiro.
    * **Tipo de tráfego**. Selecione se a regra é para tráfego privado em nuvem, Internet ou VPN (apátrida) ou para um endereço IP público (stateful).
    * **Protocolo**. Selecione o protocolo coberto pela regra (TCP, UDP ou qualquer protocolo).
    * **Direção**. Selecione se a regra é para tráfego de entrada ou saída. Você deve definir regras separadas para tráfego de entrada e saída.
    * **Ação**. Selecione a ação a ser tomada se a regra corresponder (permitir ou negar).
    * **Fonte**. Especifique as fontes abrangidas pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Alcance de porta de origem**. Especifique o intervalo de portas sujeitas à regra.
    * **Direção**. Selecione entrada ou saída.
    * **Destino**. Especifique os destinos cobertos pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Alcance de porta de origem**. Especifique o intervalo de portas sujeitas à regra.

    ![Tabela de firewall adicionar regra](media/firewall-rule-create.png)

3. Clique **em Feito** para salvar a regra e adicioná-la à lista de regras para a tabela de firewall.

> [!IMPORTANT]
> Cada tabela de Firewall pode ter até 10 regras de entrada e 20 regras de saída. Esses limites podem ser aumentados [entrando em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Anexar VLANs/sub-redes

Depois de definir uma tabela de firewall, você pode especificar as sub-redes que estão sujeitas às regras na tabela.

1. Na página**Tabelas** de firewall de **rede,** > selecione uma tabela de firewall.
2. Abra a **guia VLANs/Subnet anexada.**
3. Clique **em Anexar a uma VLAN/Sub-rede**.
4. Selecione a Nuvem Privada e a VLAN. O nome da sub-rede associada e o bloco CIDR são mostrados.
5. Clique em **Enviar**.
