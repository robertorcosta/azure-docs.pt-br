---
title: Treine um modelo para discurso personalizado - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Treinar um modelo de fala-texto pode melhorar a precisão de reconhecimento para o modelo de linha de base da Microsoft ou um modelo personalizado. Um modelo é treinado usando transcrições rotuladas por humanos e texto sumido.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137762"
---
# <a name="train-a-model-for-custom-speech"></a>Treinar um modelo para Fala Personalizada

Treinar um modelo de fala-texto pode melhorar a precisão de reconhecimento para o modelo de linha de base da Microsoft. Um modelo é treinado usando transcrições rotuladas por humanos e texto sumido. Esses conjuntos de dados, juntamente com dados de áudio enviados anteriormente, são usados para refinar e treinar o modelo de fala para texto para reconhecer palavras, frases, siglas, nomes e outros termos específicos do produto. Quanto mais conjuntos de dados no domínio você fornecer (dados relacionados ao que os usuários dirão e ao que você espera reconhecer), mais preciso será o seu modelo, o que resulta em um reconhecimento melhorado. Tenha em mente que, ao alimentar dados não relacionados em seu treinamento, você pode reduzir ou ferir a precisão do seu modelo.

## <a name="use-training-to-resolve-accuracy-issues"></a>Use o treinamento para resolver problemas de precisão

Se você está encontrando problemas de reconhecimento com seu modelo, usar transcrições com rótulo humano e dados relacionados para treinamento adicional pode ajudar a melhorar a precisão. Use esta tabela para determinar qual conjunto de dados usar para resolver seus problemas?

| Caso de uso | Tipo de dados |
| -------- | --------- |
| Melhore a precisão do reconhecimento no vocabulário e gramática específicos do setor, como terminologia médica ou jargão de TI. | Texto relacionado (frases/expressões) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia fora do padrão, como nomes de produtos ou siglas. | Texto relacionado (pronúncia) |
| Melhore a precisão de reconhecimento em estilos de fala, sotaques ou ruídos de fundo específicos. | Áudio + transcrições com rótulo humano |

> [!IMPORTANT]
> Se você não tiver carregado um conjunto de dados, consulte [Prepare e teste seus dados](how-to-custom-speech-test-data.md). Este documento fornece instruções para o upload de dados e diretrizes para a criação de conjuntos de dados de alta qualidade.

## <a name="train-and-evaluate-a-model"></a>Treinar e avaliar um modelo

O primeiro passo para treinar um modelo é carregar dados de treinamento. Use [Prepare e teste seus dados](how-to-custom-speech-test-data.md) para instruções passo a passo para preparar transcrições com rótulo humano e texto relacionado (expressões e pronúncias). Depois de enviar dados de treinamento, siga estas instruções para começar a treinar seu modelo:

1. Faça login no [portal De Fala Personalizada](https://speech.microsoft.com/customspeech).
2. Navegue para o treinamento de **> de discurso personalizado > > .**
3. Clique **no modelo Train**.
4. Em seguida, dê ao seu treinamento um **nome** e **descrição**.
5. No menu suspenso do modelo Cenário e Linha de **Base,** selecione o cenário que melhor se encaixa no seu domínio. Se você não tem certeza de qual cenário escolher, selecione **General**. O modelo de linha de base é o ponto de partida para o treinamento. Se você não tem uma preferência, você pode usar o mais recente.
6. Na página **De dados de treinamento Select,** escolha um ou vários conjuntos de dados de transcrição com rótulo humano que você gostaria de usar para treinamento.
7. Uma vez que o treinamento esteja completo, você pode optar por realizar testes de precisão no modelo recém-treinado. Esta etapa é opcional.
8. Selecione **Criar** para construir seu modelo personalizado.

A tabela Treinamento exibe uma nova entrada que corresponde a este modelo recém-criado. A tabela também exibe o status: Processamento, Sucesso, Falha.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Avalie a precisão de um modelo treinado

Você pode inspecionar os dados e avaliar a precisão do modelo usando esses documentos:

- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)

Se você optou por testar a precisão, é importante selecionar um conjunto de dados acústico que seja diferente do que você usou com o seu modelo para ter uma noção realista do desempenho do modelo.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste seus dados](how-to-custom-speech-test-data.md)
- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
- [Treinar seu modelo](how-to-custom-speech-train-model.md)
