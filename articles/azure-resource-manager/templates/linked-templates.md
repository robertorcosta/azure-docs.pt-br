---
title: Vincular modelos para implantação
description: Descreve como usar modelos vinculados em um modelo do Gerenciador de Recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especificar um arquivo de parâmetro e URLs criadas dinamicamente.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: c6a5dced0f8607e760422bebd114a08ca0a238e3
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207597"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Usando modelos vinculados e aninhados ao implantar os recursos do Azure

Para implantar soluções complexas, você pode dividir o modelo em muitos modelos relacionados e implantá-los juntos por meio de um modelo principal. Os modelos relacionados podem ser arquivos separados ou a sintaxe de modelo que é inserida no modelo principal. Este artigo usa o termo **vinculado modelo** para se referir a um arquivo de modelo separado que está vinculado a do modelo principal. Ele usa o termo **modelo aninhado** para se referir à sintaxe de modelo incorporado no modelo principal.

Para pequenas e médias soluções, um único modelo é mais fácil de entender e manter. Você pode ver todos os recursos e valores em um único arquivo. Para cenários avançados, os modelos vinculados permitem dividir a solução em componentes de destino. Você pode facilmente reutilizar esses modelos para outros cenários.

Para obter um tutorial, consulte [Tutorial: criar modelos vinculados do Azure Resource Manager](template-tutorial-create-linked-templates.md).

> [!NOTE]
> Para modelos vinculados ou aninhados, você só pode usar o modo de implantação [Incremental](deployment-modes.md).
>

## <a name="nested-template"></a>Modelo aninhado

Para aninhar um modelo, adicione um [recurso de implantações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na propriedade de **modelo** , especifique a sintaxe do modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

O exemplo a seguir implanta uma conta de armazenamento por meio de um modelo aninhado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="scope-for-expressions-in-nested-templates"></a>Escopo para expressões em modelos aninhados

Ao usar um modelo aninhado, você pode especificar se as expressões de modelo são avaliadas dentro do escopo do modelo pai ou do modelo aninhado. O escopo determina como os parâmetros, as variáveis e as funções, como o [resourcegroup](template-functions-resource.md#resourcegroup) e a [assinatura](template-functions-resource.md#subscription) , são resolvidos.

Você define o escopo por meio da propriedade `expressionEvaluationOptions`. Por padrão, a propriedade `expressionEvaluationOptions` é definida como `outer`, o que significa que ele usa o escopo do modelo pai. Defina o valor como `inner` para as expressões de escopo para o modelo aninhado.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

O modelo a seguir demonstra como as expressões de modelo são resolvidas de acordo com o escopo. Ele contém uma variável chamada `exampleVar` que é definida no modelo pai e no modelo aninhado. Ele retorna o valor da variável.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

O valor da variável é alterado com base no escopo. A tabela a seguir mostra os resultados para ambos os escopos.

| Escopo | Saída |
| ----- | ------ |
| interna | do modelo aninhado |
| externo (ou padrão) | do modelo pai |

O exemplo a seguir implanta um SQL Server e recupera um segredo do Key Vault a ser usado para a senha. O escopo é definido como `inner` porque cria dinamicamente a ID do cofre de chaves e o passa como um parâmetro para o modelo aninhado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> Quando o escopo é definido como `outer`, não é possível usar a função `reference` na seção de saídas de um modelo aninhado para um recurso que você implantou no modelo aninhado. Para retornar os valores de um recurso implantado em um modelo aninhado, use o escopo interno ou converta o modelo aninhado em um modelo vinculado.

## <a name="linked-template"></a>Modelo vinculado

Para vincular um modelo, adicione um [recurso de implantações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na propriedade **templateLink** , ESPECIFIQUE o URI do modelo a ser incluído. O exemplo a seguir contém links para um modelo que implanta uma nova conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Não é possível especificar um arquivo local ou um arquivo que esteja disponível apenas em sua rede local. Você só pode fornecer um valor de URI que inclua **http** ou **https**. O Gerenciador de recursos deve ser capaz de acessar o modelo. Uma opção é colocar o modelo vinculado em uma conta de armazenamento e usar o URI do item.

Você não precisa fornecer a propriedade `contentVersion` para o modelo ou parâmetros. Se você não fornecer um valor de versão do conteúdo, a versão atual do modelo é implantada. Se você fornecer um valor para a versão do conteúdo, ele deve corresponder à versão do modelo vinculado; caso contrário, a implantação falhará com um erro.

### <a name="parameters-for-linked-template"></a>Parâmetros para o modelo vinculado

Você pode fornecer os parâmetros para o modelo vinculado, seja em um arquivo externo ou embutido. Ao fornecer um arquivo de parâmetro externo, use a propriedade **parametersLink** :

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
    "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
    "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
    "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
    "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

Para passar valores de parâmetro embutidos, use a propriedade **Parameters** .

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2018-05-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
    }
   }
  }
]
```

Você não pode usar os dois parâmetros inline e um link para um arquivo de parâmetros. A implementação falha com um erro quando `parametersLink` e `parameters` são especificados.

## <a name="using-copy"></a>Usando a cópia

Para criar várias instâncias de um recurso com um modelo aninhado, adicione o elemento Copy no nível do recurso **Microsoft. Resources/Implantations** . Ou, se o escopo for interno, você poderá adicionar a cópia dentro do modelo aninhado.

O modelo de exemplo a seguir mostra como usar Copy com um modelo aninhado.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Usando variáveis para vincular modelos

Os exemplos anteriores mostraram valores codificados de URL para os vínculos de modelo. Essa abordagem pode funcionar para um modelo simples, mas não funciona bem quando ao trabalhar com um grande conjunto de modelos modulares. Em vez disso, você pode criar uma variável estática que armazena uma URL de base para o modelo principal e, em seguida, criar dinamicamente URLs para os modelos vinculados dessa URL de base. A vantagem dessa abordagem é mover ou bifurcar o modelo, pois você precisa alterar a variável estática no modelo principal. O modelo principal passa os URIs corretos em todo o modelo decomposto.

O exemplo a seguir mostra como usar uma URL base para criar duas URLs para modelos vinculados (**sharedTemplateUrl** e **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Você também pode usar [deployment()](template-functions-deployment.md#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Essa abordagem será útil se o local do modelo é alterado ou para evitar embutir URLs no arquivo de modelo. A propriedade templateLink só será retornada ao vincular a um modelo remoto com uma URL. Se você estiver usando um modelo local, essa propriedade não estará disponível.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Obter valores de modelos vinculados

Para obter um valor de saída de um modelo vinculado, recupere o valor da propriedade com uma sintaxe semelhante a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Ao obter uma propriedade de saída de um modelo vinculado, o nome da propriedade não pode incluir um traço.

Os exemplos a seguir demonstram como fazer referência a um modelo vinculado e recuperar um valor de saída. O modelo vinculado retorna uma mensagem simples.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "greetingMessage": {
      "value": "Hello World",
      "type" : "string"
    }
  }
}
```

O modelo principal implanta o modelo vinculado e obtém o valor retornado. Observe que ele faz referência aos recursos de implantação por nome, e ele usa o nome da propriedade retornada pelo modelo vinculado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
    }
  }
}
```

Assim como outros tipos de recurso, você pode definir dependências entre o modelo vinculado e outros recursos. Quando outros recursos exigirem um valor de saída do modelo vinculado, verifique se o modelo vinculado está implantado antes deles. Ou, quando o modelo vinculado depender de outros recursos, certifique-se que outros recursos foram implantados antes do modelo vinculado.

O exemplo a seguir mostra um modelo que implanta um endereço IP público e retorna a ID do recurso:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "eastus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 4
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "resourceID": {
      "type": "string",
      "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
    }
  }
}
```

Para usar o endereço IP público do modelo anterior ao implantar um balanceador de carga, vincule ao modelo e adicione uma dependência no recurso de implantação. O endereço IP público no balanceador de carga é definido como o valor de saída do modelo vinculado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "loadBalancers_name": {
      "defaultValue": "mylb",
      "type": "string"
    },
    "publicIPAddresses_name": {
      "defaultValue": "myip",
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/loadBalancers",
      "apiVersion": "2018-11-01",
      "name": "[parameters('loadBalancers_name')]",
      "location": "eastus",
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
              }
            }
          }
        ],
        "backendAddressPools": [],
        "loadBalancingRules": [],
        "probes": [],
        "inboundNatRules": [],
        "outboundNatRules": [],
        "inboundNatPools": []
      },
      "dependsOn": [
        "linkedTemplate"
      ]
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
        }
      }
    }
  ]
}
```

## <a name="deployment-history"></a>Histórico de implantações

O Gerenciador de Recursos trata cada modelo como uma implantação separada no histórico de implantações. Um modelo principal com três modelos vinculados ou aninhados aparece no histórico de implantação como:

![Histórico de implantações](./media/linked-templates/deployment-history.png)

Você pode usar essas entradas separadas no histórico para recuperar valores de saída após a implantação. O modelo a seguir cria um endereço IP público e gera o endereço IP:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Os seguintes links de modelo para o modelo anterior. Ele cria três endereços IP públicos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Após a implantação, você pode recuperar os valores de saída com o seguinte script do PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Ou, script da CLI do Azure em um shell Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az group deployment show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Proteger um modelo externo

Embora o modelo vinculado precise estar disponível externamente, ele não precisa estar disponível para o público. Você pode adicionar o modelo a uma conta de armazenamento privada que pode ser acessada somente pelo proprietário da conta de armazenamento. Em seguida, você cria um token SAS (assinatura de acesso compartilhado) para permitir o acesso durante a implantação. Você pode adicionar esse token SAS ao URI para o modelo vinculado. Embora o token seja transmitido como uma cadeia de caracteres segura, o URI do modelo vinculado, incluindo o token SAS, é registrado nas operações de implantação. Para limitar a exposição, defina uma expiração para o token.

O arquivo de parâmetro também pode ter o acesso limitado por meio de um token SAS.

No momento, não é possível vincular a um modelo em uma conta de armazenamento que está atrás de um [Firewall de armazenamento do Azure](../../storage/common/storage-network-security.md).

O exemplo a seguir mostra como passar um token SAS ao vincular a um modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "string" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
      "contentVersion": "1.0.0.0"
    }
    }
  }
  ],
  "outputs": {
  }
}
```

No PowerShell, você obtém um token para o contêiner e implanta os modelos com os comandos a seguir. Observe que o parâmetro **containerSasToken** está definido no modelo. Não é um parâmetro no comando **New-AzResourceGroupDeployment**.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Para a CLI do Azure em um shell Bash, você obtém um token para o contêiner e implanta os modelos com o seguinte código:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram os usos comuns dos modelos vinculados.

|Modelo principal  |Modelo vinculado |DESCRIÇÃO  |
|---------|---------| ---------|
|[Olá mundo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modelo vinculado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retorna a cadeia de caracteres do modelo vinculado. |
|[Azure Load Balancer com o endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modelo vinculado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Retorna o endereço IP público do modelo vinculado e define esse valor no balanceador de carga. |
|[Vários endereços IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modelo vinculado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Cria vários endereços IP públicos no modelo vinculado.  |

## <a name="next-steps"></a>Próximas etapas

* Para percorrer um tutorial, consulte [Tutorial: criar modelos vinculados do Azure Resource Manager](template-tutorial-create-linked-templates.md).
* Para saber mais sobre como definir a ordem de implantação para seus recursos, consulte [Definição de dependências nos modelos do Azure Resource Manager](define-resource-dependency.md).
* Para saber como definir um recurso, mas criando várias instâncias dele, consulte [Criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).
* Para ver as etapas para configurar um modelo em uma conta de armazenamento e gerar um token SAS, consulte [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](deploy-powershell.md) ou [Implantar recursos com modelos do Resource Manager e a CLI do Azure](deploy-cli.md).
