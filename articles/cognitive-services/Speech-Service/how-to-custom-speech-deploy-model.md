---
title: Implantar um modelo para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste documento, você aprenderá como criar e implantar um ponto final usando o portal Custom Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806106"
---
# <a name="deploy-a-custom-model"></a>Implantar um modelo personalizado

Depois de carregar e inspecionar dados, avaliar a precisão e treinar um modelo personalizado, você pode implantar um ponto final personalizado para usar com seus aplicativos, ferramentas e produtos. Neste documento, você aprenderá como criar e implantar um ponto final usando o [portal De fala personalizada](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Crie um ponto final personalizado

Para criar um novo ponto final personalizado, entre no [portal 'Discurso personalizado'](https://speech.microsoft.com/customspeech) e selecione **'Implantação'** no menu 'Discurso personalizado' no topo da página. Se esta for sua primeira corrida, você notará que não há pontos finais listados na tabela. Depois de criar um ponto final, você usará esta página para rastrear cada ponto final implantado.

Em seguida, **selecione Adicionar ponto final** e digite um **Nome** e **descrição** para o seu ponto final personalizado. Em seguida, selecione o modelo personalizado que você gostaria de associar com este ponto final. A partir desta página, você também pode habilitar o registro. O registro permite monitorar o tráfego de ponto final. Se desativado, o tráfego não será armazenado.

![Como implantar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e detalhes de preços.

Em seguida, selecione **Criar**. Esta ação retorna-o à página **'Implantação'.** A tabela agora inclui uma entrada que corresponde ao seu ponto final personalizado. O status do ponto final mostra seu estado atual. Pode levar até 30 minutos para instanciar um novo ponto final usando seus modelos personalizados. Quando o status da implantação for alterado para **Concluído,** o ponto final está pronto para ser usado.

Depois que seu ponto final é implantado, o nome do ponto final aparece como um link. Clique no link para exibir informações específicas do seu ponto final, como a chave de ponto final, url de ponto final e código de exemplo.

## <a name="view-logging-data"></a>Exibir dados de registro

Os dados de registro estão disponíveis para download em **Endpoint > Details**.

## <a name="next-steps"></a>Próximas etapas

* Use seu ponto final personalizado com o [Speech SDK](speech-sdk.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)
