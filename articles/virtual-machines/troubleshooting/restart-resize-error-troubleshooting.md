---
title: Problemas de reinicialização ou redimensionamento da VM no Azure | Microsoft Docs
description: Solução de problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma Máquina Virtual existente no Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965619"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Solucionar problemas de implantação ao reiniciar ou redimensionar uma VM Windows existente no Azure
Ao tentar iniciar uma VM (Máquina Virtual) do Azure parada ou redimensionar uma VM do Azure existente, o erro comum encontrado é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou quando não dá suporte ao tamanho de VM solicitado.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Coletar logs de atividades
Para iniciar a solução de problemas, colete os logs de atividades para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo:

[Exibir operações de implantação](../../azure-resource-manager/templates/deployment-history.md)

[Exibir logs de atividades para gerenciar recursos do Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: erro ao iniciar uma VM parada
Você tenta iniciar uma VM parada, mas ocorre uma falha de alocação.

### <a name="cause"></a>Causa
Deve-se tentar fazer a solicitação de início da VM parada no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### <a name="resolution"></a>Resolução
* Parar todas as VMs no conjunto de disponibilidade e, em seguida, reiniciar cada VM.
  
  1. Clique em **Grupos de recursos** > *seu grupo de recursos* > **Recursos** > *seu conjunto de disponibilidade* > **Máquinas Virtuais** > *sua máquina virtual* > **Parar**.
  2. Depois que todas as VMs pararem, selecione cada uma das VMs paradas e clique em Iniciar.
* Repita a solicitação de reinicialização mais tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: erro ao redimensionar uma VM existente
Você tenta redimensionar uma VM existente, mas ocorre uma falha de alocação.

### <a name="cause"></a>Causa
Deve-se tentar fazer a solicitação de redimensionamento da VM no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não dá suporte ao tamanho de VM solicitado.

### <a name="resolution"></a>Resolução
* Repita a solicitação com um tamanho de VM menor.
* Se o tamanho da VM solicitada não puder ser alterado:
  
  1. Pare todas as VMs no conjunto de disponibilidade.
     
     * Clique em **Grupos de recursos** > *seu grupo de recursos* > **Recursos** > *seu conjunto de disponibilidade* > **Máquinas Virtuais** > *sua máquina virtual* > **Parar**.
  2. Depois de parar todas as VMs, redimensione a VM desejada para um tamanho maior.
  3. Selecione a VM redimensionada, clique em **Iniciar**e inicie cada uma das VMs paradas.

## <a name="next-steps"></a>Próximos passos
Se você encontrar problemas ao criar uma nova VM do Windows no Azure, veja [Solucionar problemas de implantação com a criação de uma nova máquina virtual do Windows no Azure](../windows/troubleshoot-deployment-new-vm.md).

