---
title: Registrar o provedor de recursos da Solução VMware no Azure
description: Etapas para registrar o provedor de recursos da Solução VMware no Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653153"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para usar a Solução VMware no Azure, você precisa primeiro registrar o provedor de recursos com sua assinatura. Para obter mais informações sobre os provedores de recursos, confira [Provedores e tipos de recursos do Azure](/azure/azure-resource-manager/management/resource-providers-and-types).

### <a name="azure-cli"></a>CLI do Azure 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Portal do Azure
 
1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **Todos os serviços** , digite **assinatura** e, em seguida, selecione **Assinaturas**.

1. Selecione a assinatura na lista de assinaturas para visualizá-la.

1. Selecione **Provedores de recursos** e insira **Microsoft. AVS** na pesquisa. 
 
1. Caso o provedor de recursos não esteja registrado, clique em **Registrar**.
