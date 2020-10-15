---
title: Registrar o provedor de recursos da Solução VMware no Azure
description: Etapas para registrar o provedor de recursos da Solução VMware no Azure.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575734"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Para usar a Solução VMware no Azure, você precisa primeiro registrar o provedor de recursos com sua assinatura.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Como alternativa, você pode usar a GUI para registrar o provedor de recursos **Microsoft.AVS**.  Para saber mais, confira o artigo [Registrar provedor de recursos e tipos](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).  
