---
title: Avaliar a precisão para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste documento você aprenderá a medir quantitativamente a qualidade do nosso modelo de fala-texto ou seu modelo personalizado. Os dados de transcrição com rótulo humano são necessários para testar a precisão, e 30 minutos a 5 horas de áudio representativo devem ser fornecidos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806089"
---
# <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão da fala personalizada

Neste documento, você aprenderá a medir quantitativamente a qualidade do modelo de fala-para-texto da Microsoft ou seu modelo personalizado. Os dados de transcrição com rótulo humano são necessários para testar a precisão, e 30 minutos a 5 horas de áudio representativo devem ser fornecidos.

## <a name="what-is-word-error-rate-wer"></a>O que é taxa de erro do Word (WER)?

O padrão do setor para medir a precisão do modelo é *a Taxa de Erro do Word* (WER). O WER conta o número de palavras incorretas identificadas durante o reconhecimento e, em seguida, divide-se pelo número total de palavras fornecidas na transcrição rotulada pelo homem. Finalmente, esse número é multiplicado por 100% para calcular o WER.

![Fórmula WER](./media/custom-speech/custom-speech-wer-formula.png)

Palavras incorretamente identificadas se enquadram em três categorias:

* Inserção (I): Palavras que são adicionadas incorretamente na transcrição da hipótese
* Exclusão (D): Palavras que não são detectadas na transcrição da hipótese
* Substituição (S): Palavras que foram substituídas entre referência e hipótese

Aqui está um exemplo:

![Exemplo de palavras incorretamente identificadas](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Resolver erros e melhorar o WER

Você pode usar o WER a partir dos resultados de reconhecimento da máquina para avaliar a qualidade do modelo que você está usando com seu aplicativo, ferramenta ou produto. Um WER de 5%-10% é considerado de boa qualidade e está pronto para uso. Um WER de 20% é aceitável, no entanto você pode querer considerar treinamento adicional. Um WER de 30% ou mais sinaliza má qualidade e requer personalização e treinamento.

A forma como os erros são distribuídos é importante. Quando muitos erros de exclusão são encontrados, geralmente é devido à fraca força do sinal de áudio. Para resolver esse problema, você precisará coletar dados de áudio mais próximos da fonte. Erros de inserção significam que o áudio foi gravado em um ambiente barulhento e o crosstalk pode estar presente, causando problemas de reconhecimento. Erros de substituição são frequentemente encontrados quando uma amostra insuficiente de termos específicos de domínio foi fornecida como transcrições rotuladas por humanos ou texto relacionado.

Ao analisar arquivos individuais, você pode determinar que tipo de erros existem e quais erros são exclusivos de um arquivo específico. Entender problemas no nível do arquivo ajudará você a direcionar melhorias.

## <a name="create-a-test"></a>Crie um teste

Se você quiser testar a qualidade do modelo de linha de base de voz para texto da Microsoft ou um modelo personalizado que você treinou, você pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui resultados de WER e reconhecimento. Normalmente, um modelo personalizado é comparado com o modelo de linha de base da Microsoft.

Para avaliar modelos lado a lado:

1. Faça login no [portal De Fala Personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até o teste de > de **> de fala personalizado >.**
3. Clique **em Adicionar teste**.
4. Selecione **Avaliar precisão**. Dê ao teste um nome, uma descrição e selecione seu conjunto de dados de transcrição com rótulo humano.
5. Selecione até dois modelos que você gostaria de testar.
6. Clique em **Criar**.

Depois que seu teste foi criado com sucesso, você pode comparar os resultados lado a lado.

## <a name="side-by-side-comparison"></a>Comparação lado a lado

Uma vez que o teste esteja concluído, indicado pela mudança de status para *Sucesso,* você encontrará um número WER para ambos os modelos incluídos no teste. Clique no nome do teste para ver a página de detalhes do teste. Esta página detalhada lista todas as expressões em seu conjunto de dados, indicando os resultados de reconhecimento dos dois modelos ao lado da transcrição do conjunto de dados enviado. Para ajudar a inspecionar a comparação lado a lado, você pode alternar vários tipos de erros, incluindo inserção, exclusão e substituição. Ouvindo o áudio e comparando os resultados de reconhecimento em cada coluna, que mostra a transcrição rotulada pelo homem e os resultados para dois modelos de fala para texto, você pode decidir qual modelo atende às suas necessidades e onde treinamento suplementar e melhorias são Necessário.

## <a name="next-steps"></a>Próximas etapas

* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
