---
title: Visão geral - Azure Serverless para aplicativos e soluções baseados em nuvem
description: Saiba como criar aplicativos e soluções baseados em nuvem sem se preocupar com infra-estrutura usando aplicativos azure logic e funções do Azure
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666543"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Visão geral para a construção de aplicativos e soluções baseadas em nuvem com aplicativos e funções azure logic e funções azure

[Aplicativos sem servidor](https://azure.microsoft.com/solutions/serverless/) oferecem benefícios como maior velocidade de desenvolvimento, código reduzido, simplicidade e escala. Este artigo abrange os diferentes atributos de soluções sem servidor e ofertas sem servidor do Azure.

## <a name="what-is-serverless"></a>O que é sem servidor?

Sem servidor não significa que não haja servidores, mas sim os desenvolvedores não têm que se preocupar com servidores. Uma grande parte do desenvolvimento de aplicativos tradicionais é responder às perguntas sobre soluções de dimensionamento, hospedagem e monitoramento para atender às demandas do aplicativo. Com o serverless, essas questões são cuidadas como parte da solução. Além disso, os aplicativos sem servidor são cobrados em um plano baseado no consumo. Se o aplicativo nunca for usado, não será cobrada nenhuma taxa. Esses recursos ajudam os desenvolvedores a se concentrarem apenas na lógica de negócios de uma solução.

Os principais serviços do Azure para sem servidor são [os Aplicativos azure Logic](https://azure.microsoft.com/services/logic-apps/) e [funções do Azure](https://azure.microsoft.com/services/functions/). Ambas as soluções seguem os princípios descritos anteriormente e ajudam os desenvolvedores a criar aplicativos robustos em nuvem com código mínimo.

## <a name="what-is-azure-logic-apps"></a>O que são Aplicativos Lógicos do Azure?

[O Azure Logic Apps](logic-apps-overview.md) oferece uma maneira de simplificar e implementar integrações escaláveis e fluxos de trabalho na nuvem. Este serviço fornece um designer visual para modelar e automatizar seu processo como uma série de etapas chamadas de fluxo de trabalho. Existem muitos [conectores](../connectors/apis-list.md) em serviços de nuvem e sistemas locais que conectam rapidamente um aplicativo sem servidor a outras APIs. Cada aplicativo de lógica começa com um gatilho, como "Quando uma conta é adicionada ao Dynamics CRM". Após o disparo do gatilho, o fluxo de trabalho pode executar combinações de ações, conversões e lógica condicional. Logic Apps é uma ótima escolha ao orquestrar diferentes funções do Azure em um processo, especialmente quando o processo requer interagir com um sistema externo ou API.

Para uma introdução aos Aplicativos Lógicos, comece com [criar seu primeiro aplicativo lógico](quickstart-create-first-logic-app-workflow.md). Para obter mais informações técnicas sobre logic apps, consulte a referência do [desenvolvedor](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>O que é o Azure Functions?

Azure Functions é um serviço para executar facilmente peças de código ou "funções" na nuvem. Você pode escrever apenas o código necessário para o problema atual, sem se preocupar com um aplicativo inteiro ou com a infra-estrutura necessária. Funções podem tornar o desenvolvimento ainda mais produtivo e você pode usar a linguagem de desenvolvimento de sua escolha, por exemplo, C#, F#, Node.js, Python ou PHP. Você paga apenas pelo tempo que seu código for executado e o Azure dimensiona conforme necessário.

Para começar com as funções do Azure, comece [com Crie sua primeira função Azure](../azure-functions/functions-create-first-azure-function.md). Para obter mais informações técnicas sobre funções, consulte a referência do [desenvolvedor](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Como posso construir e implantar aplicativos sem servidor no Azure?

O Azure fornece ferramentas ricas para desenvolver, implantar e gerenciar aplicativos sem servidor. Você pode criar aplicativos diretamente no portal Azure, com [ferramentas no Visual Studio](logic-apps-serverless-get-started-vs.md), ou Visual Studio [Code](quickstart-create-logic-apps-visual-studio-code.md). Depois de criar seu aplicativo, você pode [implantar esse aplicativo rapidamente com os modelos do Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). O Azure também fornece monitoramento, que você pode acessar através do portal Azure, através da API ou SDKs, ou com ferramentas integradas para logs do Monitor do Azure e Insights de Aplicativos.

## <a name="next-steps"></a>Próximas etapas

* [Construa um aplicativo sem servidor no Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Crie um painel de insights do cliente sem servidor](logic-apps-scenario-social-serverless.md)
* [Automatizar implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md)
