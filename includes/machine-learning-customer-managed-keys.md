---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 1fde2947719079e411bc233bcce426feec8fa996
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631029"
---
> [!IMPORTANT]
> A instância de Cosmos DB é criada em um grupo de recursos gerenciado pela Microsoft em __sua assinatura__ , juntamente com os recursos necessários. Isso significa que você será cobrado por essa instância de Cosmos DB. O nome do grupo de recursos gerenciados é dado no formato `<AML Workspace Resource Group Name><GUID>`. Se seu espaço de trabalho do Azure Machine Learning usar um ponto de extremidade privado, uma rede virtual também será criada para a instância de Cosmos DB. Essa VNet é usada para proteger a comunicação entre Cosmos DB e Azure Machine Learning.
> 
> * Não __exclua o grupo de recursos__ que contém essa Cosmos DB instância ou qualquer um dos recursos criados automaticamente neste grupo. Se precisar excluir o grupo de recursos, Cosmos DB instância, etc., você deverá excluir o espaço de trabalho Azure Machine Learning que o utiliza. O grupo de recursos, Cosmos DB instância e outros recursos criados automaticamente são excluídos quando o espaço de trabalho associado é excluído.
> * O padrão [__Unidades de Solicitação__](../articles/cosmos-db/request-units.md) dessa conta do Cosmos DB é definido em __8000__ . __Não há suporte para a alteração desse valor__ .
> * Você __não pode fornecer sua própria VNet para uso com a instância de Cosmos DB__ que é criada. Você também __não pode modificar a rede virtual__ . Por exemplo, você não pode alterar o intervalo de endereços IP que ele usa.