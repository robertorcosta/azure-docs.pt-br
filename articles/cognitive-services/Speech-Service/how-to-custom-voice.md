---
title: Melhorar a síntese com voz personalizada - serviço de fala
titleSuffix: Azure Cognitive Services
description: Custom Voice é um conjunto de ferramentas online que permitem que você crie uma voz reconhecível e única para sua marca. Tudo o que é preciso para começar são um punhado de arquivos de áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência personalizada de fala para texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 2aacf8881a57f0677177cd341a0ddc63224b26b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402046"
---
# <a name="get-started-with-custom-voice"></a>Introdução à Voz Personalizada

[Custom Voice](https://aka.ms/customvoice) é um conjunto de ferramentas online que permitem que você crie uma voz reconhecível e única para sua marca. Tudo o que é preciso para começar são um punhado de arquivos de áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência personalizada de texto para fala.

## <a name="whats-in-custom-voice"></a>O que há em Voz Personalizada?

Antes de começar com o Custom Voice, você precisará de uma conta do Azure e uma assinatura do serviço Speech. Depois de criar uma conta, você pode preparar seus dados, treinar e testar seus modelos, avaliar a qualidade da voz e, finalmente, implantar seu modelo de voz personalizado.

O diagrama abaixo destaca as etapas para criar um modelo de voz personalizado usando o [portal Voz Personalizada](https://aka.ms/customvoice). Use os links para saber mais.

![Diagrama de arquitetura de voz personalizada](media/custom-voice/custom-voice-diagram.png)

1. [Assine e crie um projeto](#set-up-your-azure-account) - Crie uma conta no Azure e crie uma assinatura do serviço Speech. Esta assinatura unificada dá acesso a fala-para-texto, texto-para-voz, tradução de voz e o portal Voz Personalizada. Em seguida, usando sua assinatura de serviço de fala, crie seu primeiro projeto de Voz Personalizada.

2. [Upload de dados](how-to-custom-voice-create-voice.md#upload-your-datasets) - Faça upload de dados (áudio e texto) usando o portal de voz personalizado ou a API de voz personalizada. A partir do portal, você pode investigar e avaliar os escores de pronúncia e as relações sinal-ruído. Para obter mais informações, consulte [Como preparar dados para a Voz Personalizada](how-to-custom-voice-prepare-data.md).

3. [Treine seu modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Use seus dados para criar um modelo de voz personalizado de texto para voz. Você pode treinar um modelo em diferentes idiomas. Após o treinamento, teste seu modelo, e se você estiver satisfeito com o resultado, você pode implantar o modelo.

4. [Implantar seu modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - Crie um ponto final personalizado para o modelo de voz texto-a-fala e use-o para a síntese de fala em seus produtos, ferramentas e aplicativos.

## <a name="custom-neural-voices"></a>Vozes neurais personalizadas

O recurso de personalização de voz neural está atualmente em visualização pública, limitado a clientes selecionados. Preencha este formulário de [inscrição](https://go.microsoft.com/fwlink/?linkid=2108737) para começar.

> [!NOTE]
> Como parte do compromisso da Microsoft em projetar IA responsável, nossa intenção é proteger os direitos dos indivíduos e da sociedade e promover interações transparentes entre humanos e computadores. Por essa razão, o Custom Neural Voice geralmente não está disponível para todos os clientes. Você só pode ter acesso à tecnologia depois que seus aplicativos forem revisados e se comprometer a usá-la em alinhamento com nossos princípios éticos. Saiba mais sobre nosso [processo de gating de aplicativos.](https://aka.ms/custom-neural-gating-overview)

## <a name="set-up-your-azure-account"></a>Configure sua conta no Azure

Uma assinatura de serviço de fala é necessária antes que você possa usar o portal Custom Speech para criar um modelo personalizado. Siga estas instruções para criar uma assinatura do serviço Speech no Azure. Se você não tem uma conta no Azure, você pode se inscrever para uma nova.  

Depois de criar uma conta do Azure e uma assinatura do serviço Speech, você precisará entrar no portal de voz personalizada e conectar sua assinatura.

1. Obtenha a chave de assinatura do serviço Speech no portal Azure.
2. Faça login no [portal Voz Personalizada](https://aka.ms/custom-voice).
3. Selecione sua assinatura e crie um projeto de fala.
4. Se você quiser mudar para outra assinatura Speech, use o ícone de engrenagem localizado na navegação superior.

> [!NOTE]
> O serviço Custom Voice NÃO suporta a chave de avaliação gratuita de 30 dias. Você deve ter uma chave F0 ou S0 criada no Azure antes de poder usar o serviço.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e endpoints são organizados em **projetos** no portal Voz Personalizada. Cada projeto é específico para um país/idioma e o gênero da voz que você deseja criar. Por exemplo, você pode criar um projeto para uma voz feminina para os bots de bate-papo do seu call center que usam inglês nos Estados Unidos (en-US).

Para criar seu primeiro projeto, selecione a guia **Texto-para-Voz/Voz Personalizada** e clique em **Novo projeto**. Siga as instruções fornecidas pelo assistente para criar seu projeto. Depois de criar um projeto, você verá quatro guias: **Dados,** **Treinamento,** **Testes**e **Implantação.** Use os links fornecidos nos [próximos passos](#next-steps) para saber como usar cada guia.

> [!IMPORTANT]
> O [portal Custom Voice](https://aka.ms/custom-voice) foi atualizado recentemente! Se você criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, você precisa criar um novo projeto no novo portal para se conectar a essas entidades antigas.

## <a name="next-steps"></a>Próximas etapas

- [Preparar dados de voz personalizados](how-to-custom-voice-prepare-data.md)
- [Criar uma Voz Personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave suas amostras de voz](record-custom-voice-samples.md)
