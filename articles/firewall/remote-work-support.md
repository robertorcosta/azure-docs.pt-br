---
title: Suporte de trabalho remoto do Azure Firewall
description: Este artigo mostra como o Azure Firewall pode suportar seus requisitos remotos de força de trabalho.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289635"
---
# <a name="azure-firewall-remote-work-support"></a>Suporte de trabalho remoto do Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerenciado e baseado em nuvem que protege os recursos de rede virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. 

## <a name="firewall-rules"></a>Regras de firewall

Você pode usar o Azure Firewall para proteger o acesso de RDP de entrada da infra-estrutura de desktop virtual (VDI) à sua rede virtual Azure usando [as regras do DNAT](rule-processing.md)do Firewall do Azure . O Windows Virtual Desktop (WVD) não exige que você abra qualquer acesso de entrada à sua rede virtual. No entanto, você deve permitir um conjunto de conexões de rede de saída para as máquinas virtuais WVD que são executadas em sua rede virtual. Para obter mais informações, consulte [O que é o Windows Virtual Desktop?](../virtual-desktop/overview.md#requirements)

Você pode configurar esse acesso de saída usando as regras do aplicativo Azure Firewall. Para obter mais informações, consulte [Tutorial: Implante e configure o Firewall Do Azure usando o portal Azure](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).