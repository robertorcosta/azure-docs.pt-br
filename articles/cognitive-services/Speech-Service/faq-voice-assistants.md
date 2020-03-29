---
title: Assistentes de voz frequentemente fizeram perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre assistentes de voz usando Comandos Personalizados (Preview) ou o canal Direct Line Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110351"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Assistentes de voz frequentemente fizeram perguntas

Se você não conseguir encontrar respostas para suas perguntas neste documento, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: O que é um assistente de voz?**

**A:** Como cortana, um assistente de voz é uma solução que ouve as declarações faladas de um usuário, analisa o conteúdo dessas expressões para significado, realiza uma ou mais ações em resposta à intenção do enunciado e, em seguida, fornece uma resposta ao usuário que muitas vezes inclui um componente falado. É uma experiência de "voz-in, voz para interagir com um sistema. os autores do assistente de voz `DialogServiceConnector` criam um aplicativo no dispositivo usando o SDK no Speech para se comunicar com um assistente criado usando [Comandos Personalizados (Preview)](custom-commands.md) ou o canal [Direct Line Speech](direct-line-speech.md) do Bot Framework. Esses assistentes podem usar palavras-chave personalizadas, voz personalizada e voz personalizada para fornecer uma experiência adaptada à sua marca ou produto.

**P: Devo usar comandos personalizados (Preview) ou direct line speech? Qual é a diferença?**

**A:** [Custom Commands (Preview)](custom-commands.md) é um conjunto de ferramentas de menor complexidade para criar e hospedar facilmente um assistente que seja adequado para cenários de conclusão de tarefas. [O Direct Line Speech](direct-line-speech.md) oferece recursos mais ricos e sofisticados que podem permitir cenários de conversação robustos. Veja a [comparação de soluções assistentes](voice-assistants.md#choosing-an-assistant-solution) para obter mais informações.

**P: Como faço para começar?**

**A:** A melhor maneira de começar com a criação de um aplicativo de comandos personalizados (Preview) ou bot framework básico.

- [Criar um aplicativo de comandos personalizados (Preview)](quickstart-custom-speech-commands-create-new.md)
- [Crie um bot framework básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Conecte um bot ao canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Depuração

**P: Onde está meu segredo de canal?**

**A:** Se você usou a versão de pré-visualização do Direct Line Speech ou está lendo documentação relacionada, você pode esperar encontrar uma chave secreta na página de registro do canal Direct Line Speech. O método `FromBotSecret` de `DialogServiceConfig` fábrica v1.7 no Speech SDK também espera esse valor.

A versão mais recente do Direct Line Speech simplifica o processo de contato com seu bot a partir de um dispositivo. Na página de registro do canal, a queda no topo associa o registro do canal Direct Line Speech com um recurso de fala. Uma vez associado, o SDK de `BotFrameworkConfig::FromSubscription` fala v1.8 `DialogServiceConnector` inclui um método de fábrica que configurará um para entrar em contato com o bot que você associou à sua assinatura.

Se você ainda estiver migrando seu aplicativo cliente de v1.7 para v1.8, `DialogServiceConfig::FromBotSecret` pode continuar a trabalhar com um valor não vazio e não nulo para seu parâmetro secreto de canal, por exemplo, o segredo anterior que você usou. Ele será simplesmente ignorado ao usar uma assinatura de fala associada a um registro de canal mais novo. Observe que o valor _deve_ ser não nulo e não vazio, pois estes são verificados no dispositivo antes que a associação do lado do serviço seja relevante.

Para um guia mais detalhado, consulte a [seção tutorial](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) que acompanha o registro do canal.

**Q: Eu recebo um erro 401 ao conectar e nada funciona. Sei que minha chave de assinatura de discurso é válida. O que está acontecendo?**

**A:** Ao gerenciar sua assinatura no portal Azure, certifique-se de que você está usando o recurso **Speech** (Microsoft.CognitiveServicesSpeechServices, "Speech") e _não_ o recurso **Cognitive Services** (Microsoft.CognitiveServicesAllInOne, "All Cognitive Services"). Além disso, verifique o [suporte da região de serviço de fala para assistentes de voz](regions.md#voice-assistants).

![assinatura correta para discurso de linha direta](media/voice-assistants/faq-supported-subscription.png "exemplo de uma assinatura de fala compatível")

**P: Eu recebo texto `DialogServiceConnector`de reconhecimento de volta do meu , mas eu vejo um erro '1011' e nada do meu bot. Porque?**

**A:** Este erro indica um problema de comunicação entre seu assistente e o serviço de assistente de voz.

- Para comandos personalizados (Visualização), certifique-se de que seu aplicativo de comandos personalizados (Preview) seja publicado
- Para o Direct Line Speech, certifique-se de que você [conectou seu bot ao canal Direct Line Speech,](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) [adicionou suporte](https://aka.ms/botframework/addstreamingprotocolsupport) ao protocolo de streaming ao seu bot (com o suporte relacionado ao Soquete da Web) e, em seguida, verifique se seu bot está respondendo às solicitações recebidas do canal.

**P: Este código ainda não funciona e/ou estou recebendo `DialogServiceConnector`um erro diferente ao usar um . O que eu devo fazer?**

**A:** O registro baseado em arquivos fornece substancialmente mais detalhes e pode ajudar a acelerar as solicitações de suporte. Para habilitar essa funcionalidade, veja [como usar o registro de arquivos](how-to-use-logging.md).

## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)
