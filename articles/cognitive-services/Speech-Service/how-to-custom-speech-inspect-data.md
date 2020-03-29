---
title: Inspecione a qualidade dos dados para o serviço de fala personalizado
titleSuffix: Azure Cognitive Services
description: O Custom Speech fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando dados de áudio com o resultado de reconhecimento correspondente. Você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806072"
---
# <a name="inspect-custom-speech-data"></a>Inspecione dados de fala personalizada

> [!NOTE]
> Esta página pressupõe que você leu [Prepare dados de teste para Discurso Personalizado](how-to-custom-speech-test-data.md) e tenha carregado um conjunto de dados para inspeção.

O Custom Speech fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando dados de áudio com o resultado de reconhecimento correspondente. A partir do [portal Custom Speech,](https://speech.microsoft.com/customspeech)você pode reproduzir o áudio carregado e determinar se o resultado de reconhecimento fornecido está correto. Esta ferramenta permite que você inspecione rapidamente a qualidade do modelo de fala-para-texto da Microsoft ou de um modelo personalizado treinado sem ter que transcrever nenhum dado de áudio.

Neste documento, você aprenderá a inspecionar visualmente a qualidade de um modelo usando os dados de treinamento que você carregou anteriormente.

Nesta página, você aprenderá a inspecionar visualmente a qualidade do modelo de fala-para-texto da Microsoft e/ou um modelo personalizado que você treinou. Você usará os dados que você carregou na guia **Dados** para testes.

## <a name="create-a-test"></a>Crie um teste

Siga estas instruções para criar um teste:

1. Faça login no [portal De Fala Personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até o teste de > de **> de fala personalizado >.**
3. Clique **em Adicionar teste**.
4. Selecione **Inspecionar qualidade (dados somente em áudio)**. Dê ao teste um nome, uma descrição e selecione seu conjunto de dados de áudio.
5. Selecione até dois modelos que você gostaria de testar.
6. Clique em **Criar**.

Depois que um teste foi criado com sucesso, você pode comparar os modelos lado a lado.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparações lado a lado

Quando o status do teste _for bem sucedido,_ clique no nome do item do teste para ver os detalhes do teste. Esta página detalhada lista todas as expressões em seu conjunto de dados, indicando os resultados de reconhecimento dos dois modelos ao lado da transcrição do conjunto de dados enviado.

Para ajudar a inspecionar a comparação lado a lado, você pode alternar vários tipos de erros, incluindo inserção, exclusão e substituição. Ouvindo o áudio e comparando os resultados de reconhecimento em cada coluna (mostrando a transcrição rotulada por humanos e os resultados de dois modelos de fala para texto), você pode decidir qual modelo atende às suas necessidades e onde as melhorias são necessárias.

Inspecionar testes de qualidade é útil para validar se a qualidade de um ponto final de reconhecimento de voz é suficiente para um aplicativo. Para uma medida objetiva de precisão, exigindo áudio transcrito, siga as instruções encontradas em [Avaliar precisão](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Próximas etapas

- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
- [Treinar seu modelo](how-to-custom-speech-train-model.md)
- [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar dados de teste para discurso personalizado](how-to-custom-speech-test-data.md)
