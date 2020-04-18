---
title: Visão geral - Automatize implantação para aplicativos de lógica do Azure
description: Conheça os modelos do Azure Resource Manager para automatizar a implantação de aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641822"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Visão geral: Automatize a implantação de aplicativos de lógica do Azure usando modelos do Azure Resource Manager

Quando você estiver pronto para automatizar a criação e a implantação do seu aplicativo lógico, você pode expandir a definição de fluxo de trabalho subjacente do seu aplicativo lógico em um [modelo do Azure Resource Manager](../azure-resource-manager/management/overview.md). Esse modelo define a infra-estrutura, os recursos, os parâmetros e outras informações para o provisionamento e a implantação do seu aplicativo lógico. Ao definir parâmetros para valores que variam na implantação, também conhecido como *parametrização,* você pode implantar repetida e consistentemente aplicativos lógicos com base em diferentes necessidades de implantação.

Por exemplo, se você implantar em ambientes para desenvolvimento, teste e produção, provavelmente usará diferentes strings de conexão para cada ambiente. Você pode declarar parâmetros de modelo que aceitam diferentes strings de conexão e, em seguida, armazenar essas strings em um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md)separados . Dessa forma, você pode alterar esses valores sem ter que atualizar e reimplantar o modelo. Para cenários em que você tenha valores de parâmetros sensíveis ou que devem ser protegidos, como senhas e segredos, você pode armazenar esses valores no [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) e ter seus parâmetros de arquivo recuperar esses valores. No entanto, nestes cenários, você reimplantaria para recuperar os valores atuais.

Essa visão geral descreve os atributos em um modelo do Gerenciador de recursos que inclui uma definição de fluxo de trabalho de aplicativo lógico. Tanto o modelo quanto a definição do fluxo de trabalho usam a sintaxe JSON, mas existem algumas diferenças porque a definição do fluxo de trabalho também segue o [esquema de linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md). Por exemplo, expressões de modelo e expressões de definição de fluxo de trabalho diferem em como eles [referenciam parâmetros](#parameter-references) e os valores que eles podem aceitar.

> [!TIP]
> Para obter um modelo de aplicativo lógico parametrizado válido que está praticamente pronto para implantação, use o Visual Studio (edição comunitária gratuita ou superior) e o Azure Logic Apps Tools for Visual Studio. Você pode então [criar seu aplicativo lógico no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou encontrar e baixar um aplicativo lógico existente do [Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Ou, você pode criar modelos de aplicativos lógicos usando [o Azure PowerShell com o módulo LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

O aplicativo lógico de exemplo neste tópico usa um [gatilho do Office 365 Outlook](/connectors/office365/) que é acionado quando um novo e-mail chega e uma ação de armazenamento [Azure Blob](/connectors/azureblob/) que cria uma bolha para o corpo de e-mail e carrega essa bolha para um contêiner de armazenamento do Azure. Os exemplos também mostram como parametrizar valores que variam na implantação.

Para obter mais informações sobre modelos do Gerenciador de recursos, consulte esses tópicos:

* [Estrutura e sintaxe do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Práticas recomendadas para modelos do Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Desenvolva modelos do Azure Resource Manager para consistência de nuvem](../azure-resource-manager/templates/templates-cloud-consistency.md)

Para modelos de aplicativos de lógica de exemplo, consulte estes exemplos:

* [Modelo completo](#full-example-template) que é usado para os exemplos deste tópico
* [Experimente o modelo de aplicativo de lógica quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) no GitHub

Para obter informações de recurso de modelo específicas para aplicativos lógicos, contas de integração e artefatos de conta de integração, consulte [os tipos de recursos microsoft.logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Estrutura de modelo

No nível superior, um modelo de Gerenciador de recursos segue essa estrutura, que está totalmente descrita na estrutura de modelo sintaxe e no tópico [de sintaxe do Azure Resource Manager:](../azure-resource-manager/templates/template-syntax.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Para um modelo de aplicativo lógico, você trabalha principalmente com esses objetos de modelo:

| Atributo | Descrição |
|-----------|-------------|
| `parameters` | Declara os [parâmetros de modelo](../azure-resource-manager/templates/template-syntax.md#parameters) para aceitar os valores a serem usados ao criar e personalizar recursos para implantação no Azure. Por exemplo, esses parâmetros aceitam os valores para o nome e localização do seu aplicativo lógico, conexões e outros recursos necessários para a implantação. Você pode armazenar esses valores de parâmetro em um [arquivo de parâmetros,](#template-parameter-files)que é descrito mais tarde neste tópico. Para obter detalhes gerais, consulte [Parâmetros - Estrutura de modelo e sintaxe do Gerenciador de Recursos](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Define os [recursos](../azure-resource-manager/templates/template-syntax.md#resources) para criar ou atualizar e implantar em um grupo de recursos do Azure, como seu aplicativo lógico, conexões, contas de armazenamento do Azure e assim por diante. Para obter detalhes gerais, consulte [Recursos - Estrutura de modelo e sintaxe do Gerenciador de Recursos](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Seu modelo de aplicativo lógico usa este formato de nome de arquivo:

**<*nome de aplicativo de lógica*>.json**

> [!IMPORTANT]
> A sintaxe do modelo é sensível a maiúsculas e minúsculas, por isso certifique-se de usar invólucros consistentes. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parâmetros de modelo

Um modelo de `parameters` aplicativo lógico tem vários objetos que existem em diferentes níveis e executam diferentes funções. Por exemplo, no nível superior, você pode declarar [parâmetros de modelo](../azure-resource-manager/templates/template-syntax.md#parameters) para os valores a serem aceitos e usados na implantação ao criar e implantar recursos no Azure, por exemplo:

* Seu aplicativo lógico
* Conexões que sua lógica usa para acessar outros serviços e sistemas através [de conectores gerenciados](../connectors/apis-list.md)
* Outros recursos que seu aplicativo lógico precisa para implantação

  Por exemplo, se o aplicativo de lógica usar uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para cenários `parameters` B2B (business-to-business), o objeto de nível superior do modelo declara o parâmetro que aceita o ID de recurso para essa conta de integração.

Aqui está a estrutura geral e a sintaxe para uma definição de parâmetro, que é totalmente descrita por [Parâmetros - Estrutura de modelo do Gerenciador de Recursos e sintaxe](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Este exemplo mostra apenas os parâmetros de modelo para os valores usados para criar e implantar esses recursos no Azure:

* Nome e localização para o seu aplicativo lógico
* ID para usar em uma conta de integração que está vinculada ao aplicativo lógico

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Exceto para parâmetros que lidam com valores sensíveis ou que devem ser protegidos, como nomes de usuário, senhas e segredos, todos esses parâmetros incluem `defaultValue` atributos, embora em alguns casos, os valores padrão sejam valores vazios. Os valores de implantação a serem usados para esses parâmetros de modelo são fornecidos pelo [arquivo de parâmetros](#template-parameter-files) de amostra descrito posteriormente neste tópico.

Para obter mais informações sobre como proteger parâmetros de modelo, consulte estes tópicos:

* [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Melhore a segurança para parâmetros de modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Passar valores de parâmetros garantidos com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Outros objetos de modelo geralmente referenciam parâmetros de modelo para que possam usar os valores que passam pelos parâmetros do modelo, por exemplo:

* O [objeto de recursos do seu modelo,](#template-resources)descrito posteriormente neste tópico, define cada recurso no Azure que você deseja criar e implantar, como a definição de recursos do seu aplicativo [lógico](#logic-app-resource-definition). Esses recursos geralmente usam valores de parâmetros de modelo, como o nome do seu aplicativo lógico e informações de localização e conexão.

* Em um nível mais profundo na definição de recursos do seu aplicativo lógico, o [objeto de parâmetros da definição do fluxo de trabalho](#workflow-definition-parameters) declara parâmetros para os valores a serem usados no tempo de execução do seu aplicativo lógico. Por exemplo, você pode declarar parâmetros de definição de fluxo de trabalho para o nome de usuário e senha que um gatilho HTTP usa para autenticação. Para especificar os valores para `parameters` parâmetros de definição de fluxo de trabalho, use o objeto que está *fora* da definição do fluxo de trabalho, mas ainda *dentro* da definição de recurso do seu aplicativo lógico. Neste objeto `parameters` externo, você pode referenciar parâmetros de modelo previamente declarados, que podem aceitar valores na implantação a partir de um arquivo de parâmetros.

Ao referenciar parâmetros, as expressões e funções do modelo usam sintaxe diferente e se comportam de forma diferente das expressões e funções de definição do fluxo de trabalho. Para obter mais informações sobre essas diferenças, consulte [Referências a parâmetros](#parameter-references) posteriores neste tópico.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Melhores práticas - parâmetros de modelo

Aqui estão algumas práticas recomendadas para definir parâmetros:

* Declare parâmetros apenas para valores que variam, com base nas suas necessidades de implantação. Não declare parâmetros para valores que permaneçam os mesmos em diferentes requisitos de implantação.

* Inclua `defaultValue` o atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores sensíveis ou que devem ser protegidos. Use sempre parâmetros protegidos para nomes de usuários, senhas e segredos. Para ocultar ou proteger valores de parâmetros sensíveis, siga as orientações nesses tópicos:

  * [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Melhore a segurança para parâmetros de modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Passar valores de parâmetros garantidos com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Para diferenciar nomes de parâmetros de parâmetro de modelo de nomes de parâmetros de definição de fluxo de trabalho, você pode usar nomes de parâmetros de modelo descritivos, por exemplo:`TemplateFabrikamPassword`

Para obter mais práticas recomendadas de modelo, consulte [As melhores práticas para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Arquivo de parâmetros de modelo

Para fornecer os valores para parâmetros de modelo, armazene esses valores em um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md). Dessa forma, você pode usar diferentes arquivos de parâmetros com base em suas necessidades de implantação. Aqui está o formato do nome do arquivo a ser usado:

* Nome do arquivo do modelo do aplicativo lógico: ** < *nome do aplicativo de lógica*>.json**
* Parâmetros nome do arquivo: ** < *logic-app-name*>.parameters.json**

Aqui está a estrutura dentro do arquivo parâmetros, que inclui uma referência de cofre chave para [passar um valor de parâmetro seguro com o Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Este arquivo de parâmetros de exemplo especifica os valores para os parâmetros de modelo declarados anteriormente neste tópico:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Recursos de modelo

Seu modelo `resources` tem um objeto, que é um array que contém definições para cada recurso para criar e implantar no Azure, como a definição de recursos do seu [aplicativo lógico,](#logic-app-resource-definition)quaisquer [definições de recursos](#connection-resource-definitions)de conexão e quaisquer outros recursos que seu aplicativo lógico precisa para implantação.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Os modelos podem incluir definições de recursos para vários aplicativos lógicos, portanto, certifique-se de que todos os recursos do aplicativo lógico especifiquem o mesmo grupo de recursos do Azure. Quando você implanta o modelo em um grupo de recursos do Azure usando o Visual Studio, você é solicitado para qual aplicativo lógico você deseja abrir. Além disso, o projeto do grupo de recursos do Azure pode conter mais de um modelo, por isso certifique-se de selecionar o arquivo de parâmetros correto quando solicitado.

Para obter informações gerais sobre os recursos do modelo e seus atributos, consulte estes tópicos:

* [Recursos - Estrutura de modelo sincção e estrutura de modelo sintaxe do Gestor de Recursos](../azure-resource-manager/templates/template-syntax.md#resources)
* [Práticas recomendadas para recursos de modelo](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definição de recursos do aplicativo lógico

A definição de recurso do `properties` seu aplicativo lógico começa com o objeto, que inclui essas informações:

* O estado do seu aplicativo lógico na implantação
* O ID para qualquer conta de integração usada pelo seu aplicativo lógico
* Definição do fluxo de trabalho do seu aplicativo lógico
* Um `parameters` objeto que define os valores a serem usados em tempo de execução
* Outras informações de recursos sobre seu aplicativo lógico, como nome, tipo, localização e assim por diante

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Aqui estão os atributos específicos para a definição de recursos do aplicativo lógico:

| Atributo | Obrigatório | Type | Descrição |
|-----------|----------|------|-------------|
| `state` | Sim | String | O estado do seu aplicativo `Enabled` lógico na implantação `Disabled` onde significa que seu aplicativo lógico está ao vivo e significa que seu aplicativo lógico está inativo. Por exemplo, se você não estiver pronto para o seu aplicativo lógico entrar em `Disabled` operação, mas quiser implantar uma versão de rascunho, você pode usar a opção. |
| `integrationAccount` | Não | Objeto | Se o seu aplicativo lógico usar uma conta de integração, que armazena artefatos para cenários B2B (business-to-business), este objeto inclui o atributo, `id` que especifica o ID para a conta de integração. |
| `definition` | Sim | Objeto | A definição de fluxo de trabalho subjacente do seu aplicativo lógico, que é o mesmo objeto que aparece na exibição de código e está totalmente descrito na referência Esquema para o tópico [Workflow Definition Language.](../logic-apps/logic-apps-workflow-definition-language.md) Nesta definição de fluxo `parameters` de trabalho, o objeto declara parâmetros para os valores a serem usados em tempo de execução do aplicativo lógico. Para obter mais informações, consulte [definição e parâmetros do fluxo de trabalho](#workflow-definition-parameters). <p><p>Para visualizar os atributos na definição do fluxo de trabalho do seu aplicativo lógico, mude de "exibição de design" para "exibição de código" no portal Azure ou Visual Studio, ou usando uma ferramenta como [o Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | Não | Objeto | O [parâmetro de definição do fluxo de trabalho é usado](#workflow-definition-parameters) em tempo de execução do aplicativo lógico. As definições de parâmetros para esses valores aparecem dentro [do objeto parâmetros da definição do fluxo de trabalho](#workflow-definition-parameters). Além disso, se o seu aplicativo lógico usa [conectores gerenciados](../connectors/apis-list.md) para `$connections` acessar outros serviços e sistemas, este objeto inclui um objeto que define os valores de conexão a serem usados em tempo de execução. |
| `accessControl` | Não | Objeto | Para especificar atributos de segurança para seu aplicativo lógico, como restringir o acesso IP a gatilhos de solicitação ou executar entradas e saídas de histórico. Para obter mais informações, consulte [Acesso seguro a aplicativos lógicos](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Para obter informações de recurso de modelo específicas para aplicativos lógicos, contas de integração e artefatos de conta de integração, consulte [os tipos de recursos microsoft.logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definição e parâmetros do fluxo de trabalho

A definição de fluxo de trabalho `definition` do seu aplicativo `properties` lógico aparece no objeto, que aparece no objeto dentro da definição de recurso do seu aplicativo lógico. Este `definition` objeto é o mesmo objeto que aparece na exibição de código e está totalmente descrito na referência Esquema a para o tópico [Linguagem de Definição de Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md) Sua definição de fluxo `parameters` de trabalho inclui um objeto de declaração interna onde você pode definir novos ou editar parâmetros existentes para os valores que são usados pela definição do fluxo de trabalho em tempo de execução. Em seguida, você pode referenciar esses parâmetros dentro do gatilho ou ações em seu fluxo de trabalho. Por padrão, `parameters` este objeto está vazio, a menos que seu aplicativo lógico crie conexões com outros serviços e sistemas através [de conectores gerenciados.](../connectors/apis-list.md)

Para definir os valores para parâmetros de definição de fluxo de trabalho, use o `parameters` objeto que está *fora* da definição do fluxo de trabalho, mas ainda *dentro* da definição de recurso do seu aplicativo lógico. Neste objeto `parameters` externo, você pode então referenciar seus parâmetros de modelo previamente declarados, que podem aceitar valores na implantação a partir de um arquivo de parâmetros.

> [!TIP]
>
> Como uma prática recomendada, não faça referência diretamente aos parâmetros do modelo, que são avaliados na implantação, de dentro da definição do fluxo de trabalho. Em vez disso, declare um parâmetro de definição `parameters` de fluxo de trabalho, que você pode então definir no objeto que está *fora* da definição do seu fluxo de trabalho, mas ainda *dentro* da definição de recurso do seu aplicativo lógico. Para obter mais informações, consulte [Referências a parâmetros](#parameter-references).

Esta sintaxe mostra onde você pode declarar parâmetros nos níveis de definição de modelo e fluxo de trabalho, juntamente com onde você pode definir esses valores de parâmetro, fazendo referência aos parâmetros de definição de modelo e fluxo de trabalho:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parâmetros seguros de definição de fluxo de trabalho

Para um parâmetro de definição de fluxo de trabalho que lida com informações confidenciais, senhas, `securestring` chaves `secureobject` de acesso ou segredos em tempo de execução, declare ou edite o parâmetro para usar o tipo ou parâmetro. Você pode referenciar este parâmetro em toda e dentro da definição do seu fluxo de trabalho. No nível superior do modelo, declare um parâmetro que tenha o mesmo tipo para lidar com essas informações na implantação.

Para definir o valor do parâmetro de `parameters` definição do fluxo de trabalho, use o objeto que está *fora* da definição do fluxo de trabalho, mas ainda *dentro da* definição de recurso do aplicativo lógico para referenciar o parâmetro de modelo. Finalmente, para passar o valor para o parâmetro do modelo na implantação, armazene esse valor no [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) e faça referência ao cofre de chaves no [arquivo de parâmetros](#template-parameter-files) usado pelo seu modelo na implantação.

Este modelo de exemplo mostra como você pode concluir essas tarefas definindo parâmetros protegidos quando necessário para que você possa armazenar seus valores no Azure Key Vault:

* Declare parâmetros protegidos para os valores usados para autenticar o acesso.
* Use esses valores nos níveis de definição de modelo e fluxo de trabalho.
* Forneça esses valores usando um arquivo de parâmetros.

**Modelo**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Arquivo parâmetros**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Melhores práticas - parâmetros de definição de fluxo de trabalho

Para garantir que o Logic App Designer possa mostrar corretamente os parâmetros de definição do fluxo de trabalho, siga estas práticas recomendadas:

* Inclua `defaultValue` o atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores sensíveis ou que devem ser protegidos.

* Use sempre parâmetros protegidos para nomes de usuários, senhas e segredos. Para ocultar ou proteger valores de parâmetros sensíveis, siga as orientações nesses tópicos:

  * [Recomendações de segurança para parâmetros de ação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Recomendações de segurança para parâmetros nas definições de fluxo de trabalho](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Passe valores de parâmetros seguros com o Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Para obter mais informações sobre parâmetros de definição de fluxo de trabalho, consulte [Parâmetros - Linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definições de recursos de conexão

Quando seu aplicativo lógico cria e usa conexões com outros serviços e `resources` sistema usando [conectores gerenciados,](../connectors/apis-list.md)o objeto do modelo contém as definições de recursos para essas conexões.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

As definições de recursos de conexão fazem referência aos parâmetros de nível superior do modelo para seus valores, o que significa que você pode fornecer esses valores na implantação usando um arquivo de parâmetros. Certifique-se de que as conexões usem o mesmo grupo de recursos e localização do Azure que seu aplicativo lógico.

Aqui está um exemplo de definição de recurso para uma conexão do Office 365 Outlook e os parâmetros de modelo correspondentes:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

A definição de recurso do seu aplicativo lógico também funciona com definições de recursos de conexão nessas maneiras:

* Dentro da definição do `parameters` fluxo de `$connections` trabalho, o objeto declara um parâmetro para os valores de conexão a serem usados em tempo de execução do aplicativo lógico. Além disso, o gatilho ou ação que cria uma `$connections` conexão usa os valores correspondentes que passam por esse parâmetro.

* *Fora* da definição do *inside* fluxo de trabalho, mas `parameters` ainda dentro da definição de recurso `$connections` do seu aplicativo lógico, outro objeto define os valores a serem usados em tempo de execução para o parâmetro, fazendo referência aos parâmetros de modelo correspondentes. Esses valores usam expressões de modelo para referenciar recursos que armazenam com segurança os metadados para as conexões em seu aplicativo lógico.

  Por exemplo, os metadados podem incluir seqüências de conexão e tokens de acesso, que você pode armazenar no [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Para passar esses valores para os parâmetros do modelo, você faz referência ao cofre de chaves no [arquivo parâmetros](#template-parameter-files) usado pelo seu modelo na implantação. Para obter mais informações sobre diferenças nos parâmetros de referência, consulte [Referências a parâmetros](#parameter-references) posteriores neste tópico.

  Quando você abre a definição de fluxo de trabalho do seu aplicativo lógico `$connections` na exibição de código através do portal Azure ou do Visual Studio, o objeto aparece fora da definição do seu fluxo de trabalho, mas no mesmo nível. Este pedido na exibição de código torna esses parâmetros mais fáceis de fazer referência quando você atualiza manualmente a definição do fluxo de trabalho:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* A definição de recurso do `dependsOn` seu aplicativo lógico tem um objeto que especifica as dependências das conexões usadas pelo seu aplicativo lógico.

Cada conexão que você cria tem um nome único no Azure. Quando você cria várias conexões para o mesmo serviço ou sistema, cada nome de conexão é `office365`anexado com um número, que incrementa a cada nova conexão criada, por `office365-1`exemplo, e assim por diante.

Este exemplo mostra as interações entre a definição de recursos do seu aplicativo lógico e uma definição de recurso de conexão para o Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parâmetros de conexão seguros

Para um parâmetro de conexão que lida com informações confidenciais, senhas, chaves `parameterValues` de acesso ou segredos, a definição de recurso da conexão inclui um objeto que especifica esses valores no formato de par de valorde nome. Para ocultar essas informações, você pode declarar ou editar `securestring` `secureobject` os parâmetros de modelo para esses valores usando os tipos ou parâmetros. Em seguida, você pode armazenar essas informações no [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Para passar esses valores para os parâmetros do modelo, você faz referência ao cofre de chaves no [arquivo parâmetros](#template-parameter-files) usado pelo seu modelo na implantação.

Aqui está um exemplo que fornece o nome da conta e a chave de acesso para uma conexão Azure Blob Storage:

**Arquivo parâmetros**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Modelo**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Conexões autenticadas

Após a implantação, seu aplicativo lógico funciona de ponta a ponta com parâmetros válidos. No entanto, você ainda deve autorizar quaisquer conexões OAuth para gerar tokens de acesso válidos para [autenticar suas credenciais](../active-directory/develop/authentication-scenarios.md). Para obter mais informações, consulte [Autorizar conexões OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Algumas conexões suportam o uso de um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory (Azure AD) para autorizar conexões para um aplicativo lógico que está registrado [no Azure AD](../active-directory/develop/quickstart-register-app.md). Por exemplo, esta definição de recurso de conexão do Azure Data Lake mostra como referenciar os parâmetros de modelo que lidam com as informações do diretor do serviço e como o modelo declara esses parâmetros:

**Definição de recurso de conexão**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Atributo | Descrição |
|-----------|-------------|
| `token:clientId` | O aplicativo ou iD do cliente associado ao seu principal de serviço |
| `token:clientSecret` | O valor-chave associado ao seu principal de serviço |
| `token:TenantId` | O ID do diretório para o seu inquilino Azure AD |
| `token:grantType` | O tipo de subvenção `client_credentials`solicitado, que deve ser . Para obter mais informações, consulte [a plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definições de parâmetrode modelo**

O objeto de nível `parameters` superior do modelo declara esses parâmetros para a conexão por exemplo:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Para obter mais informações sobre como trabalhar com diretores de serviços, consulte estes tópicos:

* [Criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)
* [Crie um principal de serviço do Azure usando o Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Crie um diretor de serviço com um certificado usando o Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Referências a parâmetros

Para referenciar parâmetros de modelo, você pode usar expressões de modelo com [funções de modelo,](../azure-resource-manager/templates/template-functions.md)que são avaliadas na implantação. As expressões de modelo usam suportes quadrados **([]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Para referenciar parâmetros de definição de fluxo de trabalho, você usa [expressões e funções de definição de fluxo de trabalho,](../logic-apps/workflow-definition-language-functions-reference.md)que são avaliadas em tempo de execução. Você pode notar que algumas funções de modelo e funções de definição de fluxo de trabalho têm o mesmo nome. As expressões de definição do fluxo**@** de trabalho começam com o símbolo "at" ():

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Você pode passar valores de parâmetro de modelo para a definição do seu fluxo de trabalho para o seu aplicativo lógico usar em tempo de execução. No entanto, evite usar parâmetros de modelo, expressões e sintaxe na definição do fluxo de trabalho porque o Logic App Designer não suporta elementos de modelo. Além disso, a sintaxe e expressões do modelo podem complicar seu código devido a diferenças na avaliação das expressões.

Em vez disso, siga estas etapas gerais para declarar e referenciar os parâmetros de definição do fluxo de trabalho a serem usados em tempo de execução, declarar e referenciar os parâmetros de modelo a serem usados na implantação e especificar os valores a serem repassados na implantação usando um arquivo de parâmetros. Para obter detalhes completos, consulte a seção [Definição e parâmetros do Fluxo de Trabalho](#workflow-definition-parameters) anteriormente neste tópico.

1. Crie seu modelo e declare os parâmetros de modelo para os valores a serem aceitos e usados na implantação.

1. Na definição do fluxo de trabalho, declare os parâmetros para que os valores aceitem e utilizem em tempo de execução. Em seguida, você pode referenciar esses valores em toda parte e dentro da definição do seu fluxo de trabalho.

1. No `parameters` objeto que está *fora* da definição do fluxo de trabalho, mas ainda *dentro* da definição de recurso do seu aplicativo lógico, defina os valores para os parâmetros de definição do fluxo de trabalho fazendo referência aos parâmetros de modelo correspondentes. Dessa forma, você pode passar valores de parâmetro de modelo em seus parâmetros de definição de fluxo de trabalho.

1. No arquivo parâmetros, especifique os valores para o modelo a ser usado na implantação.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Modelo de exemplo completo

Aqui está o modelo de amostra parametrizado que é usado pelos exemplos deste tópico:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar modelos de aplicativo lógico](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
