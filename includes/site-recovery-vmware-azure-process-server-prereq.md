---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566202"
---
Este artigo supõe que

1. A **VPN Site a Site** ou um **via expressa** já tiver sido estabelecida a conexão entre sua rede local e de rede Virtual do Azure.
2. Sua conta de usuário tem permissões para criar uma nova máquina virtual na assinatura do Azure que as máquinas virtuais foram failover em.
3. Sua assinatura tem um mínimo de 8 núcleos disponíveis para criar uma nova máquina virtual do servidor de processo.
4. Você tem o **senha do servidor de configuração** disponíveis.

> [!TIP]
> Certifique-se de que você seja capaz de conectar-se a porta 443 do servidor de configuração (em execução local) de rede Virtual do Azure que as máquinas virtuais foram failover em.
