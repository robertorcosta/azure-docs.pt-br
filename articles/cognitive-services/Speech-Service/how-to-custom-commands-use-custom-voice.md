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
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87293953"
---
# <a name="use-custom-commands-with-custom-voice"></a>Usar Comandos Personalizados com Voz Personalizada

Neste artigo, você aprenderá a selecionar uma voz de saída personalizada para um aplicativo de comandos personalizados.

## <a name="select-a-custom-voice"></a>Selecionar uma voz personalizada

1. Em seu aplicativo de comandos personalizados, selecione **configurações** no painel esquerdo.
1. Selecione **voz personalizada** no painel central.
1. Selecione a voz pública ou personalizada desejada da tabela.
1. Clique em **Salvar**.

> [!div class="mx-imgBorder"]
> ![Sentenças de exemplo com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas**, os **tipos neurais** estão disponíveis somente para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neural por região/ponto de extremidade](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Para **vozes personalizadas**, elas podem ser criadas na página projeto de voz personalizado. Consulte [introdução à voz personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.
