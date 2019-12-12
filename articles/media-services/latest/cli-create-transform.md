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
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888591"
---
# <a name="cli-example-create-a-transform"></a>Exemplo de CLI: Criar uma Transformação

O script da CLI do Azure neste artigo mostra como criar uma transformação. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). Você sempre deve verificar se uma Transformação com o nome desejado e "receita" já existe. Se existir, você deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Você só pode especificar um caminho para um arquivo JSON de predefinição de Codificador Standard personalizado para [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset); confira o exemplo [Codificação com uma transformação personalizada](custom-preset-cli-howto.md).
>
> Não é possível passar um nome de arquivo ao usar [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Próximas etapas

[az ams transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
