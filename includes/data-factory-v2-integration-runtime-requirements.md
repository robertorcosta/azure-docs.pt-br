---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544036"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se o armazenamento de dados estiver localizado dentro de uma rede local, em uma rede virtual do Azure ou na nuvem virtual privada da Amazon, você precisará configurar um [tempo de execução de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar a ele.

Se o seu armazenamento de dados for um serviço de dados de nuvem gerenciado, em que o acesso é restrito aos IPs que estão na lista de permissões de firewall, você pode usar o tempo de execução de integração do Azure e adicionar [seus IPs](../articles/data-factory/azure-integration-runtime-ip-addresses.md) a ela. 

Consulte [estratégias de acesso a dados](../articles/data-factory/data-access-strategies.md) para obter informações sobre os mecanismos de segurança de rede com suporte pelo data Factory para acessar armazenamentos de dados em geral.
