---
title: Configure o Blockchain Data Manager usando o Azure CLI - Azure Blockchain Service
description: Crie e gerencie um Blockchain Data Manager para o Azure Blockchain Service usando o Azure CLI
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: e490803fabeed7d6234bd6984acbfb9f5270e0c0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254403"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurar o Gerenciador de Dados Blockchain usando a CLI do Azure

Configure o Blockchain Data Manager para o Azure Blockchain Service para capturar dados blockchain enviá-los para um Tópico de Grade de Eventos do Azure.

Para configurar uma instância do Blockchain Data Manager, você:

* Crie uma instância do Blockchain Manager
* Crie uma entrada para um nó de transação do Azure Blockchain Service
* Crie uma saída para um tópico da grade de eventos do Azure
* Adicionar um aplicativo de blockchain
* Inicie uma instância

## <a name="prerequisites"></a>Pré-requisitos

* Instale o mais recente [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e inscrevi-se usando `az login`.
* [Quickstart completo: Use o Visual Studio Code para se conectar a uma rede de consórcio saqueador a azure Blockchain Service](connect-vscode.md). O nível Padrão *de* Serviço blockchain do Azure é recomendado ao usar o Blockchain Data Manager.
* Criar um [tópico da grade de eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Aprenda sobre [Manipuladores de eventos na Grade de Eventos do Azure](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell [https://shell.azure.com/bash](https://shell.azure.com/bash)em uma guia de navegador separada indo para . Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.51 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Criar instância

Uma instância do Blockchain Data Manager monitora um nó de transação do Azure Blockchain Service. Uma instância captura todos os dados brutos de bloqueio e de transação provenientes do nó de transação. O Blockchain Data Manager publica uma mensagem **RawBlockAndTransactionMsg** que é um superconjunto de informações devolvidas do web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) e obter consultas [de transações.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar a instância do Blockchain Data Manager. |
| name | Nome da instância do Gerenciador de Dados blockchain. |
| tipo de recurso | O tipo de recurso para uma instância do Blockchain Data Manager é **microsoft.blockchain/watchers**. |
| is-full-object | Indica que as propriedades contêm opções para o recurso watcher. |
| properties | String formatado pelo JSON contendo propriedades para o recurso watcher. Pode ser passado como uma seqüência ou um arquivo.  |

### <a name="create-instance-examples"></a>Criar exemplos de instâncias

Exemplo de configuração JSON para criar uma instância do Blockchain Manager na região **leste dos EUA.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| local | Região onde criar o recurso de observador |
| properties | Propriedades a serem definidas ao criar o recurso watcher |

Crie uma instância do Blockchain Data Manager chamada *mywatcher* usando uma seqüência JSON para configuração.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Crie uma instância do Blockchain Data Manager chamada *mywatcher* usando um arquivo de configuração JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Criar entrada

Uma entrada conecta o Blockchain Data Manager a um nó de transação do Azure Blockchain Service. Somente usuários com acesso ao nó de transação podem criar uma conexão.

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

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar o recurso de entrada. |
| name | Nome da entrada. |
| namespace | Use o namespace do provedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma entrada do Blockchain Data Manager são **entradas**. |
| pai | O caminho para o observador ao qual a entrada está associada. Por exemplo, **observadores/mywatcher**. |
| is-full-object | Indica que as propriedades contêm opções para o recurso de entrada. |
| properties | String formatado pelo JSON contendo propriedades para o recurso de entrada. Pode ser passado como uma seqüência ou um arquivo. |

### <a name="input-examples"></a>Exemplos de entrada

Configuração exemplo JSON para criar um recurso de \<entrada\>na região leste *dos EUA* que está conectado ao membro Blockchain .

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
| local | Região onde criar o recurso de entrada. |
| Inputtype | Tipo de livro-razão do membro do Azure Blockchain Service. Atualmente, **o Ethereum** é suportado. |
| resourceId | Nó de transação ao qual a entrada está conectada. \<Substitua o\> \<ID\>de \<assinatura, o grupo de recursos e o membro\> Blockchain com os valores para o recurso de nó de transação. A entrada se conecta ao nó de transação padrão para o membro do Azure Blockchain Service. |

Crie uma entrada chamada *myInput* para *mywatcher* usando uma seqüência JSON para configuração.

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

Crie uma entrada chamada *myInput* para *mywatcher* usando um arquivo de configuração JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Criar saída

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

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar o recurso de saída. |
| name | Nome da saída. |
| namespace | Use o namespace do provedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma saída do Blockchain Data Manager é **outputs**. |
| pai | O caminho para o observador ao qual a saída está associada. Por exemplo, **observadores/mywatcher**. |
| is-full-object | Indica que as propriedades contêm opções para o recurso de saída. |
| properties | String formatado pelo JSON contendo propriedades para o recurso de saída. Pode ser passado como uma seqüência ou um arquivo. |

### <a name="output-examples"></a>Exemplos de saída

Configuração exemplo JSON para criar um recurso de saída na região \<leste\> *dos EUA* que está conectado a um tópico de grade de eventos chamado tópico da grade de eventos .

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
| local | Região onde criar o recurso de saída. |
| Outputtype | Tipo de saída. Atualmente, **o EventGrid** é suportado. |
| resourceId | Recurso ao qual a saída está conectada. \<Substitua o\> \<ID\>de \<assinatura, o grupo de recursos e o membro\> Blockchain com os valores para o recurso da grade de eventos. |

Crie uma saída chamada *myoutput* for *mywatcher* que se conecta a um tópico de grade de eventos usando uma seqüência de configuração JSON.

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

Crie uma saída chamada *myoutput* for *mywatcher* que se conecta a um tópico de grade de eventos usando um arquivo de configuração JSON.

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

## <a name="add-blockchain-application"></a>Adicionar aplicativo blockchain

Se você adicionar um aplicativo blockchain, o Blockchain Data Manager decodifica o estado de propriedade e evento para o aplicativo. Caso contrário, apenas dados de bloco bruto e transações brutas são enviados. O Blockchain Data Manager também descobre endereços de contrato quando o contrato é implantado. Você pode adicionar vários aplicativos blockchain a uma instância do Blockchain Data Manager.


> [!IMPORTANT]
> Atualmente, os aplicativos blockchain que declaram [tipos de array solidity](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) ou [tipos de mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não são totalmente suportados. As propriedades declaradas como array ou tipos de mapeamento não serão decodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg.*

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

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar o recurso de aplicação. |
| name | Nome do aplicativo. |
| namespace | Use o namespace do provedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para um aplicativo blockchain data manager é **artefatos**. |
| pai | O caminho para o observador ao qual a aplicação está associada. Por exemplo, **observadores/mywatcher**. |
| is-full-object | Indica que as propriedades contêm opções para o recurso do aplicativo. |
| properties | String formatado pelo JSON contendo propriedades para o recurso do aplicativo. Pode ser passado como uma seqüência ou um arquivo. |

### <a name="blockchain-application-examples"></a>Exemplos de aplicativos blockchain

Configuração exemplo JSON para criar um recurso de aplicativo na região *leste dos EUA* que monitora um contrato inteligente definido pelo contrato ABI e bytecode.

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
| local | Região onde criar o recurso de aplicação. |
| artefatoType | Tipo de aplicativo. Atualmente, **o EthereumSmartContract** é suportado. |
| abiFileUrl | URL para contrato inteligente arquivo ABI JSON. Para obter mais informações sobre a obtenção de contrato ABI e a criação de uma URL, consulte [Obter contrato ABI e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e Criar contrato ABI e URL [bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL para contrato inteligente implantado bytecode arquivo JSON. Para obter mais informações sobre a obtenção do contrato inteligente implantado bytecode e a criação de uma URL, consulte [Obter contrato ABI e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e Criar contrato ABI e URL [bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). Nota: O Blockchain Data Manager requer o **bytecode implantado**. |
| queryTargetTypes | Tipos de mensagens publicadas. Especificação **ContractProperties** publica *contractPropertiesMsg* tipo de mensagem. Especificar **ContractEvents** publica o tipo de mensagem *DecodedContractEventsMsg.* Nota: *RawBlockAndTransactionMsg* e *RawTransactionContractCreationOs* tipos de mensagens são sempre publicados. |

Crie um aplicativo chamado *myApplication* for *mywatcher* que monitora um contrato inteligente definido por uma seqüência json.

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

Crie um aplicativo chamado *myApplication* for *mywatcher* que assiste a um contrato inteligente definido usando um arquivo de configuração JSON.

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

## <a name="start-instance"></a>Exemplo de início

Ao ser executado, uma instância do Blockchain Manager monitora os eventos blockchain a partir das entradas definidas e envia dados para as saídas definidas.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ação | Use **comece** a correr o observador. |
| ids | ID de recursos do observador. \<Substitua o\> \<ID\>de \<assinatura, o grupo de recursos e o nome\> do Observador com os valores para o recurso watcher.|

### <a name="start-instance-example"></a>Exemplo de exemplo de início

Inicie uma instância do Blockchain Data Manager chamada *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Exemplo de parada

Pare uma instância do Blockchain Data Manager.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ação | Use **pare** para parar o observador. |
| ids | Nome do observador. \<Substitua o\> \<ID\>de \<assinatura, o grupo de recursos e o nome\> do Observador com os valores para o recurso watcher. |

### <a name="stop-watcher-example"></a>Exemplo de observador de parada

Pare um exemplo chamado *mywatcher.*

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Exclusão de instância

Exclua uma instância do Blockchain Data Manager.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos do observador para excluir. |
| name | Nome do observador para excluir. |
| tipo de recurso | O tipo de recurso para um observador de dados blockchain é **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Exemplo de exemplo de exclusão

Exclua uma instância chamada *mywatcher* no grupo de recursos *myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Próximas etapas

Experimente o próximo tutorial criando um explorador de mensagens de transação blockchain usando o Blockchain Data Manager e o Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Usar o Gerenciador de Dados do Blockchain para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md)
