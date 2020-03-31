---
title: Trabalhando com VMs e NSGs no Azure Bastion
description: Este artigo descreve como incorporar o acesso NSG com o Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087261"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Trabalhando com acesso NSG e Bastião Azure

Ao trabalhar com o Azure Bastion, você pode usar os NSGs (Network Security Groups). Para obter mais informações, consulte [Grupos de segurança](../virtual-network/security-overview.md). 

![Arquitetura](./media/bastion-nsg/nsg-architecture.png)

Neste diagrama:

* O host Bastion é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador HTML5.
* O usuário navega até a máquina virtual do Azure para RDP/SSH.
* Integração de conexão - Sessão RDP/SSH de um único clique dentro do navegador
* Nenhum IP público é necessário na VM do Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede

Esta seção mostra o tráfego de rede entre o usuário e o Azure Bastion e através de VMs de destino em sua rede virtual:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

O Azure Bastion é implantado especificamente na AzureBastionSubnet.

* **Tráfego de ingress:**

   * **Tráfego de ingress da internet pública:** O Azure Bastion criará um IP público que precisa da porta 443 habilitada no IP público para o tráfego de entrada. A porta 3389/22 NÃO deve ser aberta na AzureBastionSubnet.
   * **Inserção de tráfego do avião de controle Azure Bastion:** Para controlar a conectividade do avião, habilite a entrada da porta 443 na tag de serviço **GatewayManager.** Isso permite que o plano de controle, ou seja, o Gateway Manager seja capaz de falar com o Azure Bastion.

* **Tráfego de saída:**

   * **Tráfego de saída para as VMs:** O Azure Bastion atingirá as VMs alvo por IP privado. Os NSGs precisam permitir o tráfego de saída para outras sub-redes VM de destino para as portas 3389 e 22.
   * **Egress Traffic para outros pontos finais públicos em Azure:** O Azure Bastion precisa ser capaz de se conectar a vários pontos finais públicos dentro do Azure (por exemplo, para armazenar registros de diagnósticos e registros de medição). Por essa razão, o Azure Bastion precisa de saída para 443 para a tag de serviço **AzureCloud.**

* **Sub-rede VM alvo:** Esta é a sub-rede que contém a máquina virtual de destino para a a que você deseja RDP/SSH.

   * **Tráfego de ingress do Azure Bastion:** O Azure Bastion alcançará a VM alvo por IP privado. As portas RDP/SSH (portas 3389/22, respectivamente) precisam ser abertas no lado vm de destino sobre o IP privado. Como uma prática recomendada, você pode adicionar o intervalo de endereços IP da Azure Bastion Subnet nesta regra para permitir que apenas bastion seja capaz de abrir essas portas nas VMs de destino em sua sub-rede VM de destino.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>Aplicar NSGs no AzureBastionSubnet

Se você criar e aplicar um NSG no ***AzureBastionSubnet,*** certifique-se de ter adicionado as seguintes regras em seu NSG. Se você não adicionar essas regras, a criação/atualização do NSG falhará:

* **Controle de conectividade do avião:** Entrada em 443 do GatewayManager
* **Registro de diagnósticos e outros:** Saída na 443 para AzureCloud. As tags regionais dentro desta tag de serviço ainda não são suportadas.
* **VM alvo:** Saída para 3389 e 22 para VirtualNetwork

Um exemplo de regra NSG está disponível para referência neste [modelo quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Bastion, consulte o [FAQ](bastion-faq.md).
