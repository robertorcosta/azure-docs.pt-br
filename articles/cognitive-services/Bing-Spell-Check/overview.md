---
title: O que é API de Verificação Ortográfica do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a API de Verificação Ortográfica do Bing, que usa aprendizado de máquina e tradução automática estatística para fazer a verificação ortográfica contextual.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 205d7991551ca48c3ea2e61baef53f5d06bdcbd2
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200494"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é API de Verificação Ortográfica do Bing?

A API de Verificação Ortográfica do Bing permite fazer verificações gramaticais e ortográficas contextuais no texto. Embora a maioria dos verificadores ortográficos dependa de conjuntos de regras baseados em dicionário, o verificador ortográfico do Bing aproveita o aprendizado de máquina e a tradução automática estatística para fornecer correções precisas e contextuais. 

## <a name="features"></a>Recursos

| Recurso | Descrição |
|---------|---------|
|Modos múltiplos de verificação ortográfica     | Os modos múltiplos de verificação ortográfica permitem obter correções com foco na gramática e/ou na ortografia. |
|Reconhecimento de gírias e linguagem informal     | Reconheça expressões comuns e termos informais usados no texto.         |
|Diferenciação entre palavras semelhantes     | Encontre o uso correto entre palavras com sons semelhantes, mas com significados diferentes (por exemplo, "see" e "sea")        |
|Suporte de marca, título e uso popular     | Reconheça novas marcas, títulos e outras expressões comuns conforme vão surgindo |

## <a name="workflow"></a>Fluxo de trabalho

A API de Verificação Ortográfica do Bing é fácil de ser chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar respostas JSON. O serviço pode ser acessado usando a API REST ou os SDKs de Verificação Ortográfica do Bing. 

1. Crie uma [Conta da API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Caso você não tenha uma assinatura do Azure, crie uma conta gratuita. 
2. Envie uma solicitação à API de Pesquisa na Web do Bing.
3. Analisar a resposta JSON

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) da API de Pesquisa de Verificação Ortográfica de Bing para ver como verificar rapidamente uma variedade de textos.

Quando você estiver pronto para chamar a API, crie uma [Conta de API de serviços cognitivos](../../cognitive-services/cognitive-services-apis-create-account.md). Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.

Você também pode visitar a [página do hub da API de Pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.