---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529374"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se o armazenamento de dados estiver localizado dentro de uma rede local, em uma rede virtual do Azure ou na Amazon Virtual Private Cloud, você precisará configurar um [runtime de integração auto-hospedada](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar a ele.

Se o armazenamento de dados for um serviço de dados de nuvem gerenciado, você poderá usar o Azure Integration Runtime. Se o acesso for restrito aos IPs que estão na lista de permissões nas regras de firewall, você poderá optar por adicionar [IPs do Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) à lista. 

Para obter mais informações sobre os mecanismos de segurança de rede e as opções compatíveis com o Data Factory, consulte [Estratégias de acesso a dados](../articles/data-factory/data-access-strategies.md).
