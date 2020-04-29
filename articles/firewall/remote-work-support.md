---
title: Suporte ao trabalho remoto do firewall do Azure
description: Este artigo mostra como o Firewall do Azure pode dar suporte aos seus requisitos de força de trabalho remoto.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289635"
---
# <a name="azure-firewall-remote-work-support"></a>Suporte ao trabalho remoto do firewall do Azure

O Firewall do Azure é um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos de rede virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. 

## <a name="firewall-rules"></a>Regras de firewall

Você pode usar o Firewall do Azure para proteger o acesso de RDP de entrada do Virtual Desktop Infrastructure (VDI) à sua rede virtual do Azure usando [as regras de DNAT](rule-processing.md)do firewall do Azure. A área de trabalho virtual do Windows (WVD) não exige que você abra nenhum acesso de entrada para sua rede virtual. No entanto, você deve permitir um conjunto de conexões de rede de saída para as máquinas virtuais WVD que são executadas em sua rede virtual. Para obter mais informações, consulte [o que é a área de trabalho virtual do Windows?](../virtual-desktop/overview.md#requirements)

Você pode configurar esse acesso de saída usando regras de aplicativo de firewall do Azure. Para obter mais informações, consulte [tutorial: implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [área de trabalho virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/).