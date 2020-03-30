---
title: Referência de esquema de definição de linguagem de fluxo de trabalho
description: Guia de referência para o esquema JSON e sintaxe para a Linguagem de Definição de Fluxo de Trabalho que descreve fluxos de trabalho em Aplicativos de Lógica Do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283856"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Guia de referência de esquema para o idioma de definição de fluxo de trabalho em aplicativos de lógica do Azure

Quando você cria um aplicativo lógico no [Azure Logic Apps](../logic-apps/logic-apps-overview.md), seu aplicativo lógico tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que é executada em seu aplicativo lógico. Essa definição de fluxo de trabalho usa [JSON](https://www.json.org/) e segue uma estrutura validada pelo esquema de linguagem de definição de fluxo de trabalho. Essa referência fornece uma visão geral sobre essa estrutura e como o esquema define atributos na definição do fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura da definição de fluxo de trabalho

Uma definição de fluxo de trabalho sempre inclui um gatilho para instanciar seu aplicativo lógico, além de uma ou mais ações que são executadas após o disparo do gatilho.

Esta é a estrutura de alto nível de uma definição de fluxo de trabalho:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Atributo | Obrigatório | Descrição |
|-----------|----------|-------------|
| `definition` | Sim | O elemento inicial da definição de fluxo de trabalho |
| `$schema` | Somente ao referenciar uma definição de fluxo de trabalho externamente | O local do arquivo de esquema JSON que descreve a versão da Linguagem de Definição de Fluxo de Trabalho, que pode ser encontrado aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Não | As definições para uma ou mais ações para executar em tempo de execução do fluxo de trabalho. Para obter mais informações, consulte [Gatilhos e ações](#triggers-actions). <p><p>Máximo de ações: 250 |
| `contentVersion` | Não | O número de versão da definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implantar um fluxo de trabalho, especifique um valor a ser usado. |
| `outputs` | Não | As definições para que as saídas retornem de uma execução de fluxo de trabalho. Para obter mais informações, consulte [Saídas](#outputs). <p><p>Máximo de saídas: 10 |
| `parameters` | Não | As definições para um ou mais parâmetros que passam os valores a serem usados no tempo de execução do seu aplicativo lógico. Para obter mais informações, confira [Parâmetros](#parameters). <p><p>Máximo de parâmetros: 50 |
| `staticResults` | Não | As definições para um ou mais resultados estáticos retornados por ações como saídas simuladas quando resultados estáticos são ativados nessas ações. Em cada definição `runtimeConfiguration.staticResult.name` de ação, o `staticResults`atributo faz referência à definição correspondente no interior . Para obter mais informações, consulte [resultados estáticos](#static-results). |
| `triggers` | Não | As definições de um ou mais gatilhos que criam uma instância do fluxo de trabalho. É possível definir mais de um gatilho, mas apenas com a Linguagem de Definição de Fluxo de Trabalho, e não visualmente por meio do Designer de Aplicativos Lógicos. Para obter mais informações, consulte [Gatilhos e ações](#triggers-actions). <p><p>Máximo da gatilhos: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Gatilhos e ações

Em uma definição de fluxo de trabalho, as seções `triggers` e `actions` definem as chamadas que ocorrem durante a execução do fluxo de trabalho. Para conhecer a sintaxe e obter mais informações sobre essas seções, confira [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parâmetros

O ciclo de vida de implantação geralmente tem diferentes ambientes para desenvolvimento, teste, encenação e produção. Ao implantar aplicativos lógicos em vários ambientes, você provavelmente deseja usar valores diferentes, como strings de conexão, com base em suas necessidades de implantação. Ou, você pode ter valores que você deseja reutilizar em todo o seu aplicativo lógico sem codificação difícil ou que mudam com freqüência. Na seção de `parameters` definição do fluxo de trabalho, você pode definir ou editar parâmetros para os valores que seu aplicativo lógico usa em tempo de execução. Você deve definir esses parâmetros primeiro antes de poder referenciar esses parâmetros em outro lugar na definição do fluxo de trabalho.

Esta é a estrutura geral de uma definição de parâmetro:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Atributo | Obrigatório | Type | Descrição |
|-----------|----------|------|-------------|
| <*nome do parâmetro*> | Sim | String | O nome para o parâmetro que você deseja definir |
| <*tipo parâmetro*> | Sim | int, float, string, bool, array, object, securestring, secureobject <p><p>**Nota**: Para todas as senhas, `securestring` `secureobject` chaves e `GET` segredos, use os ou tipos porque a operação não retorna esses tipos. Para obter mais informações sobre como proteger [parâmetros, consulte recomendações de segurança para parâmetros de ação e entrada](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | O tipo do parâmetro |
| <*padrão-parâmetro-valor*> | Sim | O mesmo que `type` | O valor do parâmetro padrão a ser usado se nenhum valor for especificado quando o fluxo de trabalho instanciar. O `defaultValue` atributo é necessário para que o Logic App Designer possa mostrar corretamente o parâmetro, mas você pode especificar um valor vazio. |
| <*matriz-com-permitido-parâmetro-valores*> | Não | Array | Uma matriz com valores que o parâmetro pode aceitar |
| <*descrição do parâmetro*> | Não | Objeto JSON | Quaisquer outros detalhes do parâmetro, como uma descrição para o parâmetro |
||||

Em seguida, crie um [modelo do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para a definição do fluxo de trabalho, defina parâmetros de modelo que aceitem os valores desejadas na implantação, substitua os valores codificados por referências aos parâmetros de definição de modelo ou fluxo de trabalho conforme apropriado e armazene os valores a serem usados na implantação em um arquivo de [parâmetro](../azure-resource-manager/templates/parameter-files.md)separado. Dessa forma, você pode alterar esses valores mais facilmente através do arquivo de parâmetros sem ter que atualizar e reimplantar seu aplicativo lógico. Para informações sensíveis ou que devem ser protegidas, como nomes de usuário, senhas e segredos, você pode armazenar esses valores no Azure Key Vault e fazer com que seu arquivo de parâmetro recupere esses valores do cofre principal. Para obter mais informações e exemplos sobre a definição de parâmetros nos níveis de definição de modelo e fluxo de trabalho, consulte [Visão geral: Automatize a implantação de aplicativos lógicos com modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Resultados estáticos

No `staticResults` atributo, defina o `outputs` `status` mock de uma ação e que a ação retorne quando a configuração de resultado estático da ação estiver ligada. Na definição da ação, `runtimeConfiguration.staticResult.name` o atributo faz referência ao `staticResults`nome para a definição de resultado estático no interior . Saiba como testar [aplicativos lógicos com dados simulados configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Atributo | Obrigatório | Type | Descrição |
|-----------|----------|------|-------------|
| <*static-resultado-definição-nome*> | Sim | String | O nome para uma definição de resultado estático que uma definição de ação pode referenciar através de um `runtimeConfiguration.staticResult` objeto. Para obter mais informações, consulte [Configurações de runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Você pode usar qualquer nome único que quiser. Por padrão, este nome único é anexado com um número, que é incrementado conforme necessário. |
| <*atributos de saída-e-valores-retornados*> | Sim | Varia | Os requisitos para esses atributos variam de acordo com diferentes condições. Por exemplo, `status` quando `Succeeded`o `outputs` é , o atributo inclui atributos e valores retornados como saídas falsas pela ação. Se `status` for `Failed`, `outputs` o atributo inclui o atributo, `errors` que `message` é uma matriz com um ou mais objetos de erro que têm informações de erro. |
| <*valores de cabeçalho*> | Não | JSON | Quaisquer valores de cabeçalho devolvidos pela ação |
| <*status-código retornado*> | Sim | String | O código de status devolvido pela ação |
| <*status de ação*> | Sim | String | O status da ação, `Succeeded` por exemplo, ou`Failed` |
|||||

Por exemplo, nesta definição de `runtimeConfiguration.staticResult.name` ação `HTTP0` HTTP, `staticResults` o atributo faz referência ao atributo onde as saídas simuladas para a ação são definidas. O `runtimeConfiguration.staticResult.staticResultOptions` atributo especifica que a `Enabled` configuração de resultado estático está na ação HTTP.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

A ação HTTP retorna as `HTTP0` saídas na definição interna `staticResults`. Neste exemplo, para o código de `OK`status, a saída simulada é . Para valores de cabeçalho, a saída simulada é `"Content-Type": "application/JSON"`. Para o status da ação, `Succeeded`a saída simulada é .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Expressões

Com JSON, é possível ter valores literais existentes no tempo de design, por exemplo:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Também é possível ter valores que não existem até o tempo de execução. Para representar esses valores, é possível usar *expressões*, que são avaliadas em tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções](#functions), [operadores](#operators), constantes, valores explícitos ou variáveis. Na definição de fluxo de trabalho, é possível usar uma expressão em qualquer lugar em um valor de cadeia de caracteres JSON, prefixando a expressão com o sinal de arroba (\@). Ao avaliar uma expressão que representa um valor JSON, o corpo da expressão é extraído removendo o caractere \@ e sempre resultará em outro valor JSON.

Por exemplo, para a propriedade `customerName` definida anteriormente, você pode obter o valor da propriedade usando a função [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) em uma expressão de função e pode atribuir esse valor à propriedade `accountName`:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

A *interpolação de cadeia de caracteres* também permite usar várias expressões dentro de cadeias de caracteres que são encapsuladas pelo caractere \@ e por chaves ({}). Esta é a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado sempre é uma cadeia de caracteres, tornando essa funcionalidade semelhante à função `concat()`, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se você tiver uma cadeia de caracteres literal que inicia com o caractere \@, prefixe o caractere \@ com outro caractere \@ como um caractere de escape: \@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Result |
|------------|--------|
| "Sophia Owen" | Retornar estes caracteres: 'Sophia Owen' |
| "array[1]" | Retornar estes caracteres: 'array[1]' |
| "\@\@" | Retornar esses caracteres como uma cadeia de caracteres com um caractere: '\@' |
| " \@" | Retornar esses caracteres como uma cadeia de caracteres com dois caracteres: ' \@' |
|||

Para esses exemplos, suponha que você defina "myBirthMonth" como "January" e "myAge" igual ao número 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como as expressões a seguir são avaliadas:

| Expressão JSON | Result |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Retornar esta cadeia de caracteres: "January" |
| "\@{parameters('myBirthMonth')}" | Retornar esta cadeia de caracteres: "January" |
| "\@parameters('myAge')" | Retornar este número: 42 |
| "\@{parameters('myAge')}" | Retornar este número como uma cadeia de caracteres: "42" |
| "My age is \@{parameters('myAge')}" | Retornar esta cadeia de caracteres: My age is 42" |
| "\@concat('My age is ', string(parameters('myAge')))" | Retornar esta cadeia de caracteres: My age is 42" |
| "My age is \@\@{parameters('myAge')}" | Retornar essa cadeia de caracteres, que inclui a expressão: "My age is \@{parameters('myAge')}` |
|||

Quando está trabalhando visualmente no Designer de Aplicativos Lógicos, você pode criar expressões usando o Construtor de Expressões, por exemplo:

![Designer de Aplicativos Lógicos > Construtor de Expressões](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando você terminar, a expressão será exibida para a propriedade correspondente em sua definição de fluxo de trabalho, por exemplo, a propriedade `searchQuery` aqui:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>outputs

Na seção `outputs`, defina os dados que o fluxo de trabalho pode retornar quando terminar sua execução. Por exemplo, para rastrear um valor ou status específico em cada execução, especifique que a saída do fluxo de trabalho retorne esses dados.

> [!NOTE]
> Ao responder a solicitações de entrada da API REST de um serviço, não use `outputs`. Em vez disso, use o tipo de ação `Response`. Para obter mais informações, consulte [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

Esta é a estrutura geral de uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atributo | Obrigatório | Type | Descrição |
|-----------|----------|------|-------------|
| <*nome-chave*> | Sim | String | O nome da chave do valor retornado da saída |
| <*tipo de chave*> | Sim | int, float, string, securestring, bool, array, objeto JSON | O tipo do valor retornado da saída |
| <*valor-chave*> | Sim | O mesmo que <*tipo de chave*> | O valor retornado da saída |
|||||

Para obter a saída de um fluxo de trabalho executado, revise o histórico de execução do aplicativo lógico no portal Azure ou use a [API Workflow REST](https://docs.microsoft.com/rest/api/logic/workflows). Você também pode passar a saída para sistemas externos, por exemplo, o Power BI para que você possa criar painéis.

<a name="operators"></a>

## <a name="operators"></a>Operadores

No caso de [expressões](#expressions) e [funções](#functions), os operadores desempenham tarefas específicas, como referenciar uma propriedade ou um valor em uma matriz.

| Operador | Tarefa |
|----------|------|
| ' | Para usar um literal de cadeia de caracteres como entrada ou em expressões e funções, encapsule a cadeia de caracteres somente com aspas simples, por exemplo, `'<myString>'`. Não use aspas duplas (""), que entram em conflito com a formatação JSON ao redor de uma expressão inteira. Por exemplo:  <p>**Sim**: length('Hello') </br>**Não**: length("Hello") <p>Quando passa matrizes ou números, você não precisa de pontuação de encapsulamento. Por exemplo:  <p>**Sim**: length([1, 2, 3]) </br>**Não**: length("[1, 2, 3]") |
| [] | Para referenciar um valor em uma posição específica (índice) em uma matriz, use colchetes. Por exemplo, para obter o segundo item de uma matriz: <p>`myArray[1]` |
| . | Para referenciar uma propriedade em um objeto, use o operador de ponto. Por exemplo, para obter a propriedade `name` para um objeto JSON `customer`: <p>`"@parameters('customer').name"` |
| ? | Para referenciar propriedades nulas em um objeto sem erro de runtime, use o operador de ponto de interrogação. Por exemplo, você pode usar esta expressão para tratar as saídas nulas de um gatilho: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm seus valores a partir de ações de tempo de execução que podem ainda não existir quando a definição do fluxo de trabalho começa a ser executada. Para referenciar ou trabalhar com esses valores em expressões, você pode usar as [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) fornecidas pela linguagem de definição de fluxo de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Workflow Definition Language actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md) (Ações e gatilhos da Linguagem de Definição de Fluxo de Trabalho)
* Saiba mais sobre como criar e gerenciar Aplicativos Lógicos de forma programática com a [API REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
