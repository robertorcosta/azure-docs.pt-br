---
title: Selecione um domínio para um projeto de Visão Personalizada - Visão computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como selecionar um domínio para o seu projeto no Serviço de Visão Personalizada.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899446"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selecione um domínio para um projeto de Visão Personalizada

A partir da lâmina de configurações para o projeto Visão Personalizada, você pode selecionar um domínio para o seu projeto. Escolha o domínio mais próximo do seu cenário.

## <a name="image-classification"></a>Classificação de imagens

|Domínio|Finalidade|
|---|---|
|__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagens. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Alimentos__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Caso queira classificar fotografias de frutas ou legumes individuais, use o domínio Alimentos.|
|__Marcos__|Otimizado para pontos de referência reconhecidos, tanto naturais quanto artificiais. Este domínio funciona melhor quando o ponto de referência está claramente visível na fotografia. Este domínio funciona mesmo quando o ponto de referência está um pouco obstruído devido a pessoas na frente dele.|
|__Varejo__|Otimizado para imagens encontradas em um catálogo ou site de compras. Caso deseje uma classificação de alta precisão entre vestidos, calças e camisas, use esse domínio.|
|__Domínios compactos__| Otimizado para as restrições de classificação em tempo real em dispositivos de borda.|

## <a name="object-detection"></a>Detecção de objetos

|Domínio|Finalidade|
|---|---|
|__Geral__| Otimizado para uma ampla variedade de tarefas de detecção de objetos. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Logotipo__|Otimizado para localizar logotipos de marcas em imagens.|
|__Produtos nas prateleiras__|Otimizado para detectar e classificar produtos nas prateleiras.|
|__Domínios compactos__| Otimizado para as restrições de detecção de objetos em tempo real em dispositivos de borda.|

## <a name="compact-domains"></a>Domínios compactos

Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente. O desempenho do modelo varia de acordo com o domínio selecionado. Na tabela abaixo, relatamos o tamanho do modelo e o tempo de \[\]inferência na CPU do Desktop Intel e na GPU NVidia 1 . 

> [!NOTE]
> Esses números não incluem tempo de pré-processamento e pós-processamento.

|Tarefa|Domínio|Tamanho do modelo|Tempo de inferência da CPU|Tempo de inferência de GPU|
|---|---|---|---|---|
|classificação|Geral (compacto)|5 MB|13 ms|5 ms|
|Detecção de objetos|Geral (compacto)|45 MB|35 ms|5 ms|
|Detecção de objetos|Geral (compacto) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Quando um domínio compacto é selecionado, uma opção extra "Capacidades de exportação" é fornecida permitindo distinguir entre "Plataformas Básicas" e "Vision AI Dev Kit".

Em _Recursos de Exportação,_ as duas opções são:

- Plataformas básicas (Tensorflow, CoreML, ONNX, etc.)
- Vision AI Dev Kit.

Quando _o Vision AI Dev Kit_ é selecionado, os _domínios_ _Genérico,_ Marcos e _Varejo,_ mas não os domínios compactos _de alimentos_ estão disponíveis para classificação de imagem, enquanto geral _(compacto)_ e _geral (compacto) [S1]_ estão disponíveis para detecção de objetos.

>[!NOTE]
>O domínio __geral (compacto)__ para detecção de objetos requer uma lógica especial de pós-processamento. Para obter os detalhes, consulte um script de exemplo no pacote zip exportado. Se você precisar de um modelo sem a lógica de pós-processamento, use __Geral (compacto) [S1]__.

>[!IMPORTANT]
>Não há garantia de que os modelos exportados dão exatamente o mesmo resultado que a API de previsão na nuvem. Uma pequena diferença na plataforma em execução ou na implementação de pré-processamento pode causar maior diferença nas saídas do modelo. Para obter o detalhe da lógica de pré-processamento, consulte [este documento](python-tutorial.md).

\[1\] CPU Intel Xeon E5-2690 e NVIDIA Tesla M60
