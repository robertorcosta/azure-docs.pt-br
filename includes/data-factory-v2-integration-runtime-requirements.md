---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966380"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se o seu armazenamento de dados estiver configurado de uma das seguintes maneiras, você precisará configurar um [Runtime de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar a esse armazenamento de dados:

- O armazenamento de dados está localizado dentro de uma rede local, dentro da Rede Virtual Azure, ou dentro da Amazon Virtual Private Cloud.
- O armazenamento de dados é um serviço gerenciado de dados em nuvem onde o acesso é restrito a IPs listados nas regras de firewall.
