---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: d03d904de68720874ea175c95244ba80c586df82
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133772"
---
## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal do Azure, no recurso do Personalizador na página **Configuração**, altere a **Frequência de atualização de modelo** para 10 segundos. Essa duração curta treinará o serviço rapidamente, permitindo que você veja como a ação superior é alterada a cada iteração.

![Alterar a frequência de atualização do modelo](../media/settings/configure-model-update-frequency-settings.png)

Quando o loop do Personalizador é instanciado pela primeira vez, não há modelo, pois não houve nenhuma chamada à API de Recompensa com base na qual treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve classificar sempre o conteúdo usando a saída de RewardActionId.