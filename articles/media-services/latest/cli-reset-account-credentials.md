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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 16eaf5ab9b0af97e18a7253d132516a16003d25b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489972"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Exemplo da CLI do Azure: Redefinir as credenciais da conta

O script da CLI do Azure neste artigo mostra como redefinir as credenciais da sua conta e recuperar as configurações de app.config.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Próximas etapas

* [az ams](/cli/azure/ams)
* [Redefinir credenciais](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
