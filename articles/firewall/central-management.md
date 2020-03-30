---
title: Gerenciamento central do Firewall do Azure
description: Conheça o gerenciamento central do Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444544"
---
# <a name="azure-firewall-central-management"></a>Gerenciamento central do Firewall do Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Se você gerenciar vários firewalls, você sabe que a mudança contínua das regras de firewall dificulta a sua manutenção em sincronia. As equipes centrais de TI precisam de uma maneira de definir políticas de firewall base e aplicá-las em várias unidades de negócios. Ao mesmo tempo, as equipes de DevOps querem criar suas próprias políticas locais de firewall derivadas para uma melhor agilidade.

A visualização do Azure Firewall Manager pode ajudar a resolver esses problemas.


## <a name="azure-firewall-manager-preview"></a>Visualização do Azure Firewall Manager

O Azure Firewall Manager Preview é um serviço de gerenciamento de segurança de rede que fornece política de segurança central e gerenciamento de rotas para perímetros de segurança baseados em nuvem. Facilita que as equipes de TI da Enterprise definam centralmente as regras de nível de rede e de aplicativos para filtragem de tráfego em várias instâncias do Firewall Azure. Você pode abranger diferentes regiões e assinaturas do Azure no hub e falar arquiteturas para governança e proteção de tráfego. Ele também fornece aos DevOps uma melhor agilidade com políticas locais de segurança de firewall derivadas que são implementadas entre as organizações.

### <a name="firewall-policy"></a>Política de firewall

Uma diretiva de Firewall é um recurso do Azure que contém coleções de regras de NAT, rede e aplicativos e configurações de Inteligência de Ameaças. É um recurso global que pode ser usado em várias instâncias do Firewall Azure em *Hubs Virtuais Protegidos* e *Redes Virtuais do Hub.* Novas políticas podem ser criadas do zero ou herdadas das políticas existentes. A herança permite que os DevOps criem políticas locais de firewall em cima da política de base exigida pela organização. As políticas funcionam entre regiões e assinaturas.
 
Você pode criar políticas de firewall e associações com o Azure Firewall Manager. No entanto, você também pode criar e gerenciar uma política usando API REST, modelos, PowerShell do Azure e CLI. Depois de criar uma diretiva, você pode associá-la a um firewall em um hub WAN virtual, tornando-o um *Hub Virtual Protegido* e/ou um firewall em uma rede virtual tornando-a Hub Virtual *Network*.

### <a name="pricing"></a>Preços

As políticas são cobradas com base em associações de firewall. Uma apólice com associação de firewall zero ou um é gratuita. Uma apólice com várias associações de firewall é cobrada a uma taxa fixa. Para obter mais informações, consulte [os preços do Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Parceiros de Gerenciamento de Firewall do Azure

As seguintes soluções líderes de terceiros suportam o gerenciamento central do Azure Firewall usando APIs padrão do Azure REST. Cada uma dessas soluções tem suas próprias características e características únicas:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Orca tufina](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Firewall Manager Preview, consulte [O que é o Azure Firewall Manager Preview?](../firewall-manager/overview.md)