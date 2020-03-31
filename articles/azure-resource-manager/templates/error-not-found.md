---
title: Recurso não encontrado erros
description: Descreve como resolver erros quando um recurso não pode ser encontrado ao ser implantado com um modelo do Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773255"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Solucione erros que ocorrem quando recursos do Azure não são encontrados

Este artigo descreve os erros com que você pode ver quando um recurso não é encontrado durante a implantação.

## <a name="symptom"></a>Sintoma

Quando o modelo inclui o nome de um recurso que não pode ser resolvido, você recebe um erro semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se usar as funções [reference](template-functions-resource.md#reference) ou [listKeys](template-functions-resource.md#listkeys) com um recurso que não pode ser resolvido, você receberá o seguinte erro:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

O Resource Manager precisa recuperar as propriedades de um recurso, mas não consegue identificar o recurso em sua assinatura.

## <a name="solution-1---set-dependencies"></a>Solução 1: Definir dependências

Caso você esteja tentando implantar o recurso ausente no modelo, verifique se você precisa adicionar uma dependência. O Gerenciador de Recursos otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso precisar ser implantado após outro recurso, você precisará usar o elemento **dependsOn** em seu modelo. Por exemplo, ao implantar um aplicativo Web, o plano do Serviço de Aplicativo deve existir. Se você não tiver especificado que o aplicativo Web depende do plano do Serviço de Aplicativo, o Gerenciador de Recursos criará os dois recursos ao mesmo tempo. Você recebe um erro informando que o recurso do plano do Serviço de Aplicativo não pode ser encontrado porque ele ainda não existe ao tentar definir uma propriedade no aplicativo Web. Você evita esse erro configurando a dependência no aplicativo Web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Mas, se você quiser evitar definir dependências que não são necessárias. Quando você tiver dependências desnecessárias, você prolongar a duração da implantação, impedindo que os recursos que não são dependentes entre si seja implantado em paralelo. Além disso, você pode criar dependências circulares que bloqueiam a implantação. A função [reference](template-functions-resource.md#reference) e as funções [list*](template-functions-resource.md#list) criam uma dependência implícita no recurso referenciado, quando esse recurso é implantado no mesmo modelo e é referenciado por seu nome (não pela ID de recurso). Portanto, você pode ter dependências mais que as dependências especificada no **dependsOn** propriedade. A função [resourceId](template-functions-resource.md#resourceid) não cria uma dependência implícita ou valida que o recurso existe. A função [reference](template-functions-resource.md#reference) e as funções [list*](template-functions-resource.md#list) não criam uma dependência implícita quando o recurso for referenciado por sua ID de recurso. Para criar uma dependência implícita, passe o nome do recurso que é implantado no mesmo modelo.

Quando você vir os problemas de dependência, você precisa obter informações sobre a ordem de implantação de recursos. Para exibir a ordem das operações de implantação:

1. Selecione o histórico de implantação do grupo de recursos.

   ![selecionar o histórico de implantação](./media/error-not-found/select-deployment.png)

2. Selecione uma implantação no histórico e selecione **Eventos**.

   ![selecionar os eventos de implantação](./media/error-not-found/select-deployment-events.png)

3. Examine a sequência de eventos de cada recurso. Preste atenção ao status de cada operação. Por exemplo, a imagem a seguir mostra três contas de armazenamento implantadas em paralelo. Observe que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/error-not-found/deployment-events-parallel.png)

   A imagem a seguir mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento e a terceira conta de armazenamento depende da segunda conta de armazenamento. A primeira conta de armazenamento é iniciada, aceita e concluída antes que a próxima seja iniciada.

   ![implantação sequencial](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solução 2: Obter recursos de outro grupo de recursos

Quando o recurso existir em um grupo de recursos diferente daquele que está sendo implantado, use a [função resourceId](template-functions-resource.md#resourceid) para obter o nome totalmente qualificado desse recurso.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solução 3: Verificar função de referência

Procure por uma expressão que inclui a função [reference](template-functions-resource.md#reference). Os valores que você fornecerá serão diferentes se o recurso estiver no mesmo modelo, grupo de recursos e assinatura. Verifique se você está fornecendo os valores de parâmetro necessários para seu cenário. Se o recurso estiver em um grupo de recursos diferente, forneça a ID do recurso completa. Por exemplo, para fazer referência a uma conta de armazenamento em outro grupo de recursos, use:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Solução 4 - obter identidade gerenciada a partir de recursos

Se você estiver implantando um recurso que cria implicitamente uma [identidade gerenciada,](../../active-directory/managed-identities-azure-resources/overview.md)você deve esperar até que esse recurso seja implantado antes de recuperar valores na identidade gerenciada. Se você passar o nome de identidade gerenciado para a função [de referência,](template-functions-resource.md#reference) o Gerenciador de recursos tentará resolver a referência antes que o recurso e a identidade sejam implantados. Em vez disso, passe o nome do recurso ao que a identidade é aplicada. Essa abordagem garante que o recurso e a identidade gerenciada sejam implantados antes que o Gerenciador de recursos resolva a função de referência.

Na função de `Full` referência, use para obter todas as propriedades, incluindo a identidade gerenciada.

Por exemplo, para obter o ID do inquilino para uma identidade gerenciada que é aplicada a um conjunto de escala de máquina virtual, use:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```