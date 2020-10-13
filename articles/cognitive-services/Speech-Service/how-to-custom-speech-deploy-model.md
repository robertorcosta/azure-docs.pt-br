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
ms.openlocfilehash: c7f03027abf7f3c5e330e5cd95075cce1152a7d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85130408"
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
> [!NOTE]
>Os dados de log estão disponíveis por 30 dias no armazenamento de propriedade da Microsoft e serão removidos posteriormente. Caso uma conta de armazenamento de cliente seja vinculada à assinatura de serviços cognitivas, os dados de log não serão excluídos automaticamente.

## <a name="next-steps"></a>Próximas etapas

* Saiba como usar seu modelo personalizado [aqui](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)
