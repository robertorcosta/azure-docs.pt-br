---
title: Exemplo de Script da CLI do Azure - Redefinir as credenciais de sua conta | Microsoft Docs
description: Use o script da CLI do Azure para redefinir as credenciais de sua conta e recuperar as configurações do app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 09c93e2d851bea22e9d54dde35398f36335eb896
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967584"
---
# <a name="cli-example-reset-the-account-credentials"></a>Exemplo de CLI: Redefinir as credenciais da conta

O script da CLI do Azure neste artigo mostra como redefinir as credenciais da sua conta e recuperar as configurações de app.config.

## <a name="prerequisites"></a>Prerequisites 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

```
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup 
 ```

## <a name="next-steps"></a>Próximas etapas

* [az ams](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Redefinir credenciais](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-reset-credentials)
