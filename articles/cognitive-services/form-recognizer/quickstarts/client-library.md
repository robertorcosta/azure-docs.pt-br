---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Use a biblioteca de clientes do Reconhecimento de Formulários para criar um aplicativo de processamento de formulários que extraia pares de chave/valor e dados de tabela de seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: forms processing, automated data processing
ms.openlocfilehash: f01adc472f94d679366af50c136ddc020b79b811
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808565"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>Início Rápido: Usar a biblioteca de clientes do Reconhecimento de Formulários

Introdução ao Reconhecimento de Formulários usando uma linguagem de sua escolha. O Reconhecimento de Formulários do Azure é um serviço cognitivo que permite criar software de processamento de dados automatizado usando a tecnologia de machine learning. Identifique e extraia pares de texto, pares chave/valor e dados de tabela de seus documentos de formulário&mdash;o serviço gera dados estruturados que incluem as relações no arquivo original. Siga essas etapas para instalar o pacote do SDK e testar o código de exemplo para tarefas básicas. No momento, a biblioteca de clientes do Reconhecimento de Formulários se destina à v2.0 do serviço de Reconhecimento de Formulários.

Use a biblioteca de clientes do Reconhecimento de Formulários para:

* [Reconhecer o conteúdo do formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Reconhecer cartões de visita](#recognize-business-cards)
* [Reconhecer faturas](#recognize-invoices)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end