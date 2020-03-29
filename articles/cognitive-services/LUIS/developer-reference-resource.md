---
title: Recursos do desenvolvedor - Compreensão de idiomas
description: SDKs, APIs REST, CLI, ajudam você a desenvolver aplicativos de Compreensão de Linguagem (LUIS) em sua linguagem de programação. Gerencie seus recursos do Azure e previsões DE LUIS.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457977"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Recursos de desenvolvedor SDK, REST e CLI para compreensão de idiomas (LUIS)

SDKs, APIs REST, CLI, ajudam você a desenvolver aplicativos de Compreensão de Linguagem (LUIS) em sua linguagem de programação. Gerencie seus recursos do Azure e previsões DE LUIS.

## <a name="azure-resource-management"></a>Gerenciamento de recursos do Azure

Use a camada Azure Cognitive Services Management para criar, editar, listar e excluir o recurso Decompreensão de idiomas ou serviço cognitivo.

Encontre a documentação de referência com base na ferramenta:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [PowerShell do Azure RM](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Compreensão da linguagem de autoria e pedidos de previsão

O serviço de compreensão de idiomas é acessado a partir de um recurso do Azure que você precisa criar. Existem dois recursos:

* Use o recurso **de autoria** para treinamento para criar, editar, treinar e publicar.
* Use a **previsão** de tempo de execução para enviar o texto do usuário e receber uma previsão.

Saiba mais sobre o ponto final da [previsão v3](luis-migration-api-v3.md).

Use [o código de amostra de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-quickstart-code) para aprender e usar as tarefas mais comuns.

### <a name="rest-specifications"></a>Especificações de REST

As [especificações do LUIS REST,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)juntamente com todas as [especificações do Azure REST,](https://github.com/Azure/azure-rest-api-specs)estão disponíveis publicamente no GitHub.

### <a name="rest-apis"></a>APIs REST

Tanto a autoria quanto o ponto final de previsão APIS estão disponíveis nas APIs REST:

|Type|Versão|
|--|--|
|Criação|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[pré-visualização V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Previsão|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Pontos finais do REST

Luis atualmente possui 2 tipos de pontos finais:

* autoria sobre o ponto final de treinamento
* previsão de consulta no ponto final de tempo de execução.

|Finalidade|URL|
|--|--|
|Autoria sobre o ponto final do treinamento|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime - todas as previsões sobre o ponto final de tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime - previsão de versões no ponto final de tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime - previsão de slot no ponto final de tempo de execução|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

A tabela a seguir explica os parâmetros, indicados com chaves `{}`, na tabela anterior.

|Parâmetro|Finalidade|
|--|--|
|`your-resource-name`|Nome do recurso do Azure|
|`q` ou `query`|texto de expressão enviado do aplicativo cliente como chat bot|
|`version`|Nome da versão de 10 caracteres|
|`slot`| `production` ou `staging`|

### <a name="language-based-sdks"></a>SDKs baseados em idioma

|Idioma |Documentação de referência|Pacote|Exemplos|Inícios rápidos|
|--|--|--|--|--|
|C#|[Criação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet autoria](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão de NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net amostras de SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Criar e gerenciar um aplicativo](sdk-authoring.md?pivots=programming-language-csharp)<br>[Ponto de extremidade de previsão da consulta](sdk-query-prediction-endpoint.md)|
|Go|[Autoria e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Autoria e Previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Autoria e previsão](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Autoria de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão de Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Autoria e Previsão](luis-get-started-get-intent-from-rest.md)
|Node.js|[Criação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Autoria do NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Previsão do NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Autoria e Previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Autoria e previsão](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Criação](sdk-authoring.md?pivots=programming-language-python)<br>[Previsão usando REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contêineres

O Language Understanding (LUIS) fornece um [contêiner](luis-container-howto.md) para fornecer no local e as versões contidas do seu aplicativo.

### <a name="export-and-import-formats"></a>Formatos de exportação e importação

O Language Understanding fornece a capacidade de gerenciar seu aplicativo `.LU` e seus modelos em um formato JSON, o formato[(LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)e um pacote compactado para o contêiner Language Understanding.

A importação e exportação desses formatos está disponível nas APIs e no portal LUIS. O portal fornece importação e exportação como parte da lista de aplicativos e versões.

## <a name="other-tools-and-sdks"></a>Outras ferramentas e SDKs

A estrutura bot está disponível como [um SDK](https://github.com/Microsoft/botframework) em uma variedade de idiomas e como um serviço usando [o Azure Bot Service](https://dev.botframework.com/).

A estrutura do Bot fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar na compreensão do idioma, incluindo:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Construa modelos de compreensão de linguagem LUIS usando arquivos de marcação
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Crie e gerencie seus aplicativos de LUIS.ai
* [Despacho](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- gerenciar aplicativos de pais e filhos
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Gerar aulas de C#/Typescript de backup para suas intenções e entidades DE LUIS.
* [Emulador Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) - um aplicativo de desktop que permite aos desenvolvedores de bots testar e depurar bots construídos usando o Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - uma ferramenta de desenvolvimento integrada para desenvolvedores e equipes multidisciplinares para construir bots e experiências de conversação com o Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Ferramentas que suportam integração contínua e implantação de serviços NLU.

## <a name="next-steps"></a>Próximas etapas

* Conheça os [códigos de erro HTTP comuns](luis-reference-response-codes.md)
* [Documentação de referência](https://docs.microsoft.com/azure/index) para todas as APIs e SDKs
* [Estrutura bot](https://github.com/Microsoft/botbuilder-dotnet) e [Serviço de Bot sinuoso](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Recipientes Cognitivos](../cognitive-services-container-support.md)
