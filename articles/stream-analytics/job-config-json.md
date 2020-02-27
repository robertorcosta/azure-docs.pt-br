---
title: Azure Stream Analytics campos JobConfig. JSON
description: Este artigo lista os campos com suporte para o arquivo Azure Stream Analytics JobConfig. JSON usado para criar trabalhos no Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617951"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics campos JobConfig. JSON

Os campos a seguir têm suporte no arquivo *JobConfig. JSON* usado para [criar um trabalho de Azure Stream Analytics usando Visual Studio Code](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nome|Type|Obrigatório|Valor|
|----|----|--------|-----|
|Datalocale|string|Não|A localidade de dados do trabalho do Stream Analytics. O valor deve ser o nome de um com suporte. O padrão é "en-US" se não houver nenhum especificado.|
|OutputErrorPolicy|string|Não|Indica a política a ser aplicada aos eventos que chegam à saída e não pode ser gravada no armazenamento externo devido a estar malformado (faltam valores de coluna, valores de coluna de tipo ou tamanho incorreto). -Parar ou descartar|
|EventsLateArrivalMaxDelayInSeconds|inteiro|Não|O atraso máximo tolerável em segundos em que os eventos chegando ao final podem ser incluídos. O intervalo com suporte é de-1 a 1814399 (20.23:59:59 dias) e-1 é usado para especificar a espera indefinidamente. Se a propriedade estiver ausente, ela será interpretada para ter um valor de-1.|
|EventsOutOfOrderMaxDelayInSeconds|inteiro|Não|O atraso máximo tolerável em segundos em que os eventos fora de ordem podem ser ajustados para volta na ordem.|
|EventsOutOfOrderPolicy|string|Não|Indica a política a ser aplicada aos eventos que chegam fora de ordem no fluxo de eventos de entrada. -Ajustar ou descartar|
|StreamingUnits|inteiro|Sim|Especifica o número de unidades de streaming que o trabalho de streaming usa.|
|CompatibilityLevel|string|Não|Controla determinados comportamentos de tempo de execução do trabalho de streaming. -Os valores aceitáveis são "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|booleano|Não|Defina true para permitir que esse trabalho se comunique com outros serviços do Azure usando uma identidade de Azure Active Directory gerenciada.|
|GlobalStorage. AccountName|string|Não|A conta de armazenamento global é usada para armazenar o conteúdo relacionado ao trabalho do Stream Analytics, como instantâneos de dados de referência do SQL.|
|GlobalStorage. AccountKey|string|Não|Chave correspondente para a conta de armazenamento global.|
|DataSourceCredentialDomain|string|Não|Propriedade reservada para armazenamento local de credencial.|
|ScriptType|string|Sim|Propriedade reservada para indicar o tipo desse arquivo de origem. O valor aceitável é "JobConfig" para JobConfig. JSON.|
|Marcas|Pares chave-valor JSON|Não|As marcas são pares de nome/valor que permitem categorizar recursos e exibir a cobrança consolidada aplicando a mesma marca a vários recursos e grupos de recursos. Os nomes de marca diferenciam maiúsculas de minúsculas e os valores de marca diferenciam maiúsculas de minúsculas.|

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-vs-code.md)
* [Testar Stream Analytics consultas localmente com dados de exemplo usando Visual Studio Code](visual-studio-code-local-run.md)
* [Testar Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[implantar um trabalho de Azure Stream Analytics usando CI/CD NPM pacote](setup-cicd-vs-code.md)