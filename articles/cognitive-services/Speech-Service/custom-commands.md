---
title: Comandos Personalizados (Visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, recursos e restrições para comandos personalizados (Preview), uma solução para criar aplicativos de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367832"
---
# <a name="what-are-custom-commands-preview"></a>O que são Comandos Personalizados (Preview)?

Aplicativos de voz, como [assistentes de voz,](voice-assistants.md) ouvem os usuários e tomam uma ação em resposta, muitas vezes falando de volta. Eles usam [a fala-para-texto](speech-to-text.md) para transcrever a fala do usuário e, em seguida, tomar medidas sobre a compreensão da linguagem natural do texto. Esta ação frequentemente inclui saída falada do assistente gerado com [texto-para-fala](text-to-speech.md). Os dispositivos se conectam aos assistentes com `DialogServiceConnector` o objeto do Speech SDK.

**Custom Commands (Preview)** é uma solução simplificada para criar aplicativos de voz. Ele oferece uma experiência de criação unificada, um modelo de hospedagem automática e uma complexidade relativamente menor em comparação com outras opções como [direct line speech](direct-line-speech.md). Essa simplificação, no entanto, vem com uma redução da flexibilidade. Assim, os Comandos Personalizados (Preview) são mais adequados para a conclusão de tarefas ou cenários de comando e controle. É particularmente compatível com internet das coisas (IoT) e dispositivos sem cabeça.

Para uma interação conversacional complexa e integração com outras soluções, como a [Solução assistente virtual e o modelo corporativo,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) você é encorajado a usar o Direct Line Speech.

Bons candidatos para Comandos Personalizados (Preview) têm um vocabulário fixo com conjuntos bem definidos de variáveis. Por exemplo, tarefas de automação residencial, como controlar um termostato, são ideais.

   ![Exemplos de cenários de conclusão de tarefas](media/voice-assistants/task-completion-examples.png "exemplos de conclusão de tarefas")

## <a name="getting-started-with-custom-commands-preview"></a>Começando com comandos personalizados (Visualização)

O primeiro passo para usar comandos personalizados (Preview) para fazer um aplicativo de voz é [obter uma chave de assinatura de voz](get-started.md) e acessar o Construtor de Comandos Personalizados (Preview) no Estúdio de [Voz](https://speech.microsoft.com). A partir daí, você pode criar um novo aplicativo de comandos personalizados (Preview) e publicá-lo, após o qual um aplicativo no dispositivo pode se comunicar com ele usando o Speech SDK.

   ![Fluxo de autoria para Comandos Personalizados (Visualização)](media/voice-assistants/custom-commands-flow.png "O fluxo de autoria de Comandos Personalizados (Preview)")

Oferecemos partidas rápidas projetadas para que você esteja executando o código em menos de 10 minutos.

* [Criar um aplicativo de comandos personalizados (Preview)](quickstart-custom-speech-commands-create-new.md)
* [Crie um aplicativo de comandos personalizados (Preview) com parâmetros](quickstart-custom-speech-commands-create-parameters.md)
* [Conecte-se a um aplicativo de comandos personalizados (Preview) com o Speech SDK,C #](quickstart-custom-speech-commands-speech-sdk.md)

Uma vez que você terminar com as partidas rápidas, explore nossos "como fazer".

- [Adicionar validações aos parâmetros de comando personalizado](./how-to-custom-speech-commands-validations.md)
- [Cumprir comandos no cliente com o Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Adicionar uma confirmação a um Comando Personalizado](./how-to-custom-speech-commands-confirmations.md)
- [Adicionar uma correção de uma etapa a um Comando Personalizado](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Vá ao Speech Studio para experimentar comandos personalizados](https://speech.microsoft.com)
* [Obter o SDK de Fala](speech-sdk.md)
