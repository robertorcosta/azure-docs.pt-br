---
title: Use serviços cognitivos de PNL para enriquecer conversas
titleSuffix: Azure Cognitive Services
description: Os Serviços Cognitivos fornecem dois serviços de processamento de linguagem natural, o Language Understanding e o QnA Maker, cada um com um propósito diferente. Entenda quando usar cada serviço e como eles elogiam uns aos outros.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73818205"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>Use serviços cognitivos com processamento de linguagem natural (PNL) para enriquecer conversas com bots

Os Serviços Cognitivos fornecem dois serviços de processamento de linguagem natural, [o Language Understanding](what-is-luis.md) e o [QnA Maker](../qnamaker/overview/overview.md), cada um com um propósito diferente. Entenda quando usar cada serviço e como eles elogiam uns aos outros. 

O processamento de linguagem natural (NLP) permite que seu aplicativo cliente, como um bot de bate-papo, trabalhe com seus usuários, usando linguagem natural. Um usuário digita uma frase ou frase. O texto do usuário pode ter gramática, ortografia e pontuação ruins. O Serviço Cognitivo pode trabalhar através da frase do usuário de qualquer maneira, devolvendo informações que o bot de chat precisa para ajudar o usuário. 

## <a name="cognitive-services-with-nlp"></a>Serviços Cognitivos com PNL

O Entendimento de Linguagem (LUIS) e o QnA Maker fornecem PNL. O aplicativo cliente envia texto de linguagem natural. O serviço pega o texto, processa e retorna um resultado. 

## <a name="when-to-use-each-service"></a>Quando usar cada serviço

O Entendimento da Linguagem (LUIS) e o QnA Maker resolvem diferentes problemas. Luis determina a intenção do texto de um usuário (conhecido como enunciado), enquanto o QnA Maker determina a resposta ao texto de um usuário (conhecido como consulta). 

Para escolher o serviço correto, você precisa entender o texto do usuário proveniente do aplicativo cliente e quais informações o aplicativo cliente precisa obter do Serviço Cognitivo.

Se o seu bot `How do I get to the Human Resources building on the Seattle North campus?`de bate-papo receber o texto, use o gráfico abaixo para entender como cada serviço funciona com o texto.

|Serviço|O aplicativo cliente determina|
|--|--|
|LUIS|**Determina a intenção de** texto do usuário - o serviço não retorna a resposta à pergunta. Por exemplo, este texto é `FindLocation` classificado como correspondendo à intenção.<br>|
|QnA Maker|**Retorna a resposta à pergunta a** partir de uma base de conhecimento personalizada. Por exemplo, este texto é determinado como uma `Get on the #9 bus and get off at Franklin street`pergunta com a resposta de texto estático de .|
|||

## <a name="when-do-you-use-luis"></a>Quando você usa luis? 

Use LUIS quando precisar saber a intenção do enunciado como parte de um processo no bot de bate-papo. Continuando com o `How do I get to the Human Resources building on the Seattle North campus?`texto de exemplo, , uma vez que você sabe que a intenção do usuário é encontrar um local, você pode passar detalhes sobre o enunciado (retirado com entidades) para outro serviço, como um servidor de transporte, para obter a resposta. 

Você não precisa combinar LUIS e QnA Maker para determinar a intenção. 

Você pode combinar os dois serviços para essa expressão, se o bot de bate-papo precisar processar o texto com base em intenções e entidades (usando LUIS) bem como encontrar a resposta de texto estática específica (usando O Criador de QnA).

## <a name="when-do-you-use-qna-maker"></a>Quando você usa o QnA Maker? 

Use qnA Maker quando você tiver uma base de conhecimento estático de respostas. Essa base de dados de conhecimento é personalizada para suas necessidades e você a criou com documentos como PDFs e URLs. 

Continuando com o exemplo `How do I get to the Human Resources building on the Seattle North campus?`de enunciado, envie o texto, como uma consulta, para o seu serviço QnA Maker publicado e receba a melhor resposta. 

Você não precisa combinar LUIS e QnA Maker para determinar a resposta à pergunta.

Você pode combinar os dois serviços para essa expressão, se o bot de bate-papo precisar processar o texto com base em intenções e entidades (usando LUIS) bem como encontrar a resposta (usando o QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Use ambos os serviços quando sua base de conhecimento estiver incompleta

Se você estiver construindo sua base de conhecimento qnA Maker, mas saiba que o domínio do assunto está mudando (como informações oportunas), você pode combinar serviços LUIS e QnA Maker. Isso permite que você use as informações em sua base de conhecimento, mas também use LUIS para determinar a intenção de um usuário. Uma vez que o aplicativo cliente tenha a intenção, ele pode solicitar informações relevantes de outra fonte. 

Seu aplicativo cliente precisaria monitorar as respostas do CRIADOR DE LUIS e QnA para pontuação. Se a pontuação do QnA Maker estiver abaixo de algum limite arbitrário, use as informações de intenção e entidade retornadas do LUIS para passar as informações para um serviço de terceiros.

Continuando com o `How do I get to the Human Resources building on the Seattle North campus?`texto de exemplo, suponha que o QnA Maker retorne uma baixa pontuação de confiança. Use a intenção devolvida `FindLocation` do LUIS, e quaisquer `Human Resources building` `Seattle North campus`entidades extraídas, como e , para enviar essas informações para um serviço de mapeamento ou busca para outra resposta. 

Você pode apresentar esta resposta de terceiros ao usuário para validação. Uma vez que você tenha a aprovação do usuário, você pode voltar ao QnA Maker para adicionar as informações para aumentar seu conhecimento. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Use ambos os serviços quando seu bot de bate-papo precisar de mais informações

Se o seu bot de bate-papo precisar de mais informações do que qualquer serviço fornece, para continuar através de uma árvore de decisão, use os serviços e processe ambas as respostas no aplicativo do cliente. 

Use a ferramenta Cli Dispatch do Bot **[framework Dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** para ajudar a construir um processo para trabalhar com ambos os serviços. Esta ferramenta constrói um aplicativo de intenções LUIS superior que despacha entre LUIS e QnA Maker como aplicativos infantis. 

Use a amostra do construtor bot, **NLP com despacho**, em [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) ou [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), para implementar este tipo de bot de bate-papo. 

## <a name="best-practices"></a>Práticas recomendadas

Implementar as melhores práticas para cada serviço:

* [Melhores](luis-concept-best-practices.md) práticas de LUIS
* Melhores práticas [do QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Confira também

* [Reconhecimento Vocal (LUIS)](what-is-luis.md)
* [Fabricante de QnA](../qnamaker/overview/overview.md)
* [Despachar CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Amostras da estrutura de bots](https://github.com/Microsoft/BotBuilder-Samples)
* [Serviço de bot azure](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulador de bots azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Bate-papo web do bot framework](https://github.com/microsoft/BotFramework-WebChat)