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
> Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. A informação de assinatura com `isDefault: true` é a assinatura ativada no momento para os comandos da CLI do Azure. Essa assinatura precisa ser a mesma que contém o seu workspace do Azure Machine Learning. Encontre a ID da assinatura no [portal do Azure](https://portal.azure.com) acessando a página de visão geral do seu workspace. Use também o SDK para obter a ID da assinatura por meio do objeto de workspace. Por exemplo, `Workspace.from_config().subscription_id`.
> 
> Para selecionar outra assinatura, use o comando `az account set -s <subscription name or ID>` e especifique o nome ou a ID da assinatura para a qual deseja alternar. Para obter mais informações sobre a seleção da assinatura, confira [Usar várias assinaturas do Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).