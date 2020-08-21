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
ms.openlocfilehash: 65b0b276dcda58810f44494c14f06dd412d633f6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719284"
---
# <a name="create-an-asset"></a>Criar um ativo

Este artigo mostra como criar um ativo de serviços de mídia.  Você usará um ativo para manter o conteúdo de mídia para codificação e streaming.  Para saber mais sobre os ativos dos serviços de mídia, leia [ativos nos serviços de mídia do Azure v3](assets-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas em [criar uma conta dos serviços de mídia](./create-account-howto.md) para criar a conta dos serviços de mídia e o grupo de recursos necessários para criar um ativo.

## <a name="methods"></a>Métodos

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Usar o REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Usando cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Uso do Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos serviços de mídia](media-services-overview.md)
