---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629516"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se o armazenamento de dados estiver configurado de uma das maneiras a seguir, você precisará configurar um [tempo de execução de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar ao armazenamento de dados:

- O armazenamento de dados está localizado dentro de uma rede local, dentro de uma rede virtual do Azure, ou dentro da nuvem virtual privada da Amazon.
- O armazenamento de dados é um serviço de dados de nuvem gerenciado em que o acesso é restrito a IPs que estão na lista de permissões de firewall.
