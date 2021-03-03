---
title: Converter modelos de Azure Resource Manager entre JSON e bicep
description: Compara modelos de Azure Resource Manager desenvolvidos com JSON e bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744071"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparando JSON e bicep para modelos

Este artigo compara a sintaxe bicep com a sintaxe JSON para modelos de Azure Resource Manager (modelos ARM). Na maioria dos casos, o bicep fornece sintaxe que é menos detalhada do que o equivalente em JSON.

## <a name="syntax-equivalents"></a>Equivalentes de sintaxe

Se você estiver familiarizado com o uso do JSON para desenvolver modelos de ARM, use a tabela a seguir para saber mais sobre a sintaxe equivalente para bicep.

| Cenário | Modelo de ARM | Bicep |
| -------- | ------------ | ----- |
| Criar uma expressão | `"[func()]"` | `func()` |
| Obter valor do parâmetro | `[parameters('exampleParameter'))]` | `exampleParameter` |
| Obter valor da variável | `[variables('exampleVar'))]` | `exampleVar` |
| Concatenar cadeias de caracteres | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| Definir propriedade de recurso | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| Retornar o AND lógico | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| Obter a ID de recurso do recurso no modelo | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| Obter Propriedade do recurso no modelo | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| Definir condicionalmente um valor | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| Separar uma solução em vários arquivos | Usar modelos vinculados | Usar módulos |
| Definir o escopo de destino da implantação | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| Definir dependência | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | Conte com a detecção automática de dependências ou defina manualmente a dependência com `dependsOn: [ stg ]` |

Para declarar o tipo e a versão de um recurso, use o seguinte em bicep:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

Em vez da sintaxe equivalente em JSON:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>Recomendações

* Quando possível, evite usar as funções [Reference](template-functions-resource.md#reference) e [ResourceId](template-functions-resource.md#resourceid) em seu arquivo bicep. Ao fazer referência a um recurso na mesma implantação do bicep, use o identificador de recurso. Por exemplo, se você implantou um recurso em seu arquivo bicep com `stg` como o identificador de recurso, use sintaxe como `stg.id` ou `stg.properties.primaryEndpoints.blob` para obter valores de propriedade. Usando o identificador de recurso, você cria uma dependência implícita entre os recursos. Você não precisa definir explicitamente a dependência com a propriedade depending.
* Use maiúsculas e minúsculas para identificadores. Se não tiver certeza de qual tipo de capitalização usar, experimente o camel case. Por exemplo, `param myCamelCasedParameter string`.
* Adicione uma descrição a um parâmetro somente quando a descrição fornecer informações essenciais aos usuários. Você pode usar `//` comentários para algumas informações.

## <a name="decompile-json-to-bicep"></a>Descompilar JSON para bicep

A CLI do bicep fornece um comando para descompilar qualquer modelo ARM existente em um arquivo bicep. Para descompilar um arquivo JSON, use: `bicep decompile "path/to/file.json"`

Esse comando fornece um ponto de partida para a criação de bicep, mas o comando não funciona para todos os modelos. O comando pode falhar ou você pode ter que corrigir problemas após a descompilação. Atualmente, o comando tem as seguintes limitações:

* Os modelos que usam loops de cópia não podem ser descompilados.
* Modelos aninhados podem ser descompilados somente se usarem o escopo de avaliação da expressão ' interna '.

Você pode exportar o modelo para um grupo de recursos e, em seguida, passá-lo diretamente para o comando bicep decompile. O exemplo a seguir mostra como descompilar um modelo exportado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporte o modelo](export-template-portal.md) por meio do Portal. Use `bicep decompile <filename>` no arquivo baixado.

---

## <a name="build-json-from-bicep"></a>Compilar JSON de bicep

A CLI do bicep também fornece um comando para converter bicep em JSON. Para criar um arquivo JSON, use: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Exibição lado a lado

O [bicep playground](https://aka.ms/bicepdemo) permite exibir arquivos JSON e bicep equivalentes lado a lado. Você pode selecionar um modelo de exemplo para ver as duas versões. Ou então, selecione `Decompile` para carregar seu próprio modelo JSON e exibir o arquivo bicep equivalente.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre o projeto bicep, consulte [Project bicep](https://github.com/Azure/bicep).
