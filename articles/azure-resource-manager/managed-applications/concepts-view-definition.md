---
title: Visão geral da definição de exibição
description: Descreve o conceito de criar definição de exibição para aplicativos gerenciados do Azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650689"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Exibir artefato de definição em aplicativos gerenciados do Azure

A definição de exibição é um artefato opcional em Aplicativos Gerenciados do Azure. Ele permite personalizar a página de visão geral e adicionar mais visualizações, como métricas e recursos personalizados.

Este artigo fornece uma visão geral do artefato de definição de exibição e suas capacidades.

## <a name="view-definition-artifact"></a>Exibir artefato de definição

O artefato de definição de exibição deve ser nomeado **viewDefinition.json** e colocado no mesmo nível **que createUiDefinition.json** e **mainTemplate.json** no pacote .zip que cria uma definição de aplicativo gerenciada. Para saber como criar o pacote .zip e publicar uma definição de aplicativo gerenciada, consulte [Publicar uma definição de aplicativo gerenciado do Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Ver esquema de definição

O arquivo **viewDefinition.json** tem `views` apenas uma propriedade de nível superior, que é uma matriz de visualizações. Cada exibição é mostrada na interface do usuário do aplicativo gerenciado como um item de menu separado na tabela de conteúdo. Cada visualização `kind` tem uma propriedade que define o tipo de exibição. Deve ser definido como um dos seguintes valores: [Visão geral,](#overview) [Métricas, Recursos Personalizados,](#metrics) [Associações](#associations). [CustomResources](#custom-resources) Para obter mais informações, consulte o [esquema JSON atual para viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Amostra json para definição de exibição:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Visão geral

`"kind": "Overview"`

Quando você fornece essa exibição em **viewDefinition.json,** ela substitui a página de visão geral padrão no aplicativo gerenciado.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|cabeçalho|Não|O cabeçalho da página de visão geral.|
|descrição|Não|A descrição do seu aplicativo gerenciado.|
|comandos|Não|O conjunto de botões adicionais da barra de ferramentas da página de visão geral, veja [comandos](#commands).|

![Visão geral](./media/view-definition/overview.png)

## <a name="metrics"></a>Métricas

`"kind": "Metrics"`

A visualização de métricas permite coletar e agregar dados dos recursos gerenciados do aplicativo no [Azure Monitor Metrics](../../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Não|O título exibido da vista.|
|version|Não|A versão da plataforma usada para renderizar a visualização.|
|gráficos|Sim|O conjunto de gráficos da página métricas.|

### <a name="chart"></a>Gráfico

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido do gráfico.|
|Charttype|Não|A visualização para usar neste gráfico. Por padrão, ele usa um gráfico de linhas. Tipos de gráficos suportados: `Bar, Line, Area, Scatter`.|
|Métricas|Sim|A matriz de métricas para plotar neste gráfico. Para saber mais sobre métricas suportadas no portal Azure, consulte [métricas suportadas com o Azure Monitor](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Métrica

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|name|Sim|O nome da métrica.|
|Aggregationtype|Sim|O tipo de agregação a ser usado para esta métrica. Tipos de agregação suportados:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|Não|Informações adicionais a serem usados ao determinar o provedor de métricas correta.|
|resourceTagFilter|Não|O array de tags de `or` recursos (será separado com a palavra) para o qual as métricas seriam exibidas. Aplica-se em cima do filtro do tipo de recurso.|
|resourceType|Sim|O tipo de recurso para o qual as métricas seriam exibidas.|

![Métricas](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Recursos personalizados

`"kind": "CustomResources"`

Você pode definir vários pontos de vista deste tipo. Cada exibição representa um tipo de recurso personalizado **exclusivo** do provedor personalizado que você definiu no **mainTemplate.json**. Para obter uma introdução aos provedores personalizados, confira [Visão geral da Visualização de Provedores Personalizados do Azure](../custom-providers/overview.md).

Nesta exibição, você pode executar operações GET, PUT, DELETE e POST para o seu tipo de recurso personalizado. As operações POST podem ser ações personalizadas globais ou ações personalizadas em um contexto do seu tipo de recurso personalizado.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido da vista. O título deve ser **único** para cada visualização customResources em seu **viewDefinition.json**.|
|version|Não|A versão da plataforma usada para renderizar a visualização.|
|resourceType|Sim|O tipo de recurso personalizado. Deve ser um tipo de recurso personalizado **exclusivo** do seu provedor personalizado.|
|ícone|Não|O ícone da vista. A lista de ícones de exemplo é definida no [Esquema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|criarUIDefinition|Não|Criar esquema de definição de interface do ui para criar comando de recursos personalizados. Para uma introdução à criação de definições de interface do iur, consulte [Getting started with CreateUiDefinition](create-uidefinition-overview.md)|
|comandos|Não|O conjunto de botões adicionais da barra de ferramentas da exibição CustomResources, consulte [comandos](#commands).|
|colunas|Não|A matriz de colunas do recurso personalizado. Se não `name` for definida, a coluna será mostrada por padrão. A coluna `"key"` deve `"displayName"`ter e. Para a tecla, forneça a chave da propriedade para exibir em uma exibição. Se aninhada, use o dot como `"key": "name"` delimitador, por exemplo, ou `"key": "properties.property1"`. Para nome de exibição, forneça o nome de exibição da propriedade para exibição em uma exibição. Você também pode `"optional"` fornecer uma propriedade. Quando definido como verdadeiro, a coluna é oculta da exibição por padrão.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandos

Comandos é um conjunto de botões adicionais de barra de ferramentas que são exibidos na página. Cada comando representa uma ação POST do seu Provedor Personalizado Azure definido em **mainTemplate.json**. Para obter uma introdução a provedores personalizados, consulte [a visão geral dos provedores personalizados do Azure.](../custom-providers/overview.md)

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O nome exibido do botão de comando.|
|caminho|Sim|O nome de ação do provedor personalizado. A ação deve ser definida em **mainTemplate.json**.|
|ícone|Não|O ícone do botão de comando. A lista de ícones de exemplo é definida no [Esquema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|criarUIDefinition|Não|Criar esquema de definição de interface do ui para comando. Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Associações

`"kind": "Associations"`

Você pode definir vários pontos de vista deste tipo. Essa exibição permite vincular os recursos existentes ao aplicativo gerenciado através do provedor personalizado definido no **mainTemplate.json**. Para obter uma introdução aos provedores personalizados, confira [Visão geral da Visualização de Provedores Personalizados do Azure](../custom-providers/overview.md).

Nesta visão, você pode estender os recursos `targetResourceType`existentes do Azure com base no . Quando um recurso é selecionado, ele criará uma solicitação de onboarding ao provedor personalizado **público,** que pode aplicar um efeito colateral ao recurso. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Propriedade|Obrigatório|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido da vista. O título deve ser **único** para cada exibição de Associações em seu **viewDefinition.json**.|
|version|Não|A versão da plataforma usada para renderizar a visualização.|
|targetResourceType|Sim|O tipo de recurso de destino. Este é o tipo de recurso que será exibido para o onboarding de recursos.|
|criarUIDefinition|Não|Criar esquema de definição de interface do ui para criar comando de recursos de associação. Para uma introdução à criação de definições de interface do iur, consulte [Getting started with CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre os Aplicativos Gerenciados do Azure, tente perguntar no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida. Portanto, verifique primeiro antes de postar. Adicione a marca `azure-managedapps` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](overview.md).
- Para obter uma introdução a provedores personalizados, consulte [a visão geral dos provedores personalizados do Azure.](../custom-providers/overview.md)
- Para criar um aplicativo gerenciado pelo Azure com provedores personalizados do Azure, consulte [Tutorial: Crie aplicativo gerenciado com ações e tipos de recursos personalizados do provedor](tutorial-create-managed-app-with-custom-provider.md)
