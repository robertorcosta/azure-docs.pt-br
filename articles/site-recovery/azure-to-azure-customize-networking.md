---
title: Personalize configurações de rede para uma VM failover | Microsoft Docs
description: Fornece uma visão geral das configurações de rede personalizadas para uma VM failover na replicação de VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292851"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizar as configurações de rede da VM do Azure de destino

Este artigo fornece orientações sobre a personalização das configurações de rede na máquina virtual (VM) alvo quando você está replicando e recuperando VMs do Azure de uma região para outra, usando [o Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Recursos de rede suportados

Você pode fornecer as seguintes configurações de recursos principais para a VM failover enquanto replica as VMs do Azure:

- [Balanceador de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) tanto para a sub-rede quanto para a NIC

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de planejar suas configurações laterais de recuperação com antecedência.
- Crie os recursos de rede com antecedência. Forneça-o como uma entrada para que o serviço de recuperação do site do Azure possa honrar essas configurações e garantir que o Failover VM adere a essas configurações.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalizar configurações de rede failover e testfailover

1. Ir para **Itens Replicados**. 
2. Selecione a VM Azure desejada.
3. Selecione **Computação e Rede** e selecione **Editar**. Observe que as configurações de configuração de NIC incluem os recursos correspondentes na origem. 

     ![Personalize as configurações de rede failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecione uma rede virtual de failover de teste. Você pode optar por deixá-lo em branco e selecionar um no momento do failover do teste.
5. A rede Failover é Selecionar **Editar** perto da NIC que deseja configurar. Na próxima lâmina que for aberta, selecione os recursos pré-criados correspondentes no failover de teste e no local de failover.

    ![Editar a configuração nic](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selecione **OK**.

A Recuperação do Site agora honrará essas configurações e garantirá que a VM no failover esteja conectada ao recurso selecionado através da NIC correspondente.

Quando você aciona o failover de teste via Plano de Recuperação, ele sempre perguntará à rede virtual Do Zure. Esta rede virtual será usada para failover de teste para as máquinas que não tiveram as configurações de failover de teste pré-configuradas.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="unable-to-view-or-select-a-resource"></a>Não é possível visualizar ou selecionar um recurso

Se você não puder selecionar ou visualizar um recurso de rede, passe pelas seguintes verificações e condições:

- O campo de destino de um recurso de rede é habilitado apenas se a VM de origem tiver uma entrada correspondente. Isso se baseia no princípio de que, para um cenário de recuperação de desastres, você gostaria de uma versão exata ou redimensionada da sua fonte.
- Para cada recurso de rede, alguns filtros são aplicados na lista suspensa para garantir que o VM failover possa se anexar ao recurso selecionado e a confiabilidade do failover seja mantida. Esses filtros são baseados nas mesmas condições de rede que teriam sido verificadas quando você configurou a VM de origem.

Validação do balanceador de carga interna:

- A Assinatura e região do balanceador de carga e a VM alvo devem ser as mesmas.
- A rede virtual associada ao balanceador de carga interna e à VM alvo deve ser a mesma.
- O SKU IP público da VM e o SKU do balanceador de carga interna devem ser os mesmos.
- Se a VM de destino estiver configurada para ser colocada em uma zona de disponibilidade, verifique se o balanceador de carga é redundante de zona ou parte de qualquer zona de disponibilidade. (Os balanceadores básicos de carga SKU não suportam zonas e não serão mostrados na lista de baixa neste caso.)
- Certifique-se de que o balanceador de carga interno tenha um pool back-end pré-criado e configuração front-end.

Endereço IP público:

- A Assinatura e região do IP público e a VM alvo devem ser as mesmas.
- O SKU IP público da VM e o SKU do balanceador de carga interna devem ser os mesmos.

Grupo de segurança de rede:
- A Assinatura e região do grupo de segurança da rede e a VM alvo devem ser as mesmas.


> [!WARNING]
> Se a VM de destino estiver associada a um conjunto de disponibilidade, então você precisa associar o IP público e o balanceador de carga interna do mesmo SKU com o do IP público e do balanceador de carga interna da outra VM no conjunto de disponibilidade. Se você não fizer isso, failover pode não ter sucesso.
