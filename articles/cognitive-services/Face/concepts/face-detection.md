---
title: Detecção facial e conceitos de atributos
titleSuffix: Azure Cognitive Services
description: Detecção facial é a ação de localizar rostos humanos em uma imagem e, opcionalmente, retornar diferentes tipos de dados relacionados ao rosto.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743062"
---
# <a name="face-detection-and-attributes"></a>Detecção facial e atributos

Este artigo explica os conceitos de detecção facial e dados de atributos faciais. Detecção facial é a ação de localizar rostos humanos em uma imagem e, opcionalmente, retornar diferentes tipos de dados relacionados ao rosto.

Você usa a [operação Face - Detecte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para detectar rostos em uma imagem. No mínimo, cada face detectada corresponde a um campo faceRectangle na resposta. Este conjunto de coordenadas de pixel para a face esquerda, superior, largura e altura marca maquinaa da face localizada. Usando essas coordenadas, você pode obter a localização do rosto e seu tamanho. Na resposta à API, as faces são listadas em ordem de tamanho de maior para menor.

## <a name="face-id"></a>Face ID

O Face ID é uma seqüência de identificador única para cada rosto detectado em uma imagem. Você pode solicitar um Face ID em seu [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) chamada de API.

## <a name="face-landmarks"></a>Pontos de referência facial

Os marcos faciais são um conjunto de pontos fáceis de encontrar em um rosto, como as pupilas ou a ponta do nariz. Por padrão, há 27 pontos de referência facial predefinidos. A figura a seguir mostra todos os 27 pontos:

![Um diagrama facial com todos os 27 marcos rotulados](../Images/landmarks.1.jpg)

As coordenadas dos pontos são devolvidas em unidades de pixels.

## <a name="attributes"></a>Atributos

Atributos são um conjunto de recursos que podem ser detectados opcionalmente pelo [Face - Detecte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Os seguintes atributos podem ser detectados:

* **Idade**. A idade estimada em anos de um rosto particular.
* **Borrão**. A embaçada do rosto na imagem. Este atributo retorna um valor entre zero e um e uma classificação informal de baixa, média ou alta.
* **Emoção.** Uma lista de emoções com sua confiança de detecção para o rosto dado. Os resultados de confiança são normalizados, e as pontuações em todas as emoções somam-se a uma. As emoções devolvidas são felicidade, tristeza, neutro, raiva, desprezo, nojo, surpresa e medo.
* **Exposição**. A exposição do rosto na imagem. Esse atributo retorna um valor entre zero e um e uma classificação informal de subExposição, boa Exposição ou superexposição.
* **Pelos faciais.** A presença estimada do cabelo facial e o comprimento do rosto dado.
* **Gênero**. O sexo estimado do rosto dado. Os valores possíveis são masculinos, femininos e sem gênero.
* **Óculos.** Se o rosto dado tem óculos. Os valores possíveis são Óculos, Óculos de Leitura, Óculos de Sol e Óculos de Natação.
* **Cabelo**. O tipo de cabelo do rosto. Este atributo mostra se o cabelo é visível, se a calvície é detectada e quais cores de cabelo são detectadas.
* **Pose de cabeça**. A orientação do rosto em espaço 3D. Este atributo é descrito pelos ângulos de arremesso, rolo e tecida em graus. As faixas de valor são de -90 graus a 90 graus, -180 graus a 180 graus, e -90 graus a 90 graus, respectivamente. Consulte o diagrama a seguir para mapear o ângulo:

    ![Uma cabeça com o arremesso, rolo, e eixos de inhame rotulados](../Images/headpose.1.jpg)
* **Maquiagem**. Se o rosto tem maquiagem. Este atributo retorna um valor booleano para eyeMakeup e lipMakeup.
* **Ruído**. O ruído visual detectado na imagem do rosto. Este atributo retorna um valor entre zero e um e uma classificação informal de baixa, média ou alta.
* **Oclusão.** Se há objetos bloqueando partes do rosto. Este atributo retorna um valor booleano para eyeOccluded, testoccluded, e mouthOccluded.
* **Sorria.** A expressão do sorriso do rosto dado. Este valor é entre zero para nenhum sorriso e um para um sorriso claro.

> [!IMPORTANT]
> Atributos faciais são previstos através do uso de algoritmos estatísticos. Eles podem nem sempre ser precisos. Tenha cuidado ao tomar decisões com base em dados de atributos.

## <a name="input-data"></a>Dados de entrada

Use as seguintes dicas para garantir que suas imagens de entrada dêem os resultados de detecção mais precisos:

* Os formatos de imagem de entrada suportados são JPEG, PNG, GIF para o primeiro quadro e BMP.
* O tamanho do arquivo de imagem não deve ser maior que 4 MB.
* O intervalo de tamanhos de face detectáveis é de 36 x 36 a 4096 x 4096 pixels. Rostos fora deste alcance não serão detectados.
* Alguns rostos podem não ser detectados por causa de desafios técnicos. Ângulos extremos da face (pose da cabeça) ou oclusão facial (objetos como óculos escuros ou mãos que bloqueiam parte do rosto) podem afetar a detecção. Rostos frontais e quase frontais dão os melhores resultados.

Se você estiver detectando rostos a partir de um feed de vídeo, você pode ser capaz de melhorar o desempenho ajustando certas configurações em sua câmera de vídeo:

* **Suavização**: Muitas câmeras de vídeo aplicam um efeito de suavização. Você deve desligá-lo se puder, porque cria um borrão entre os quadros e reduz a clareza.
* **Velocidade do obturador**: Uma velocidade mais rápida do obturador reduz a quantidade de movimento entre os quadros e torna cada quadro mais claro. Recomendamos velocidades de obturador de 1/60 segundo ou mais rápido.
* **Ângulo do obturador**: Algumas câmeras especificam o ângulo do obturador em vez da velocidade do obturador. Você deve usar um ângulo inferior do obturador, se possível. Isso resultará em quadros de vídeo mais claros.

    >[!NOTE]
    > Uma câmera com um ângulo inferior do obturador receberá menos luz em cada quadro, de modo que a imagem será mais escura. Você precisará determinar o nível certo para usar.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com conceitos de detecção facial, aprenda a escrever um script que detecte rostos em uma determinada imagem.

* [Detectar rostos em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)