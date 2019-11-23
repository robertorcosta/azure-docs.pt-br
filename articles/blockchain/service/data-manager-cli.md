---
title: Configure Blockchain Data Manager using Azure CLI - Azure Blockchain Service
description: Create and manage a Blockchain Data Manager for Azure Blockchain Service using Azure CLI
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 88abea691219a78ee16702e231337de055dbf5e4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326231"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurar o Gerenciador de Dados Blockchain usando a CLI do Azure

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Manager instance
* Create an input to an Azure Blockchain Service transaction node
* Create an output to an Azure Event Grid Topic
* Adicionar um aplicativo de blockchain
* Start an instance

## <a name="prerequisites"></a>Pré-requisitos

* Install the latest [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) and signed in using `az login`.
* Complete [Quickstart: Use Visual Studio Code to connect to a Azure Blockchain Service consortium network](connect-vscode.md)
* Criar um [tópico da Grade de Eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Aprenda sobre [Manipuladores de eventos na Grade de Eventos do Azure](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.51 ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalá-la ou atualizá-la, confira [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Criar instância

A Blockchain Data Manager instance monitors an Azure Blockchain Service transaction node. Uma instância captura todos os dados brutos de bloqueio e de transação provenientes do nó de transação.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| . | Descrição |
|-----------|-------------|
| resource-group | Resource group name where to create the Blockchain Data Manager instance. |
| Nome | Name of the Blockchain Data Manager instance. |
| resource-type | The resource type for a Blockchain Data Manager instance is **Microsoft.blockchain/watchers**. |
| is-full-object | Indicates properties contain options for the watcher resource. |
| properties | JSON-formatted string containing properties for the watcher resource. Can be passed as a string or a file.  |

### <a name="create-instance-examples"></a>Create instance examples

JSON configuration example to create a Blockchain Manager instance in the **East US** region.

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Region where to create the watcher resource |
| properties | Properties to set when creating the watcher resource |

Create a Blockchain Data Manager instance named *mywatcher* using a JSON string for configuration.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Create a Blockchain Data Manager instance named *mywatcher* using a JSON configuration file.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Criar entrada

An input connects Blockchain Data Manager to an Azure Blockchain Service transaction node. Only users with access to the transaction node can create a connection.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| . | Descrição |
|-----------|-------------|
| resource-group | Resource group name where to create the input resource. |
| Nome | Name of the input. |
| namespace | Use the **Microsoft.Blockchain** provider namespace. |
| resource-type | The resource type for a Blockchain Data Manager input is **inputs**. |
| parent | The path to the watcher to which the input is associated. For example, **watchers/mywatcher**. |
| is-full-object | Indicates properties contain options for the input resource. |
| properties | JSON-formatted string containing properties for the input resource. Can be passed as a string or a file. |

### <a name="input-examples"></a>Input examples

Configuration JSON example to create an input resource in the *East US* region that is connected to \<Blockchain member\>.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Region where to create the input resource. |
| inputType | Ledger type of the Azure Blockchain Service member. Currently, **Ethereum** is supported. |
| ResourceId | Transaction node to which the input is connected. Replace \<Subscription ID\>, \<Resource group\>, and \<Blockchain member\> with the values for the transaction node resource. The input connects to the default transaction node for the Azure Blockchain Service member. |

Create an input named *myInput* for *mywatcher* using a JSON string for configuration.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Create an input named *myInput* for *mywatcher* using a JSON configuration file.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Create output

Uma conexão de saída envia os dados de blockchain para a Grade de Eventos do Azure. Você pode enviar dados de blockchain para um único destino ou para vários destinos. O Gerenciador de Dados de Blockchain é compatível com várias conexões de saída aos Tópico da Grade de Eventos para qualquer instância especificada do Gerenciador de Dados de Blockchain.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| . | Descrição |
|-----------|-------------|
| resource-group | Resource group name where to create the output resource. |
| Nome | Name of the output. |
| namespace | Use the **Microsoft.Blockchain** provider namespace. |
| resource-type | The resource type for a Blockchain Data Manager output is **outputs**. |
| parent | The path to the watcher to which the output is associated. For example, **watchers/mywatcher**. |
| is-full-object | Indicates properties contain options for the output resource. |
| properties | JSON-formatted string containing properties for the output resource. Can be passed as a string or a file. |

### <a name="output-examples"></a>Output examples

Configuration JSON example to create an output resource in the *East US* region that is connected to an event grid topic named \<event grid topic\>.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Region where to create the output resource. |
| outputType | Type of output. Currently, **EventGrid** is supported. |
| ResourceId | Resource to which the output is connected. Replace \<Subscription ID\>, \<Resource group\>, and \<Blockchain member\> with the values for the event grid resource. |

Create an output named *myoutput* for *mywatcher* that connects to an event grid topic using a JSON configuration string.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Create an output named *myoutput* for *mywatcher* that connects to an event grid topic using a JSON configuration file.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.


> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| . | Descrição |
|-----------|-------------|
| resource-group | Resource group name where to create the application resource. |
| Nome | Name of the application. |
| namespace | Use the **Microsoft.Blockchain** provider namespace. |
| resource-type | The resource type for a Blockchain Data Manager application is **artifacts**. |
| parent | The path to the watcher to which the application is associated. For example, **watchers/mywatcher**. |
| is-full-object | Indicates properties contain options for the application resource. |
| properties | JSON-formatted string containing properties for the application resource. Can be passed as a string or a file. |

### <a name="blockchain-application-examples"></a>Blockchain application examples

Configuration JSON example to create an application resource in the *East US* region that monitors a smart contract defined by the contract ABI and bytecode.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Region where to create the application resource. |
| artifactType | Tipo de aplicativo. Currently, **EthereumSmartContract** is supported. |
| abiFileUrl | URL for smart contract ABI JSON file. For more information on obtaining contract ABI and creating a URL, see [Get Contract ABI and bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) and [Create contract ABI and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL for smart contract deployed bytecode JSON file. For more information on obtaining the smart contract deployed bytecode and creating a URL, see [Get Contract ABI and bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) and [Create contract ABI and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Note: Blockchain Data Manager requires the **deployed bytecode**. |
| queryTargetTypes | Published message types. Specifying **ContractProperties** publishes *ContractPropertiesMsg* message type. Specifying **ContractEvents** publishes *DecodedContractEventsMsg* message type. Note: *RawBlockAndTransactionMsg* and *RawTransactionContractCreationMsg* message types are always published. |

Create an application named *myApplication* for *mywatcher* that monitors a smart contract defined by a JSON string.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Create an application named *myApplication* for *mywatcher* that watches a smart contract defined using a JSON configuration file.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Start instance

When running, a Blockchain Manager instance monitors blockchain events from the defined inputs and sends data to the defined outputs.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| . | Descrição |
|-----------|-------------|
| ação | Use **start** to run the watcher. |
| ids | Watcher resource ID. Replace \<Subscription ID\>, \<Resource group\>, and \<Watcher name\> with the values for the watcher resource.|

### <a name="start-instance-example"></a>Start instance example

Start a Blockchain Data Manager instance named *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Stop instance

Stop a Blockchain Data Manager instance.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| . | Descrição |
|-----------|-------------|
| ação | Use **stop** to stop the watcher. |
| ids | Name of the watcher. Replace \<Subscription ID\>, \<Resource group\>, and \<Watcher name\> with the values for the watcher resource. |

### <a name="stop-watcher-example"></a>Stop watcher example

Stop an instance named *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Delete instance

Delete a Blockchain Data Manager instance.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| . | Descrição |
|-----------|-------------|
| resource-group | Resource group name of the watcher to delete. |
| Nome | Name of the watcher to delete. |
| resource-type | The resource type for a Blockchain Data Manager watcher is **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Delete instance example

Delete an instance named *mywatcher* in the *myRG* resource group.

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Próximos passos

Try creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Tutorial: Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)
