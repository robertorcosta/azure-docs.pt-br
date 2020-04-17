---
title: Filtros de tópico do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo explica como os assinantes podem definir quais mensagens querem receber de um tópico especificando filtros.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482059"
---
# <a name="topic-filters-and-actions"></a>Ações e filtros de tópico

Os assinantes podem definir quais mensagens desejam receber de um tópico. Essas mensagens são especificadas na forma de uma ou mais regras de assinatura nomeadas. Cada regra consiste em uma condição que seleciona mensagens específicas e uma ação que anota a mensagem selecionada. Para cada condição de regra com correspondência, a assinatura produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra com correspondência.

Cada assinatura de tópico recém-criada tem uma regra de assinatura padrão inicial. Se você não especificar explicitamente uma condição de filtro para a regra, o filtro aplicado é o filtro **true** que permite que todas as mensagens sejam selecionadas na assinatura. A regra padrão não tem nenhuma ação de anotação associada.

O Barramento de Serviço dá suporte a três condições de filtro:

-   *Filtros boolianos* – o **TrueFilter** e **FalseFilter** fazem com que todas as mensagens recebidas (**true**) ou nenhuma das mensagens recebidas (**false**) seja selecionada para a assinatura.

-   *Filtros SQL* – um **SqlFilter** contém uma expressão condicional do tipo SQL que é avaliada no agente em relação às propriedades do sistema e propriedades definidas pelo usuário das mensagens recebidas. Todas as propriedades de sistema devem ser prefixadas com `sys.` na expressão condicional. O [subconjunto sql para](service-bus-messaging-sql-filter.md) testes de condições de`EXISTS`filtro para`IS NULL`a existência de propriedades ( ), valores nulos (), lógicoNÃO/AND/OR, `LIKE`operadores relacionais, aritmética numérica simples e simples correspondência de padrão de texto com .

-   *Filtros de correlação* – um **CorrelationFilter** contém um conjunto de condições que são comparadas com uma ou mais das propriedades do sistema e do usuário de uma mensagem recebida. Um uso comum é corresponder à propriedade **CorrelationId,** mas o aplicativo também pode optar por corresponder às seguintes propriedades:

    - **Contenttype**
     - **Rótulo**
     - **MessageId**
     - **Replyto**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Para**
     - quaisquer propriedades definidas pelo usuário. 
     
     Uma correspondência existe quando o valor de uma propriedade de uma mensagem recebida é igual ao valor especificado no filtro de correlação. Para expressões de cadeia de caracteres, a comparação diferencia maiúsculas de minúsculas. Ao especificar várias propriedades de correspondência, o filtro as combina como uma condição AND lógica, o que significa que para haver a correspondência do filtro, todas as condições devem corresponder.

Todos os filtros avaliam as propriedades da mensagem. Os filtros não podem avaliar o corpo da mensagem.

Regras de filtro complexas requerem capacidade de processamento. Em particular, o uso de regras de filtro SQL causa menor throughput geral de mensagens no nível de assinatura, tópico e namespace. Sempre que possível, os aplicativos devem escolher filtros de correlação em vez de filtros semelhantes a SQL, porque eles são muito mais eficientes no processamento e têm menos impacto sobre o throughput.

## <a name="actions"></a>Ações

Com condições de filtro SQL, você pode definir uma ação que pode anotar a mensagem adicionando, removendo ou substituindo propriedades e seus valores. A ação [usa uma expressão do tipo SQL](service-bus-messaging-sql-filter.md) que depende vagamente da sintaxe da instrução SQL UPDATE. A ação é feita na mensagem depois de ser combinada e antes que a mensagem seja selecionada na assinatura. As alterações nas propriedades de mensagem são particulares para a mensagem copiada para a assinatura.

## <a name="usage-patterns"></a>Padrões de uso

O cenário de uso mais simples para um tópico é que cada assinatura obtém uma cópia de cada mensagem enviada para um tópico, o que permite um padrão de difusão.

Os filtros e as ações permitem dois grupos de padrões adicionais: particionamento e roteamento.

O particionamento usa filtros para distribuir mensagens através de várias assinaturas de tópico existentes de maneira previsível e mutuamente exclusiva. O padrão de particionamento é usado quando um sistema é escalado horizontalmente para lidar com muitos contextos diferentes em compartimentos funcionalmente idênticos que mantêm um subconjunto dos dados dos gerais. Por exemplo, informações de perfil do cliente. Com o particionamento, um editor envia a mensagem em um tópico sem a necessidade de qualquer conhecimento do modelo de particionamento. Depois, a mensagem é movida para a assinatura correta da qual ela pode ser recuperada pelo manipulador de mensagens da partição.

O roteamento usa filtros para distribuir mensagens através de assinaturas de tópico de maneira previsível, mas não necessariamente exclusiva. Em conjunto com o recurso de [encaminhamento automático](service-bus-auto-forwarding.md), os filtros de tópico podem ser usados para criar grafos de roteamento complexos dentro de um namespace do Barramento de Serviço para a distribuição de mensagens dentro de uma região do Azure. Com o Azure Functions ou os Aplicativos Lógicos do Azure atuando como uma ponte entre os namespaces do Barramento de Serviço do Azure, você pode criar tecnologias globais complexas com integração direta em aplicativos de linhas de negócios.


> [!NOTE]
> Atualmente, o portal Azure não permite especificar regras de filtro para assinaturas. Você pode usar qualquer um dos Modelos SDKs ou Azure Resource Manager suportados para definir regras de assinatura. 

## <a name="next-steps"></a>Próximas etapas
Veja as seguintes amostras: 

- [.NET - Tutorial básico de envio e recebimento com filtros](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Filtros tópicos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Script de tipo](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Modelo do Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


