---
title: Campos Azure Stream Analytics JobConfig.json
description: Este artigo lista os campos suportados para o arquivo Azure Stream Analytics JobConfig.json usado para criar empregos no Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617951"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Campos Azure Stream Analytics JobConfig.json

Os campos a seguir são suportados no arquivo *JobConfig.json* usado para [criar um trabalho do Azure Stream Analytics usando o Visual Studio Code](quick-create-vs-code.md).

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
|DataLocale|string|Não|A localização de dados do trabalho de análise de fluxo. Valor deve ser o nome de um suportado. Padrão para 'en-US' se nenhum especificado.|
|Política de erro de saída|string|Não|Indica a política a ser aplicada a eventos que chegam à saída e não podem ser gravados no armazenamento externo devido à má forma (valores de coluna ausentes, valores de coluna de tipo ou tamanho errados). - Pare ou solte|
|EventsLateArrivalmaxDelayinseconds|inteiro|Não|O atraso máximo tolerável em segundos onde os eventos chegam atrasados poderia ser incluído. O intervalo suportado é de -1 a 1814399 (20.23:59:59 dias) e -1 é usado para especificar a espera indefinidamente. Se a propriedade estiver ausente, é interpretado como se tivesse um valor de -1.|
|EventosOutofOrdermaxDelayinseconds|inteiro|Não|O atraso máximo tolerável em segundos onde eventos fora de ordem podem ser ajustados para voltar em ordem.|
|Política de EventosFora da Ordem|string|Não|Indica a política a ser aplicada a eventos que chegam fora de ordem no fluxo de eventos de entrada. - Ajustar ou soltar|
|StreamingUnits|inteiro|Sim|Especifica o número de unidades de streaming que o trabalho de streaming usa.|
|CompatibilityLevel|string|Não|Controla certos comportamentos de tempo de execução do trabalho de streaming. - Os valores aceitáveis são "1.0", "1.1", "1.2"|
|UseSystemAssignedIdentity|booleano|Não|Defina-se para permitir que este trabalho se comunique com outros serviços do Azure como ele mesmo usando uma identidade de diretório ativo do Azure gerenciada.|
|GlobalStorage.AccountName|string|Não|A conta de armazenamento global é usada para armazenar conteúdo relacionado ao seu trabalho de análise de fluxo, como instantâneos de dados de referência SQL.|
|globalstorage.accountkey|string|Não|Chave correspondente para conta de armazenamento global.|
|DataSourceCredentialDomínio|string|Não|Propriedade reservada para credencial de armazenamento local.|
|ScriptType|string|Sim|Propriedade reservada para indicar o tipo deste arquivo de origem. O valor aceitável é "JobConfig" para JobConfig.json.|
|Marcas|Pares de valores-chave JSON|Não|As tags são pares de nomes/valores que permitem categorizar recursos e exibir faturamento consolidado aplicando a mesma tag a vários recursos e grupos de recursos. Os nomes das marcas são insensíveis a casos e os valores da tag são sensíveis a maiúsculas e minúsculas.|

## <a name="next-steps"></a>Próximas etapas

* [Crie um trabalho de Análise de Fluxo azure no Visual Studio Code](quick-create-vs-code.md)
* [Test Stream Analytics consulta localmente com dados de amostra usando visual studio code](visual-studio-code-local-run.md)
* [Test Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando visual Studio Code](visual-studio-code-local-run-live-input.md)
*[Implantar um trabalho do Azure Stream Analytics usando o pacote NPM CI/CD](setup-cicd-vs-code.md)