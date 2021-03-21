---
title: Visão geral do Fala Personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: Fala Personalizada é um conjunto de ferramentas online que permite avaliar e melhorar a precisão de fala para texto da Microsoft para seus aplicativos, ferramentas e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 13bf0f2430e0d58dd9ef28061aad897acf94ac3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493043"
---
# <a name="what-is-custom-speech"></a>O que é a Fala Personalizada?

[Fala personalizada](https://aka.ms/customspeech) é um conjunto de ferramentas baseadas na interface do usuário que permitem avaliar e melhorar a precisão da fala para o texto da Microsoft para seus aplicativos e produtos. Tudo o que é necessário para começar é um grande quantidade de arquivos de áudio de teste. Siga os links neste artigo para começar a criar uma experiência de fala em texto Personalizada.

## <a name="whats-in-custom-speech"></a>O que há de Fala Personalizada?

Para poder fazer qualquer coisa com Fala Personalizada, você precisará de uma conta do Azure e uma assinatura do serviço de fala. Depois de ter uma conta, você pode preparar seus dados, treinar e testar seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, por fim, implantar e usar o modelo de fala em texto personalizado.

Este diagrama realça as partes que compõem a [área de fala personalizada do Speech Studio](https://aka.ms/customspeech). Use os links abaixo para saber mais sobre cada etapa.

![Diagrama que realça os componentes que compõem a área de Fala Personalizada do Speech Studio.](./media/custom-speech/custom-speech-overview.png)

1. [Assine e crie um projeto](#set-up-your-azure-account). Crie uma conta do Azure e assine o serviço de fala. Essa assinatura unificada fornece acesso a fala-para-texto, conversão de fala e fala do Speech [Studio](https://speech.microsoft.com/customspeech). Em seguida, use sua assinatura do serviço de fala para criar seu primeiro projeto de Fala Personalizada.

1. [Carregar dados de teste](./how-to-custom-speech-test-and-train.md). Carregue dados de teste (arquivos de áudio) para avaliar a oferta de fala a texto da Microsoft para seus aplicativos, ferramentas e produtos.

1. [Inspecione a qualidade do reconhecimento](how-to-custom-speech-inspect-data.md). Use o [Speech Studio](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecione a qualidade do reconhecimento de fala de seus dados de teste. Para medições quantitativas, consulte [inspecionar dados](how-to-custom-speech-inspect-data.md).

1. [Avalie e aprimore a precisão](how-to-custom-speech-evaluate-data.md). Avalie e aprimore a precisão do modelo de conversão de fala em texto. O [Speech Studio](https://speech.microsoft.com/customspeech) fornecerá uma *taxa de erros do Word*, que você pode usar para determinar se o treinamento adicional é necessário. Se estiver satisfeito com a precisão, você poderá usar as APIs do serviço de fala diretamente. Se você quiser melhorar a precisão por uma média relativa de 5% a 20%, use a guia **treinamento** no portal para carregar dados de treinamento adicionais, como transcrições com rótulo humano e texto relacionado.

1. [Treine e implante um modelo](how-to-custom-speech-train-model.md). Melhore a precisão do seu modelo de fala para texto fornecendo transcrições escritas (10 a 1.000 horas) e texto relacionado (<200 MB) junto com seus dados de teste de áudio. Esses dados ajudam a treinar o modelo de conversão de fala em texto. Após o treinamento, teste novamente. Se estiver satisfeito com o resultado, você poderá implantar seu modelo em um ponto de extremidade personalizado.

## <a name="set-up-your-azure-account"></a>Configurar sua conta do Azure

Você precisa ter uma assinatura do serviço de fala e conta do Azure antes de poder usar o [Speech Studio](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Certifique-se de criar uma assinatura padrão (S0). Não há suporte para assinaturas gratuitas (F0).

Se você planeja treinar um modelo personalizado com **dados de áudio**, escolha uma das seguintes regiões que têm hardware dedicado disponível para treinamento. Isso reduzirá o tempo necessário para treinar um modelo e permitirá que você use mais áudio para treinamento. Nessas regiões, o serviço de fala usará até 20 horas de áudio para treinamento; em outras regiões, ele só usará até 8 horas.

* Leste da Austrália
* Canadá Central
* Índia Central
* Leste dos EUA
* Leste dos EUA 2
* Centro-Norte dos EUA
* Norte da Europa
* Centro-Sul dos Estados Unidos
* Sudeste Asiático
* Sul do Reino Unido
* Governo dos EUA do Arizona
* Gov. dos EUA – Virgínia
* Europa Ocidental
* Oeste dos EUA 2

Depois de criar uma conta do Azure e uma assinatura do serviço de fala, você precisará entrar no [Speech Studio](https://speech.microsoft.com/customspeech) e conectar sua assinatura.

1. Entre no [Speech Studio](https://aka.ms/custom-speech).
1. Selecione a assinatura na qual você precisa trabalhar e crie um projeto de fala.
1. Se você quiser modificar sua assinatura, selecione o botão engrenagem no menu superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdo como dados, modelos, testes e pontos de extremidade são organizados em *projetos* no [Speech Studio](https://speech.microsoft.com/customspeech). Cada projeto é específico de um domínio e país/idioma. Por exemplo, você pode criar um projeto para centros de chamadas que usam o inglês no Estados Unidos.

Para criar seu primeiro projeto, selecione **fala-para-texto/fala personalizada** e, em seguida, selecione **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar seu projeto. Depois de criar um projeto, você deverá ver quatro guias: **dados**, **teste**, **treinamento** e **implantação**. Use os links fornecidos nas [próximas etapas](#next-steps) para aprender a usar cada guia.

> [!IMPORTANT]
> O [Speech Studio](https://aka.ms/custom-speech) , anteriormente conhecido como "portal de fala personalizada", foi atualizado recentemente! Se você criou dados, modelos, testes e pontos de extremidade publicados anteriores no portal do CRIS.ai ou com APIs, você precisa criar um novo projeto no novo portal para se conectar a essas entidades antigas.

## <a name="model-and-endpoint-lifecycle"></a>Ciclo de vida de modelo e ponto de extremidade

Modelos mais antigos normalmente se tornam menos úteis ao longo do tempo porque o modelo mais recente geralmente tem maior precisão. Portanto, modelos de base, bem como modelos personalizados e pontos de extremidade criados por meio do portal, estão sujeitos à expiração após 1 ano para adaptação e 2 anos para decodificação. Consulte uma descrição detalhada no artigo [modelo e ciclo de vida do ponto de extremidade](./how-to-custom-speech-model-and-endpoint-lifecycle.md) .

## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar e melhorar a precisão do modelo](how-to-custom-speech-evaluate-data.md)
* [Treinar e implantar um modelo](how-to-custom-speech-train-model.md)
