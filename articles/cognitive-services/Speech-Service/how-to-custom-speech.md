---
title: Comece com discurso personalizado - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Custom Speech é um conjunto de ferramentas on-line que permitem avaliar e melhorar nossa precisão de fala para texto para seus aplicativos, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de fala para texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: a52735ee62f564a5dc536fd2b7d3539406388341
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402077"
---
# <a name="what-is-custom-speech"></a>O que é discurso personalizado?

[O Custom Speech](https://aka.ms/customspeech) é um conjunto de ferramentas on-line que permitem avaliar e melhorar a precisão de voz a texto da Microsoft para seus aplicativos, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de fala para texto.

## <a name="whats-in-custom-speech"></a>O que há no Discurso Personalizado?

Antes de fazer qualquer coisa com o Custom Speech, você precisará de uma conta do Azure e uma assinatura do serviço Speech. Depois de ter uma conta, você pode preparar seus dados, treinar e testar seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, finalmente, implantar e usar o modelo personalizado de voz para texto.

Este diagrama destaca as peças que compõem o [portal Custom Speech](https://aka.ms/customspeech). Use os links abaixo para saber mais sobre cada etapa.

![Destaca os diferentes componentes que compõem o portal Fala Personalizada.](./media/custom-speech/custom-speech-overview.png)

1. [Inscreva-se e crie um projeto](#set-up-your-azure-account) - Crie uma conta no Azure e assine o serviço Speech. Esta assinatura unificada dá acesso a fala-para-texto, texto-para-fala, tradução de fala e o [portal De Fala Personalizada](https://speech.microsoft.com/customspeech). Em seguida, usando sua assinatura de serviço de fala, crie seu primeiro projeto de Discurso Personalizado.

2. [Carregar dados de teste](how-to-custom-speech-test-data.md) - Faça upload de dados de teste (arquivos de áudio) para avaliar a oferta de voz para texto da Microsoft para seus aplicativos, ferramentas e produtos.

3. [Inspecione](how-to-custom-speech-inspect-data.md) a qualidade do reconhecimento - Use o [portal Custom Speech](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecione a qualidade do reconhecimento de voz dos seus dados de teste. Para obter medidas quantitativas, consulte [Inspecionar dados](how-to-custom-speech-inspect-data.md).

4. [Avaliar a precisão](how-to-custom-speech-evaluate-data.md) - Avaliar a precisão do modelo fala-a-texto. O [portal De fala personalizada](https://speech.microsoft.com/customspeech) fornecerá uma taxa de erro do *Word,* que pode ser usada para determinar se é necessário treinamento adicional. Se você estiver satisfeito com a precisão, você pode usar as APIs de serviço de fala diretamente. Se você quiser melhorar a precisão em uma média relativa de 5% a 20%, use a guia **Treinamento** no portal para carregar dados adicionais de treinamento, como transcrições rotuladas por humanos e texto relacionado.

5. [Treine o modelo](how-to-custom-speech-train-model.md) - Melhore a precisão do seu modelo de fala-texto fornecendo transcrições por escrito (10-1.000 horas) e texto relacionado (<200 MB) juntamente com seus dados de teste de áudio. Esses dados ajudam a treinar o modelo de fala para texto. Após o treinamento, teste novamente, e se você estiver satisfeito com o resultado, você pode implantar o seu modelo.

6. [Implantar o modelo](how-to-custom-speech-deploy-model.md) - Crie um ponto final personalizado para o modelo de voz para texto e use-o em seus aplicativos, ferramentas ou produtos.

## <a name="set-up-your-azure-account"></a>Configure sua conta no Azure

Uma assinatura de serviço de fala é necessária antes que você possa usar o [portal Custom Speech](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Siga estas instruções para criar uma assinatura padrão do serviço de fala: [Crie uma assinatura de fala](get-started.md#new-resource).

> [!NOTE]
> Por favor, certifique-se de criar assinaturas padrão (S0), as assinaturas de avaliação gratuita (F0) não são suportadas.

Depois de criar uma conta do Azure e uma assinatura do serviço Speech, você precisará entrar no [portal Custom Speech](https://speech.microsoft.com/customspeech) e conectar sua assinatura.

1. Obtenha a chave de assinatura do serviço Speech no portal Azure.
2. Faça login no [portal De Fala Personalizada](https://aka.ms/custom-speech).
3. Selecione a assinatura que você precisa para trabalhar e crie um projeto de fala.
4. Se quiser modificar sua assinatura, use o ícone **de engrenagem** localizado na navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no [portal Custom Speech](https://speech.microsoft.com/customspeech). Cada projeto é específico para um domínio e país/idioma. Por exemplo, você pode criar um projeto para call centers que usam inglês nos Estados Unidos.

Para criar seu primeiro projeto, selecione a **fala Fala-para-texto/Personalizado**e clique em **Novo Projeto**. Siga as instruções fornecidas pelo assistente para criar seu projeto. Depois de criar um projeto, você deve ver quatro guias: **Dados,** **Testes,** **Treinamento**e **Implantação.** Use os links fornecidos nos [próximos passos](#next-steps) para saber como usar cada guia.

> [!IMPORTANT]
> O [portal Custom Speech](https://aka.ms/custom-speech) foi atualizado recentemente! Se você criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, você precisa criar um novo projeto no novo portal para se conectar a essas entidades antigas.

## <a name="next-steps"></a>Próximas etapas

* [Prepare e teste seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar o modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)
