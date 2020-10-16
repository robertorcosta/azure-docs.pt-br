---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131625"
---
| Mecanismo | Escopo |Limites | Nível de permissão com suporte |
|---|---|---|---|
| RBAC | Contas de armazenamento, contêineres. <br>Atribuições de função de RBAC entre recursos no nível de assinatura ou grupo de recursos. | 2000 atribuições de função RBAC em uma assinatura | Funções RBAC (internas ou personalizadas) |
| LCA| Diretório, arquivo |32 entradas de ACL (efetivamente 28 entradas de ACL) por arquivo e por diretório. As ACLs de acesso e padrão têm seu próprio limite de entrada de ACL 32. |Permissão de ACL|
