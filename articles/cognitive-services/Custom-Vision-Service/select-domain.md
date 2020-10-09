---
title: Selecione um domínio para um projeto Visão Personalizada-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como selecionar um domínio para seu projeto no Serviço de Visão Personalizada.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82127766"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selecionar um domínio para um projeto Visão Personalizada

Na folha configurações do seu projeto Visão Personalizada, você pode selecionar um domínio para seu projeto. Escolha o domínio mais próximo de seu cenário.

## <a name="image-classification"></a>Classificação de imagens

|Domínio|Finalidade|
|---|---|
|__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagens. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Alimentos__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Caso queira classificar fotografias de frutas ou legumes individuais, use o domínio Alimentos.|
|__Pontos de referência__|Otimizado para pontos de referência reconhecidos, tanto naturais quanto artificiais. Este domínio funciona melhor quando o ponto de referência está claramente visível na fotografia. Este domínio funciona mesmo quando o ponto de referência está um pouco obstruído devido a pessoas na frente dele.|
|__Varejo__|Otimizado para imagens encontradas em um catálogo ou site de compras. Caso deseje uma classificação de alta precisão entre vestidos, calças e camisas, use esse domínio.|
|__Domínios compactos__| Otimizado para as restrições de classificação em tempo real em dispositivos de borda.|

## <a name="object-detection"></a>Detecção de objetos

|Domínio|Finalidade|
|---|---|
|__Geral__| Otimizado para uma ampla variedade de tarefas de detecção de objetos. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Logotipo__|Otimizado para localizar logotipos de marcas em imagens.|
|__Produtos em prateleiras__|Otimizado para detectar e classificar produtos em prateleiras.|
|__Domínios compactos__| Otimizado para as restrições de detecção de objetos em tempo real em dispositivos de borda.|

## <a name="compact-domains"></a>Domínios compactos

Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente. O desempenho do modelo varia de acordo com o domínio selecionado. Na tabela a seguir, relatamos o tamanho do modelo e o tempo de inferência na CPU da área de trabalho Intel e NVidia GPU \[ 1 \] . 

> [!NOTE]
> Esses números não incluem pré-processamento e tempo de pré-processamento.

|Tarefa|Domínio|Tamanho do modelo|Tempo de inferência de CPU|Tempo de inferência de GPU|
|---|---|---|---|---|
|classificação|Geral (compacto)|6 MB|13 ms|5 ms|
|Detecção de objetos|Geral (compacto)|45 MB|35 MS|5 ms|
|Detecção de objetos|Geral (Compact) [S1]|14 MB|27 MS|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Kit de desenvolvimento de ia de visão)

Quando um domínio compacto é selecionado, uma opção extra "recursos de exportação" é fornecida, permitindo a distinção entre "plataformas básicas" e "kit de desenvolvimento de ia de visão".

Em _recursos de exportação_ , as duas opções são:

- Plataformas básicas (Tensorflow, do coreml, ONNX, etc.)
- Kit de desenvolvimento de ia de visão.

Quando _o kit de desenvolvimento de ia da visão_ é selecionado, os _genéricos_, os _pontos de referência_e o _varejo_ , mas não os domínios de _alimentos_ estão disponíveis para a classificação de imagem, enquanto tanto _geral (Compact)_ quanto _geral (Compact) [S1]_ estão disponíveis para detecção de objetos.

>[!NOTE]
>O domínio __geral (compacto)__ para detecção de objeto requer lógica de processamento especial. Para obter detalhes, consulte um exemplo de script no pacote zip exportado. Se você precisar de um modelo sem a lógica de pré-processamento, use __geral (Compact) [S1]__.

>[!IMPORTANT]
>Não há nenhuma garantia de que os modelos exportados forneçam exatamente o mesmo resultado que a API de previsão na nuvem. A pequena diferença na plataforma em execução ou na implementação de pré-processamento pode causar maior diferença nas saídas do modelo. Para obter os detalhes da lógica de pré-processamento, consulte [este documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
