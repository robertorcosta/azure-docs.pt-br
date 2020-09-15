---
title: Registrar o provedor de recursos da Solução VMware no Azure
description: Etapas para registrar o provedor de recursos da Solução VMware no Azure.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512345"
---
Para usar a Solução VMware no Azure, você precisa primeiro registrar o provedor de recursos com sua assinatura.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Para conhecer outras maneiras de registrar o provedor de recursos, confira [Provedores e tipos de recursos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md).