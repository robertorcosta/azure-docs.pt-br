---
title: Recursos para desenvolvedores-Reconhecimento vocal
description: SDKs, APIs REST, CLI, ajudam você a desenvolver aplicativos Reconhecimento vocal (LUIS) em sua linguagem de programação. Gerencie seus recursos do Azure e previsões do LUIS.
ms.topic: reference
ms.date: 02/09/2020
ms.openlocfilehash: ed869b7022e43b8ecf8c1f05bb3c6f0919076818
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119970"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Recursos de desenvolvedor de SDK, REST e CLI para Reconhecimento vocal (LUIS)

SDKs, APIs REST, CLI, ajudam você a desenvolver aplicativos Reconhecimento vocal (LUIS) em sua linguagem de programação. Gerencie seus recursos do Azure e previsões do LUIS. 

## <a name="azure-resource-management"></a>Gerenciamento de recursos do Azure

Use a camada de gerenciamento de serviços cognitivas do Azure para criar, editar, listar e excluir o Reconhecimento vocal ou recurso de serviço cognitiva.

Encontre a documentação de referência com base na ferramenta:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [PowerShell do Azure RM](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Solicitações de criação e previsão de Reconhecimento vocal

O serviço de Reconhecimento vocal é acessado de um recurso do Azure que você precisa criar. Há dois recursos:

* Use o recurso de **criação** para treinamento para criar, editar, treinar e publicar.
* Use a **previsão** de tempo de execução para enviar o texto do usuário e receber uma previsão.

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Use o [código de exemplo de serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-quickstart-code) para aprender e usar as tarefas mais comuns.

### <a name="rest-apis"></a>APIs REST

As APIS de ponto de extremidade de criação e de previsão estão disponíveis em APIs REST:

|Type|Versão|
|--|--|
|Criação|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[visualização v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Previsão|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>SDKs baseados em linguagem

|Linguagem |Documentação de referência|Pacote|Exemplos|Inícios rápidos|
|--|--|--|--|--|
|C#|[Criação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Criação de NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Previsão do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Exemplos do SDK do .net](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Criar e gerenciar aplicativo](sdk-authoring.md?pivots=programming-language-csharp)<br>[Ponto de extremidade de previsão de consulta](sdk-query-prediction-endpoint.md)|
|Go|[Criação e previsão](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Criação e previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Criação e previsão](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Criação do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Previsão do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Criação e previsão](luis-get-started-get-intent-from-rest.md)
|Node.js|[Criação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Criação de NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Previsão de NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Criação](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsão](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Criação e previsão usando REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Criação e previsão](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Criação](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Criação](sdk-authoring.md?pivots=programming-language-python)<br>[Previsão usando REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contêineres

O Reconhecimento vocal (LUIS) fornece um [contêiner](luis-container-howto.md) para fornecer versões locais e contidas do seu aplicativo.

### <a name="export-and-import-formats"></a>Exportar e importar formatos

Reconhecimento vocal fornece a capacidade de gerenciar seu aplicativo e seus modelos em um formato JSON, o formato `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) e um pacote compactado para o contêiner reconhecimento vocal.

A importação e a exportação desses formatos estão disponíveis nas APIs e no portal do LUIS. O portal fornece importação e exportação como parte da lista de aplicativos e lista de versões.

## <a name="other-tools-and-sdks"></a>Outras ferramentas e SDKs

A estrutura de bot está disponível como [um SDK](https://github.com/Microsoft/botframework) em uma variedade de linguagens e como um serviço usando o [serviço de bot do Azure](https://dev.botframework.com/).

O bot Framework fornece [várias ferramentas](https://github.com/microsoft/botbuilder-tools) para ajudar com reconhecimento vocal, incluindo:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -criar modelos de reconhecimento de linguagem Luis usando arquivos de redução
* [CLI do Luis](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – criar e gerenciar seus aplicativos do Luis.ai
* [Expedição](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-gerenciar aplicativos pai e filho
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – gere C#/typescript classes de backup automaticamente para suas intenções e entidades de Luis.
* [Emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) -um aplicativo de área de trabalho que permite que os desenvolvedores de bot testem e depurem bots criados usando o SDK do bot Framework


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [códigos de erro http](luis-reference-response-codes.md) comuns
* [Documentação de referência](https://docs.microsoft.com/azure/index#pivot=sdkstools) para todas as APIs e SDKs
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) e [serviço de bot do Azure](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contêineres cognitivas](../cognitive-services-container-support.md)
