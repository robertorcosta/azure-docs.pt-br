---
title: Quais são as opções de arquitetura do Azure Firewall Manager?
description: Compare e contraste usando a rede virtual hub ou arquiteturas de hub virtual seguras com o Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444570"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quais são as opções de arquitetura do Azure Firewall Manager?

O Azure Firewall Manager pode fornecer gerenciamento de segurança para dois tipos de arquitetura de rede:

- **centro virtual garantido**

   Um [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerenciado pela Microsoft que permite criar facilmente arquiteturas de hub e spoke. Quando as políticas de segurança e roteamento estão associadas a esse hub, é referida como um *[hub virtual seguro](secured-virtual-hub.md)*. 
- **hub rede virtual**

   Esta é uma rede virtual padrão do Azure que você cria e gerencia a si mesmo. Quando as políticas de segurança estão associadas a esse hub, ela é referida como uma *rede virtual de hub*. Neste momento, apenas a Política de Firewall do Azure é suportada. Você pode fazer redes virtuais com falas que contêm seus servidores e serviços de carga de trabalho. Você também pode gerenciar firewalls em redes virtuais autônomas que não são espiadas para qualquer conversa.

## <a name="comparison"></a>Comparação

A tabela a seguir compara essas duas opções de arquitetura e pode ajudá-lo a decidir qual é a certa para os requisitos de segurança da sua organização:


|  |**Rede virtual de hub**|**Hub virtual protegido**  |
|---------|---------|---------|
|**Recurso subjacente**     |Rede virtual|Virtual WAN Hub|
|**Hub & Spoke**     |Usa peering de rede virtual|Automatizado usando conexão de rede virtual do hub|
|**Conectividade on-prem**     |VPN Gateway até 10 Gbps e 30 conexões S2S; ExpressRoute|Mais vpn gateway escalável até 20 Gbps e 1000 conexões S2S; Rota Expressa|
|**Conectividade automatizada de filiais usando O DDWAN**      |Sem suporte|Com suporte|
|**Hubs por região**     |Múltiplas redes virtuais por região|Único Hub Virtual por região. Vários hubs possíveis com vários WANs virtuais|
|**Firewall Azure – vários endereços IP públicos**      |Fornecida pelo cliente|Gerado automaticamente. Para estar disponível pela GA.|
|**Zonas de disponibilidade de firewall do Azure**     |Com suporte|Não disponível na pré-visualização. Para estar disponível por GA|
|**Segurança avançada na Internet com segurança de terceiros como parceiros de serviço**     |Conectividade VPN estabelecida e gerenciada pelo cliente ao serviço de parceiros de escolha|Automatizado via fluxo de parceiros de segurança confiável e experiência de gerenciamento de parceiros|
|**Gerenciamento centralizado de rotas para direcionar o tráfego para o hub**     |Rota definida pelo usuário gerenciada pelo cliente|Suportado usando BGP|
|**Firewall do Aplicativo Web no Gateway de Aplicativo** |Suportado em Rede Virtual|Atualmente apoiado em rede de raios|
|**Solução de Virtualização de Rede**|Suportado em Rede Virtual|Atualmente apoiado em rede de raios|

## <a name="next-steps"></a>Próximas etapas

- Examine a [Visão geral sobre a implantação da Versão prévia do Gerenciador de Firewall do Azure](deployment-overview.md)
- Saiba mais sobre [Hubs virtuais seguros](secured-virtual-hub.md).