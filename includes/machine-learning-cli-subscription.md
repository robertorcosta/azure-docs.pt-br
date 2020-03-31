---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296894"
---
Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. As informações `isDefault: true` de assinatura com são a assinatura ativada atualmente para comandos Azure CLI. Esta assinatura deve ser a mesma que contém o espaço de trabalho do Azure Machine Learning. Você pode encontrar o ID de assinatura do [portal Azure](https://portal.azure.com) visitando a página de visão geral do seu espaço de trabalho. Você também pode usar o SDK para obter o ID de assinatura do objeto do espaço de trabalho. Por exemplo, `Workspace.from_config().subscription_id`.
    
Para selecionar outra assinatura, use o comando [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) com a ID da assinatura para a qual alternar. Para obter mais informações sobre a seleção de assinaturas, consulte [Usar várias assinaturas do Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).