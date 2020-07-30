---
title: Exemplo de script da CLI do Azure - Criar uma transformação | Microsoft Docs
description: Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). O script da CLI do Azure neste artigo mostra como criar uma transformação.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 365c6a6a10ee79d96c1054416669e84c5392344c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092160"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: criar uma transformação

O script da CLI do Azure neste artigo mostra como criar uma transformação. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). Você sempre deve verificar se uma Transformação com o nome desejado e "receita" já existe. Se existir, você deve reutilizá-la.

## <a name="prerequisites"></a>Prerequisites 

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Você só pode especificar um caminho para um arquivo JSON de predefinição de Codificador Standard personalizado para [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset); confira o exemplo [Codificação com uma transformação personalizada](custom-preset-cli-howto.md).
>
> Não é possível passar um nome de arquivo ao usar [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Próximas etapas

[az ams transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
