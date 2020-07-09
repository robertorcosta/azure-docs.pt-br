---
title: Trabalhando com VMs e NSGs na bastiões do Azure
description: Este artigo descreve como incorporar o acesso NSG com a bastiões do Azure
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: charwen
ms.openlocfilehash: 1fc261c31a1190536f3128ed6472d9ca76dfce7e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112180"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Trabalhando com acesso NSG e bastiões do Azure

Ao trabalhar com a bastiões do Azure, você pode usar NSGs (grupos de segurança de rede). Para obter mais informações, consulte [grupos de segurança](../virtual-network/security-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

Neste diagrama:

* O host de bastiões é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador HTML5.
* O usuário navega para a máquina virtual do Azure para RDP/SSH.
* Conectar integração-sessão RDP/SSH de clique único dentro do navegador
* Nenhum IP público é necessário na VM do Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede

Esta seção mostra o tráfego de rede entre o usuário e a bastiões do Azure e para as VMs de destino em sua rede virtual:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

A bastiões do Azure é implantada especificamente para ***AzureBastionSubnet***.

* **Tráfego de entrada:**

   * **Tráfego de entrada da Internet pública:** A bastiões do Azure criará um IP público que precisa da porta 443 habilitada no IP público para o tráfego de entrada. A porta 3389/22 não precisa ser aberta no AzureBastionSubnet.
   * **Tráfego de entrada do plano de controle de bastiões do Azure:** Para conectividade do plano de controle, habilite a porta 443 de entrada da marca de serviço do **gatewaymanager** . Isso permite que o plano de controle, ou seja, o Gerenciador de gateway seja capaz de se comunicar com a bastiões do Azure.


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Entrada":::

* **Tráfego de saída:**

   * **Tráfego de saída para VMs de destino:** A bastiões do Azure alcançará as VMs de destino sobre o IP privado. O NSGs precisa permitir o tráfego de saída para outras sub-redes de VM de destino para a porta 3389 e 22.
   * **Tráfego de saída para outros pontos de extremidade públicos no Azure:** A bastiões do Azure precisa ser capaz de se conectar a vários pontos de extremidade públicos no Azure (por exemplo, para armazenar logs de diagnóstico e logs de medição). Por esse motivo, a bastiões do Azure precisa de saída para 443 para a marca de serviço **AzureCloud** .


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Saída":::

### <a name="target-vm-subnet"></a>Sub-rede VM de destino
Essa é a sub-rede que contém a máquina virtual de destino para a qual você deseja RDP/SSH.

   * **Tráfego de entrada da bastiões do Azure:** A bastiões do Azure alcançará a VM de destino sobre o IP privado. As portas RDP/SSH (portas 3389/22, respectivamente) precisam ser abertas no lado da VM de destino sobre o IP privado. Como prática recomendada, você pode adicionar o intervalo de endereços IP da sub-rede de bastiões do Azure nesta regra para permitir que somente a bastiões possa abrir essas portas nas VMs de destino em sua sub-rede de VM de destino.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a bastiões do Azure, consulte as [perguntas frequentes](bastion-faq.md).
