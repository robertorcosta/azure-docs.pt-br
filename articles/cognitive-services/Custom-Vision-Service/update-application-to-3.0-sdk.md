---
title: Como atualizar seu projeto para a API 3.0
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar projetos de Visão Personalizada da versão anterior da API para a API 3.0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647495"
---
# <a name="update-to-the-30-api"></a>Atualização para a API 3.0

O Custom Vision chegou agora à Disponibilidade Geral e passou por uma atualização da API.
Esta atualização inclui alguns novos recursos e, principalmente, algumas mudanças de ruptura:

* A API de previsão agora é dividida em duas com base no tipo de projeto.
* A opção de exportação do Vision AI Developer Kit (VAIDK) requer a criação de um projeto de forma específica.
* As iterações padrão foram removidas em favor de uma publicação/não publicação de uma iteração nomeada.

Este guia mostrará como atualizar seus projetos para trabalhar com a nova versão da API. Consulte as [notas de lançamento](release-notes.md) para obter uma lista completa das alterações.

## <a name="use-the-updated-prediction-api"></a>Use a API de previsão atualizada

As APIs 2.x usaram a mesma chamada de previsão para classificadores de imagem e projetos de detector de objetos. Ambos os tipos de projeto eram aceitáveis para as chamadas **PredictImage** e **PredictImageUrl.** Começando com 3.0, dividimos esta API para que você precise combinar o tipo de projeto com a chamada:

* Use **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** para obter previsões para projetos de classificação de imagens.
* Use **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** para obter previsões para projetos de detecção de objetos.

## <a name="use-the-new-iteration-publishing-workflow"></a>Use o novo fluxo de trabalho de publicação de iteração

As APIs 2.x usaram a iteração padrão ou um ID de iteração especificado para escolher a iteração a ser usada para previsão. A partir do 3.0, adotamos um fluxo de publicação pelo qual você publica pela primeira vez uma iteração um nome especificado da API de treinamento. Em seguida, passe o nome para os métodos de previsão para especificar qual iteração usar.

> [!IMPORTANT]
> As APIs 3.0 não usam o recurso de iteração padrão. Até despredarmos as APIs mais antigas, você pode continuar a usar as APIs 2.x para alternar uma iteração como padrão. Essas APIs serão mantidas por um período de tempo, e você pode chamar o método **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** para marcar uma iteração como padrão.

### <a name="publish-an-iteration"></a>Publicar uma iteração

Uma vez que uma iteração é treinada, você pode disponibilizá-la para previsão usando o método **[PublishIteration.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** Para publicar uma iteração, você precisará do ID de recurso de previsão, que está disponível na página de configurações do site da CustomVision.

![A página de configurações do site Custom Vision com o ID de recurso de previsão delineado.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Você também pode obter essas informações no [Portal Azure](https://portal.azure.com) indo para o recurso Depredição de Visão Personalizada e selecionando **Propriedades**.

Uma vez que sua iteração é publicada, os aplicativos podem usá-lo para predição especificando o nome em sua chamada de API de previsão. Para tornar uma iteração indisponível para chamadas de previsão, use a API **[UnpublishIteration.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência da API de treinamento (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentação de referência da API de previsão (REST)](https://go.microsoft.com/fwlink/?linkid=865445)