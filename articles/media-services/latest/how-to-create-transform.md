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
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ffab4eaf63844057a3872730f91634cb2df5669
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918055"
---
# <a name="create-a-transform"></a>Criar uma transformação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O script da CLI do Azure neste artigo mostra como criar uma transformação. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). Você sempre deve verificar se uma Transformação com o nome desejado e "receita" já existe. Se existir, você deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Você só pode especificar um caminho para um arquivo JSON de predefinição de Codificador Standard personalizado para [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset); confira o exemplo [Codificação com uma transformação personalizada](custom-preset-cli-howto.md).
>
> Não é possível passar um nome de arquivo ao usar [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-transform-rest.md)]

---

## <a name="next-steps"></a>Próximas etapas

[Mais sobre transformações e trabalhos](transforms-jobs-concept.md)
