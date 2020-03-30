---
title: Latência de conexão de usuário do Windows Virtual Desktop - Azure
description: Latência de conexão para usuários do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128182"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determine a latência da conexão do usuário no Windows Virtual Desktop

O Windows Virtual Desktop está disponível globalmente. Os administradores podem criar máquinas virtuais (VMs) em qualquer região do Azure que quiserem. A latência da conexão varia dependendo da localização dos usuários e das máquinas virtuais. Os serviços de Desktop Virtual do Windows serão continuamente lançados para novas geografias para melhorar a latência. 
 
A [ferramenta Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) pode ajudá-lo a determinar a melhor localização para otimizar a latência de suas VMs. Recomendamos que você use a ferramenta a cada dois ou três meses para garantir que a localização ideal não tenha sido alterada à medida que o Windows Virtual Desktop for lançado para novas áreas. 

## <a name="azure-traffic-manager"></a>Gerenciador de Tráfego do Azure

O Windows Virtual Desktop usa o Azure Traffic Manager, que verifica a localização do servidor DNS do usuário para encontrar a instância de serviço do Windows Virtual Desktop mais próxima. Recomendamos que os admins revisem a localização do servidor DNS do usuário antes de escolher o local para as VMs.

## <a name="next-steps"></a>Próximas etapas

- Para verificar a melhor localização para obter a latência ideal, consulte a [ferramenta Estimativa de Experiência de Área de Trabalho Virtual](https://azure.microsoft.com/services/virtual-desktop/assessment/)do Windows .
- Para planos de preços, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Para começar com a implantação do Windows Virtual Desktop, confira [nosso tutorial](tenant-setup-azure-active-directory.md).