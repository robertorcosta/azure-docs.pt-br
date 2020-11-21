---
title: 'Como: usar comandos personalizados com o serviço de fala de voz personalizado'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você especificará a voz de saída de um aplicativo de comandos personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: 4a5c14909606dcb862fcf53d99bc5bc00fba63bd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025677"
---
# <a name="use-custom-commands-with-custom-voice"></a>Usar Comandos Personalizados com Voz Personalizada

Neste artigo, você aprenderá a selecionar uma voz de saída personalizada para um aplicativo de comandos personalizados.

## <a name="select-a-custom-voice"></a>Selecionar uma voz personalizada

1. Em seu aplicativo de comandos personalizados, selecione **configurações** no painel esquerdo.
1. Selecione **voz personalizada** no painel central.
1. Selecione a voz pública ou personalizada desejada da tabela.
1. Selecione **Salvar**.

> [!div class="mx-imgBorder"]
> ![Sentenças de exemplo com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas**, os **tipos neurais** estão disponíveis somente para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neural por região/ponto de extremidade](./regions.md#standard-and-neural-voices).
> - Para **vozes personalizadas**, elas podem ser criadas na página projeto de voz personalizado. Consulte [introdução à voz personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.