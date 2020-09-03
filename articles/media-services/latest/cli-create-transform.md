---
title: Exemplo de script da CLI do Azure - Criar uma transformação | Microsoft Docs
description: Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). O script da CLI do Azure neste artigo mostra como criar uma transformação.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e85116aaad1eecbe137ae3e470811b31d1a855f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295964"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: criar uma transformação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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
