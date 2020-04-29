---
title: Implantar um modelo para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Neste documento, você aprenderá a criar e implantar um ponto de extremidade usando o portal de Fala Personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 9d48f09fe5d5c736f65b6a76211dd3fec85479ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402191"
---
# <a name="deploy-a-custom-model"></a>Implantar um modelo personalizado

Depois de carregar e inspecionar dados, avaliar a precisão e treinar um modelo personalizado, você pode implantar um ponto de extremidade personalizado para usar com seus aplicativos, ferramentas e produtos. Neste documento, você aprenderá a criar e implantar um ponto de extremidade usando o [portal de fala personalizada](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Criar um ponto de extremidade personalizado

Para criar um novo ponto de extremidade personalizado, entre no [portal de fala personalizada](https://speech.microsoft.com/customspeech) e selecione **implantação** no menu fala personalizada na parte superior da página. Se esta for sua primeira execução, você observará que não há nenhum ponto de extremidade listado na tabela. Depois de criar um ponto de extremidade, você usará essa página para acompanhar cada ponto de extremidade implantado.

Em seguida, selecione **Adicionar ponto de extremidade** e insira um **nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo personalizado que você deseja associar a esse ponto de extremidade. Nessa página, você também pode habilitar o registro em log. O registro em log permite monitorar o tráfego do ponto de extremidade. Se desabilitado, o tráfego não será armazenado.

![Como implantar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e os detalhes de preços.

Em seguida, selecione **Criar**. Essa ação retorna para a página de **implantação** . A tabela agora inclui uma entrada que corresponde ao seu ponto de extremidade personalizado. O status do ponto de extremidade mostra seu estado atual. Pode levar até 30 minutos para criar uma instância de um novo ponto de extremidade usando seus modelos personalizados. Quando o status da implantação for alterado para **concluído**, o ponto de extremidade estará pronto para uso.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Clique no link para exibir informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo.

## <a name="view-logging-data"></a>Exibir dados de log

Os dados de log estão disponíveis para download em **detalhes do ponto de extremidade >**.

## <a name="next-steps"></a>Próximas etapas

* Saiba como usar seu modelo personalizado [aqui](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)
