---
title: Início Rápido – Configurar o Provisionamento de Dispositivos no Hub IoT do Azure usando um modelo do Azure Resource Manager
description: Início rápido do Azure – Configurar o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure usando um modelo
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e1ca3d7270fb0858bb2512e5b9e285eb8d4555c6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91297140"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Início Rápido: Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com um modelo do Azure Resource Manager

É possível usar o [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para configurar programaticamente os recursos de nuvem do Azure necessários para o provisionamento dos dispositivos. Essas etapas mostram como criar um Hub IoT, um novo Serviço de Provisionamento de Dispositivos no Hub IoT e vincular os dois serviços usando um modelo do Azure Resource Manager. Este início rápido usa [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) executar o programático as etapas necessárias para criar um grupo de recursos e implantar o modelo, mas você pode facilmente usar o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby ou outras linguagens de programação para executar essas etapas e implantar seu modelo. 


## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Este Início Rápido requer que você execute a CLI do Azure localmente. Você deve ter a CLI do Azure versão 2.0 ou posterior instalada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar a CLI, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Entrar no Azure e criar um grupo de recursos

Entre na sua conta do Azure e selecione sua assinatura.

1. No prompt de comando, execute o [comando de logon][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga as instruções de autenticação usando o código e entre em sua conta do Azure por meio de um navegador da Web.

2. Se você tiver várias assinaturas do Azure, entrar o Azure lhe dará acesso a todas as contas do Azure associadas às suas credenciais. Use o seguinte comando [para listar as contas do Azure][lnk-az-account-command] disponíveis para você usar:
    
    ```azurecli
    az account list 
    ```

    Use o comando a seguir para selecionar a assinatura que você deseja usar para executar os comandos e criar seu Hub IoT. Você pode usar a ID ou nome da assinatura da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Ao criar recursos de nuvem do Azure como Hubs IoT e serviços de provisionamento, você os cria em um grupo de recursos. Use um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > O exemplo anterior cria o grupo de recursos localizado no Oeste dos EUA. Você pode exibir uma lista dos locais disponíveis executando o comando `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Criar um modelo do Resource Manager

Use um modelo JSON para criar um serviço de provisionamento e um Hub IoT vinculado em seu grupo de recursos. Também é possível usar um modelo do Azure Resource Manager para fazer alterações em um serviço de provisionamento ou Hub IoT existentes.

1. Use um editor de texto para criar um modelo do Azure Resource Manager chamado **template.json** com o seguinte conteúdo do esqueleto. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Substitua a seção **parâmetros** pelo conteúdo a seguir. A seção de parâmetros define parâmetros cujos valores podem ser passados de um outro arquivo. Esta seção define o nome do Hub IoT e o serviço de provisionamento a criar. Ela também define a localização para o Hub IoT e para o serviço de provisionamento. Os valores serão restritos a regiões do Azure que oferecem suporte a Hubs IoT e serviços de provisionamento. Para obter uma lista de locais com suporte para o Serviço de Provisionamento de Dispositivos, você pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` a seguir ou ir para a página [Status do Azure](https://azure.microsoft.com/status/) e procurar por "Serviço de Provisionamento de Dispositivos".

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Substitua a seção **variáveis** pelo conteúdo a seguir. Esta seção define os valores que serão usados posteriormente para construir a cadeia de conexão do Hub IoT, que é necessária para vincular o serviço de provisionamento e o Hub IoT. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Para criar um Hub IoT, adicione as linhas a seguir para a coleção **recursos**. O JSON especifica as propriedades mínimas necessárias para criar um Hub IoT. Os valores de **nome** e **localização** serão passados como parâmetros de outro arquivo. Para saber mais sobre as propriedades que você pode especificar para um Hub IoT em um modelo, confira a [referência de modelo Microsoft.Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Para criar o serviço de provisionamento, adicione as seguintes linhas após a especificação do Hub IoT na coleção **recursos**. O **nome** e a **localização** do serviço de provisionamento serão passados como parâmetros. A coleção **iotHubs** especifica os hubs IoT a serem vinculados ao serviço de provisionamento. No mínimo, você deve especificar as propriedades **connectionString** e **location** para cada Hub IoT vinculado. Também é possível definir propriedades como **allocationWeight** e **applyAllocationPolicy** em cada Hub IoT, além de propriedades como **allocationPolicy** e **authorizationPolicies** no serviço de provisionamento em si. Para saber mais, confira a [referência de modelo Microsoft.Devices/provisioningServices](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   A propriedade **dependsOn** é usada para garantir que o Gerenciador de Recursos crie o Hub IoT antes de criar o serviço de provisionamento. O modelo requer a cadeia de conexão do Hub IoT para especificar o vínculo com o serviço de provisionamento, para que o hub e suas chaves sejam criados primeiro. O modelo usa funções como **concat** e **listKeys** para criar a cadeia de conexão com base nas variáveis parametrizadas. Para saber mais, confira [Funções de modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Salvar o arquivo de modelo. O modelo concluído deve ter a seguinte aparência:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Criar um arquivo de parâmetro do Gerenciador de Recursos

O modelo que você definiu na última etapa usa parâmetros para especificar o nome do Hub IoT, o nome do serviço de provisionamento e a localização (região do Azure) no qual criá-los. Você passa esses parâmetros para o modelo em um arquivo separado. Dessa forma, é possível reutilizar o mesmo modelo para várias implantações. Para criar o arquivo de parâmetro, siga estas etapas:

1. Use um editor de texto para criar um arquivo de parâmetro do Azure Resource Manager chamado **parameters.json** com o seguinte conteúdo de esqueleto: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Adicione o valor **iotHubName** para a seção do parâmetro.  O nome de um Hub IoT deve ser globalmente exclusivo no Azure, de modo que talvez você queira adicionar um prefixo ou sufixo exclusivo ao nome de exemplo ou escolher um novo nome. Certifique-se de que seu nome siga a convenção de nomenclatura adequada do hub IoT: ele deve ter de 3 a 50 caracteres de comprimento e conter somente caracteres alfanuméricos minúsculos ou hifens ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Adicione o valor **provisioningServiceName** à seção do parâmetro. Você também precisará escolher um nome globalmente exclusivo para seu serviço de provisionamento. Certifique-se de que ele siga a convenção de nomenclatura adequada para um Serviço de Provisionamento de Dispositivos no Hub IoT: ele deve ter de 3 a 64 caracteres de comprimento e conter somente caracteres alfanuméricos minúsculos ou hifens ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Adicione o valor **hubLocation** à seção do parâmetro. Esse valor especifica o local para o Hub IoT e para o serviço de provisionamento. O valor deve corresponder a um dos locais especificados na coleção **allowedValues** na definição do parâmetro no arquivo de modelo. Essa coleção restringe os valores para os locais do Azure que oferecem suporte a Hubs IoT e a serviços de provisionamento. Para obter uma lista de locais com suporte para o Serviço de Provisionamento de Dispositivos, você pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou ir para a página [Status do Azure](https://azure.microsoft.com/status/) e procurar por "Serviço de Provisionamento de Dispositivos".

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Salve o arquivo. 


> [!IMPORTANT]
> O Hub IoT e o serviço de provisionamento serão detectáveis publicamente como pontos de extremidade DNS, portanto, evite usar qualquer informação confidencial ao nomeá-los.
>

## <a name="deploy-the-template"></a>Implantar o modelo

Use os seguintes comandos da CLI do Azure para implantar seus modelos e verificar a implantação.

1. Para implantar seu modelo, navegue até a pasta que contém o modelo e os arquivos de parâmetro e execute o seguinte [comando para iniciar uma implantação](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create&preserve-view=true):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Esta operação pode demorar alguns minutos para ser concluída. Após concluída, procure a propriedade **provisioningState** mostrando "Êxito" na saída. 

   ![Saída do provisionamento](./media/quick-setup-auto-provision-rm/output.png) 


2. Para verificar sua implantação, execute o seguinte [comando para listar recursos](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true) e procure pelo novo serviço de provisionamento e pelo Hub IoT na saída:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse início rápido. Caso não planeje continuar, você pode usar a CLI do Azure para [excluir um recurso individual][lnk-az-resource-command], como um hub IoT ou um serviço de provisionamento, ou excluir um grupo de recursos e todos os seus recursos.

Para excluir o serviço de provisionamento, execute o seguinte comando:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Para excluir um Hub IoT, execute o seguinte comando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Para excluir um grupo de recursos e todos os seus recursos, execute o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

Você também pode excluir grupos de recursos e recursos individuais usando o Portal do Azure, o PowerShell e APIs REST, além de SDKs de plataforma com suporte publicados para o Azure Resource Manager ou o Serviço de Provisionamento de Dispositivos no Hub IoT.

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você implantou um Hub IoT e uma instância do Serviço de Provisionamento de Dispositivos, bem como vinculou os dois recursos. Para aprender a usar essa configuração a fim de provisionar um dispositivo simulado, prossiga para o Início Rápido de criação de dispositivo simulado.

> [!div class="nextstepaction"]
> [Início Rápido para criar um dispositivo simulado](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
