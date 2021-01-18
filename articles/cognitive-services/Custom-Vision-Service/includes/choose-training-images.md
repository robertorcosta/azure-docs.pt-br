---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256334"
---
É recomendável usar pelo menos 30 imagens por marca no conjunto de treinamento inicial. Também é conveniente coletar algumas imagens adicionais para testar o seu modelo após o treinamento.

Para treinar o seu modelo com eficiência, use imagens com variedade de visual. Selecione imagens com variação em:
* ângulo da câmera
* iluminação
* background
* estilo do visual
* assuntos individuais/agrupados
* tamanho
* type

Além disso, certifique-se de que todas as suas imagens de treinamento atendam aos seguintes critérios:
* Formato .jpg, .png, .bmp ou .gif
* não ultrapasse 6 MB em tamanho (4 MB para imagens de previsão)
* não tenha menos de 256 pixels na borda mais curta; qualquer imagem menor que isso será escalada verticalmente de maneira automática pelo Serviço de Visão Personalizada

> [!NOTE]
> O Trove, um projeto da Microsoft Garage, permite coletar e comprar conjuntos de imagens para fins de treinamento. Depois de coletar suas imagens, você pode baixá-las e, em seguida, importá-las para seu projeto de Visão Personalizada da maneira usual. Visite a [página do Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) para saber mais.