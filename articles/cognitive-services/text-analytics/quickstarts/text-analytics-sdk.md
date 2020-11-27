---
title: 'Início Rápido: Mineração de texto usando a biblioteca de clientes da Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para executar análises de sentimentos e muito mais com a API de Análise de Texto de Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 10/07/2020
ms.author: aahi
keywords: mineração de texto, análise de sentimentos, análise de texto
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 5a0856df71f87e49c1a7d627ba92419352c796d5
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980926"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Início Rápido: Usar a biblioteca de clientes da Análise de Texto

Use este artigo para começar a usar a biblioteca de clientes de Análise de Texto. Siga essas etapas para instalar o pacote e testar o código de exemplo para minerar texto.

Use a biblioteca de clientes da Análise de Texto para executar:

* Análise de sentimento
* Detecção de idioma
* Reconhecimento de entidade
* Extração de frases-chave
* Mineração de opinião

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A última versão estável da API de Análise de Texto é `3.0`.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo.
> * Caso deseje usar a Análise de Texto para operações assíncronas ou de integridade, confira os exemplos no GitHub para [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) ou [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * A última versão estável da API de Análise de Texto é `3.0`.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo.
Caso deseje usar a Análise de Texto para operações assíncronas ou de integridade, confira os exemplos no GitHub para [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) ou [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A última versão estável da API de Análise de Texto é `3.0`.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo.
> * Você também pode executar esta versão da biblioteca de clientes da Análise de Texto [no seu navegador](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A última versão estável da API de Análise de Texto é `3.0`.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo. Caso deseje usar a Análise de Texto para operações assíncronas ou de integridade, confira os exemplos no GitHub para [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) ou [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Suporte a idiomas adicionais

Se você clicou nessa guia, provavelmente não viu um início rápido em sua linguagem de programação favorita. Não se preocupe. Temos inícios rápidos adicionais disponíveis. Use a tabela para encontrar a amostra correta para a sua linguagem de programação.

| Linguagem | Versão disponível | 
|----------|------------------------|
| Ruby     | [Versão 2.1](ruby-sdk.md) | 
| Go       | [Versão 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar uma solução](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar o idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
