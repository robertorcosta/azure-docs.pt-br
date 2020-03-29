---
title: Rotular imagens mais rapidamente com o Smart Labeler
titleSuffix: Azure Cognitive Services
description: Neste guia, você aprenderá a usar o Smart Labeler para gerar tags sugeridas para imagens. Isso permite rotular um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647764"
---
# <a name="label-images-faster-with-smart-labeler"></a>Rotular imagens mais rapidamente com o Smart Labeler

Neste guia, você aprenderá a usar o Smart Labeler para gerar tags sugeridas para imagens. Isso permite rotular um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada.

Quando você marca imagens para um modelo de Visão Personalizada, o serviço usa a mais recente iteração treinada do modelo para prever os rótulos de imagens não marcadas. Em seguida, mostra essas previsões como tags sugeridas, com base no limiar de confiança selecionado e na incerteza da previsão. Em seguida, você pode confirmar ou alterar as sugestões, acelerando o processo de marcação manual das imagens para treinamento.

## <a name="when-to-use-smart-labeler"></a>Quando usar o Smart Labeler

Tenha em mente as seguintes limitações:

* Você só deve solicitar tags sugeridas para imagens cujo conteúdo já foi treinado uma vez. Não receba sugestões para uma nova etiqueta que você está apenas começando a treinar.

> [!IMPORTANT]
> O recurso Smart Labeler usa o mesmo [modelo de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) que as previsões regulares. A primeira vez que você acionar tags sugeridas para um conjunto de imagens, você será cobrado o mesmo que para chamadas de previsão. Depois disso, o serviço armazena os resultados das imagens selecionadas em um banco de dados por 30 dias, e você pode acessá-las a qualquer momento gratuitamente dentro desse período. Depois de 30 dias, você será cobrado se solicitar suas etiquetas sugeridas novamente.

## <a name="smart-labeler-workflow"></a>Fluxo de trabalho do Smart Labeler

As etapas a seguir mostram como usar o Smart Labeler:

1. Envie todas as suas imagens de treinamento para o seu projeto Visão Personalizada.
1. Marque parte do seu conjunto de dados, escolhendo um número igual de imagens para cada tag.
    > [!TIP]
    > Certifique-se de usar todas as tags para as quais você deseja sugestões mais tarde.
1. Inicie o processo de treinamento.
1. Quando o treinamento estiver concluído, navegue até a exibição **Untagged** e selecione o botão **Obter tags sugeridas** no painel esquerdo.
    > [!div class="mx-imgBorder"]
    > ![O botão de tags sugerido é mostrado na guia imagens não marcadas.](./media/suggested-tags/suggested-tags-button.png)
1. Na janela pop-up que aparece, defina o número de imagens para as quais você deseja sugestões. Você só deve obter sugestões iniciais de tag para uma parte das imagens não marcadas. Você terá melhores sugestões de etiquetas à medida que itera durante este processo.
1. Confirme as etiquetas sugeridas, corrigindo todas que não estejam corretas.
    > [!TIP]
    > Imagens com etiquetas sugeridas são classificadas por sua incerteza de previsão (valores mais baixos indicam maior confiança). Você pode alterar a ordem de classificação com o Sort pela opção **de incerteza.** Se você definir a ordem para **alta para baixa,** você pode corrigir as previsões de alta incerteza primeiro e, em seguida, confirmar rapidamente as de baixa incerteza.
    * Em projetos de classificação de imagens, você pode selecionar e confirmar tags em lotes. Filtrar a exibição por uma determinada tag sugerida, desmarcar imagens que estão marcadas incorretamente e, em seguida, confirmar o resto em um lote.
        > [!div class="mx-imgBorder"]
        > ![As tags sugeridas são exibidas no modo de lote para IC com filtros.](./media/suggested-tags/ic-batch-mode.png)

        Você também pode usar tags sugeridas no modo de imagem individual, selecionando uma imagem da galeria.

        ![As tags sugeridas são exibidas no modo de imagem individual para IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * Em projetos de detecção de objetos, as confirmações em lote não são suportadas, mas você ainda pode filtrar e classificar por tags sugeridas para uma experiência de rotulagem mais organizada. Miniaturas de suas imagens não marcadas mostrarão uma sobreposição de caixas delimitadoras indicando os locais das tags sugeridas. Se você não selecionar um filtro de marca sugerido, todas as suas imagens não marcadas aparecerão sem sobrepor caixas delimitadoras.
        > [!div class="mx-imgBorder"]
        > ![As tags sugeridas são exibidas no modo lote para OD com filtros.](./media/suggested-tags/od-batch-mode.png)

        Para confirmar as tags de detecção de objeto, você precisa aplicá-las a cada imagem individual na galeria.

        ![As tags sugeridas são exibidas no modo de imagem individual para OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Comece o processo de treinamento novamente.
1. Repita as etapas anteriores até que esteja satisfeito com a qualidade da sugestão.

## <a name="next-steps"></a>Próximas etapas

Siga um início rápido para começar a criar e treinar um projeto de Visão Personalizada.

* [Criar um classificador](getting-started-build-a-classifier.md)
* [Criar um detector de objeto](get-started-build-detector.md)