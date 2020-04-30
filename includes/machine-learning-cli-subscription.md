---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616828"
---
> [!TIP]
> Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. As informações de assinatura `isDefault: true` com o são a assinatura atualmente ativada para comandos de CLI do Azure. Essa assinatura deve ser a mesma que contém seu espaço de trabalho Azure Machine Learning. Você pode encontrar a ID da assinatura na [portal do Azure](https://portal.azure.com) visitando a página Visão geral do seu espaço de trabalho. Você também pode usar o SDK para obter a ID da assinatura do objeto de espaço de trabalho. Por exemplo, `Workspace.from_config().subscription_id`.
> 
> Para selecionar outra assinatura, use o `az account set -s <subscription name or ID>` comando e especifique o nome da assinatura ou a ID para a qual alternar. Para obter mais informações sobre a seleção de assinatura, consulte [usar várias assinaturas do Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).