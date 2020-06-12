---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966380"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Será necessário configurar um [runtime de integração auto-hospedada](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar ao armazenamento de dados se este estiver configurado de uma das seguintes maneiras:

- O armazenamento de dados está localizado em uma rede local, na Rede Virtual do Azure ou na Amazon Virtual Private Cloud.
- O armazenamento de dados é um serviço de dados de nuvem gerenciado em que o acesso é restrito aos IPs adicionados à lista de permissões nas regras de firewall.
