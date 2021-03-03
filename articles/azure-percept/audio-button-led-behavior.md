---
title: Botão de áudio do Azure Percept e comportamento do LED
description: Saiba mais sobre os Estados de botão e LED do áudio do Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c7ee2289680bb50fabb8e6eb2a3ea0466bd58afb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661894"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Botão de áudio do Azure Percept e comportamento do LED

Consulte as diretrizes a seguir para obter informações sobre os Estados de botão e LED do áudio do Azure Percept.

## <a name="button-behavior"></a>Comportamento do botão

Você pode usar os botões para controlar o comportamento do dispositivo.

|Estado do botão|  Comportamento|
|------------|----------|
|Mute|  Pressione para ativar/desativar mudo do MIC-array. O evento de botão é disparado por liberação quando pressionado.|
|PTT/PTS|   Pressione PTT para ignorar o estado de detecção de palavra-chave e ativar o estado de escuta do comando. Pressione novamente para interromper a caixa de diálogo ativo do agente e reverter para o estado de parada da palavra-chave.|

## <a name="led-behavior"></a>Comportamento do LED

Você pode usar indicadores de LED para entender em qual estado seu dispositivo está.

|LED|   Estado do LED|  Status de SoM Ear|
|---|------------|----------------| 
|L02|   1x branco, estático em |Ligue |
|L02|   1x branco, 0,5 Hz piscando|  Autenticação em andamento |
|L01 & L02 & L03|   azul-3x, estático ativado|     Palavra-chave detectada|
|L01 & L02 & L03|   Matriz de LED piscando, 20fps | Ouvindo ou falando|
|L01 & L02 & L03|   Corrida de matriz LED, 20fps|    Pensando|
|L01 & L02 & L03|   triplo vermelho, estático em | Mute|

## <a name="next-steps"></a>Próximas etapas

Para obter dicas de solução de problemas para o dispositivo de áudio do Azure Percept, consulte este [guia](./troubleshoot-audio-accessory-speech-module.md).