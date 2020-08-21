---
title: Carregar conteúdo em um ativo dos Serviços de Mídia do Azure usando a CLI do Azure
description: O script de CLI do Azure neste tópico mostra como criar um ativo dos Serviços de Mídia para carregar o conteúdo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585400"
---
# <a name="create-an-asset"></a>Criar um ativo

Este artigo mostra como criar um ativo dos Serviços de Mídia.  Você usará um ativo para armazenar o conteúdo de mídia para codificação e streaming.  Para saber mais sobre os ativos dos Serviços de Mídia, leia [Ativos dos Serviços de Mídia do Azure v3](assets-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas descritas em [Criar uma conta dos Serviços de Mídia](./create-account-howto.md) para criar a conta dos Serviços de Mídia e o grupo de recursos necessários para criar um ativo.

## <a name="methods"></a>Métodos

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Shell da CLI](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>Próximas etapas

[Gerenciar ativos](manage-asset-concept.md)
