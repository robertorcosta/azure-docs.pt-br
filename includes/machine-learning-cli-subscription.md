---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616828"
---
> [!TIP]
> Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. As informações `isDefault: true` de assinatura com são a assinatura ativada atualmente para comandos Azure CLI. Esta assinatura deve ser a mesma que contém o espaço de trabalho do Azure Machine Learning. Você pode encontrar o ID de assinatura do [portal Azure](https://portal.azure.com) visitando a página de visão geral do seu espaço de trabalho. Você também pode usar o SDK para obter o ID de assinatura do objeto do espaço de trabalho. Por exemplo, `Workspace.from_config().subscription_id`.
> 
> Para selecionar outra assinatura, use o `az account set -s <subscription name or ID>` comando e especifique o nome da assinatura ou ID para mudar. Para obter mais informações sobre a seleção de assinaturas, consulte [Usar várias assinaturas do Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).