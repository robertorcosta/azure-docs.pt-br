---
title: Quais são as opções de arquitetura do Gerenciador de firewall do Azure?
description: Compare e contraste usando a rede virtual do Hub ou arquiteturas de Hub virtual protegidas com o Gerenciador de firewall do Azure.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444570"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quais são as opções de arquitetura do Gerenciador de firewall do Azure?

O Gerenciador de firewall do Azure pode fornecer gerenciamento de segurança para dois tipos de arquitetura de rede:

- **Hub virtual protegido**

   Um [Hub de WAN virtual do Azure](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerenciado pela Microsoft que permite criar facilmente arquiteturas de Hub e spoke. Quando as políticas de segurança e roteamento são associadas a esse Hub, ele é conhecido como um *[Hub virtual protegido](secured-virtual-hub.md)*. 
- **rede virtual do Hub**

   Essa é uma rede virtual do Azure padrão que você cria e gerencia por conta própria. Quando as políticas de segurança são associadas a esse Hub, elas são chamadas de *rede virtual do Hub*. Neste momento, há suporte apenas para a política de firewall do Azure. Você pode emparelhar redes virtuais que contêm seus serviços e servidores de carga de trabalho. Você também pode gerenciar firewalls em redes virtuais autônomas que não são emparelhadas com nenhum spoke.

## <a name="comparison"></a>Comparação

A tabela a seguir compara essas duas opções de arquitetura e pode ajudá-lo a decidir qual delas é a mais adequada para os requisitos de segurança de sua organização:


|  |**Rede virtual de hub**|**Hub virtual protegido**  |
|---------|---------|---------|
|**Recurso subjacente**     |Rede virtual|Hub WAN virtual|
|**Hub & spoke**     |Usa o emparelhamento de rede virtual|Automatizado usando conexão de rede virtual do Hub|
|**Conectividade local**     |Gateway de VPN de até 10 Gbps e 30 conexões S2S; ExpressRoute|Gateway de VPN mais escalonável de até 20 Gbps e 1000 conexões S2S; Rota expressa|
|**Conectividade de ramificação automatizada usando SDWAN**      |Sem suporte|Com suporte|
|**Hubs por região**     |Várias redes virtuais por região|Único Hub virtual por região. Vários hubs possíveis com várias WANs virtuais|
|**Firewall do Azure – vários endereços IP públicos**      |Fornecida pelo cliente|Gerado automaticamente. Para estar disponível por GA.|
|**Zonas de Disponibilidade de firewall do Azure**     |Com suporte|Não disponível na visualização. Para estar disponível por GA|
|**Segurança avançada da Internet com segurança de terceiros como parceiros de serviço**     |Conectividade de VPN estabelecida e gerenciada pelo cliente para o serviço de parceiro de sua escolha|Automatizado por meio da experiência de gerenciamento de parceiros e do fluxo de parceiros de segurança confiável|
|**Gerenciamento de rota centralizado para rotear o tráfego para o Hub**     |Rota definida pelo usuário gerenciada pelo cliente|Com suporte usando BGP|
|**Firewall do Aplicativo Web no Gateway de Aplicativo** |Com suporte na rede virtual|Atualmente com suporte na rede spoke|
|**Solução de Virtualização de Rede**|Com suporte na rede virtual|Atualmente com suporte na rede spoke|

## <a name="next-steps"></a>Próximas etapas

- Examine a [Visão geral sobre a implantação da Versão prévia do Gerenciador de Firewall do Azure](deployment-overview.md)
- Saiba mais sobre [Hubs virtuais seguros](secured-virtual-hub.md).