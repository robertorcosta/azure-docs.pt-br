---
title: Gerenciamento central do firewall do Azure
description: Saiba mais sobre o gerenciamento central do Gerenciador de firewall do Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444544"
---
# <a name="azure-firewall-central-management"></a>Gerenciamento central do firewall do Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Se você gerencia vários firewalls, sabe que a alteração contínua das regras de firewall dificulta a sua manutenção. As equipes de ti centrais precisam de uma maneira de definir as políticas básicas de firewall e imaplicá-las em várias unidades de negócios. Ao mesmo tempo, as equipes de DevOps desejam criar suas próprias políticas de firewall derivadas locais para melhorar a agilidade.

A versão prévia do Gerenciador de firewall do Azure pode ajudar a resolver esses problemas.


## <a name="azure-firewall-manager-preview"></a>Versão prévia do Gerenciador de firewall do Azure

A versão prévia do Gerenciador de firewall do Azure é um serviço de gerenciamento de segurança de rede que fornece política de segurança central e gerenciamento de rotas para perímetros de segurança baseados em nuvem. Ele facilita para as equipes de ti empresariais definirem centralmente as regras de rede e de nível de aplicativo para a filtragem de tráfego entre várias instâncias do firewall do Azure. Você pode abranger diferentes regiões e assinaturas do Azure em arquiteturas de Hub e spoke para a governança e a proteção do tráfego. Ele também fornece uma agilidade DevOps melhor com políticas de segurança de firewall local derivadas que são implementadas entre organizações.

### <a name="firewall-policy"></a>Política de firewall

Uma política de firewall é um recurso do Azure que contém coleções de regra de aplicativo, rede e NAT e as configurações de inteligência de ameaça. É um recurso global que pode ser usado em várias instâncias do firewall do Azure em hubs virtuais e *redes virtuais* *protegidas* . Novas políticas podem ser criadas do zero ou herdadas de políticas existentes. A herança permite que o DevOps crie políticas de firewall locais sobre a política base obrigatória da organização. As políticas funcionam entre regiões e assinaturas.
 
Você pode criar uma política de firewall e associações com o Gerenciador de firewall do Azure. No entanto, você também pode criar e gerenciar uma política usando a API REST, os modelos, Azure PowerShell e a CLI. Depois de criar uma política, você pode associá-la a um firewall em um hub de WAN virtual, tornando-o um *Hub virtual seguro* e/ou um firewall em uma rede virtual que torne a *rede virtual do Hub*de ti.

### <a name="pricing"></a>Preços

As políticas são cobradas com base nas associações de firewall. Uma política com zero ou uma associação de firewall é gratuita. Uma política com várias associações de firewall é cobrada a uma taxa fixa. Para obter mais informações, consulte [preços do Gerenciador de firewall do Azure](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Parceiros de gerenciamento de firewall do Azure

As soluções de terceiros líderes a seguir dão suporte ao gerenciamento central do firewall do Azure usando as APIs REST do Azure padrão. Cada uma dessas soluções tem suas próprias características e recursos exclusivos:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Guardião de segurança de nuvem Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a versão prévia do Gerenciador de firewall do Azure, consulte [o que é a visualização do Azure firewall Manager?](../firewall-manager/overview.md)