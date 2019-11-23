---
title: Configure Blockchain Data Manager using Azure portal - Azure Blockchain Service
description: Create and manage Blockchain Data Manager for Azure Blockchain Service using the Azure portal.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 2ab6c3333635e6c830bc2a36a0c5152fe3b78c49
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326230"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurar o Gerenciador de Dados Blockchain usando o portal do Azure

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data and send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Data Manager instance for an Azure Blockchain Service transaction node
* Add your blockchain applications

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)
* Criar um [tópico da Grade de Eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Aprenda sobre [Manipuladores de eventos na Grade de Eventos do Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain conecta e monitora um nó de transação Azure Blockchain Service. Only users with access to the transaction node can create a connection. Uma instância captura todos os dados brutos de bloqueio e de transação provenientes do nó de transação.

Uma conexão de saída envia os dados de blockchain para a Grade de Eventos do Azure. Você configura uma conexão de saída única quando cria a instância. O Gerenciador de Dados de Blockchain é compatível com várias conexões de saída aos Tópico da Grade de Eventos para qualquer instância especificada do Gerenciador de Dados de Blockchain. Você pode enviar dados de blockchain para um único destino ou para vários destinos. To add another destination, just add additional outbound connections to the instance.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Go to the Azure Blockchain Service member you want to connect to Blockchain Data Manager. Selecione **Gerenciador de Dados do Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar Gerenciador de Dados do Blockchain](./media/data-manager-portal/add-instance.png)

    Insira os seguintes detalhes:

    Configuração | Descrição
    --------|------------
    name | Insira um nome exclusivo para um Gerenciador de Dados do Blockchain conectado. The Blockchain Data Manager name can contain lower case letters and numbers and has a maximum length of 20 characters.
    Nó de transação | Choose a transaction node. Only transaction nodes you have read access are listed.
    Nome da conexão | Insira um nome exclusivo para a conexão de saída pela qual os dados da transação de blockchain são enviados.
    Ponto de extremidade da Grade de Eventos | Choose an event grid topic in the same subscription as the Blockchain Data Manager instance.

1. Selecione **OK**.

    Leva menos de um minuto para criar uma instância do Gerenciador de Dados do Blockchain. Depois que a instância for implantada, ela será iniciada automaticamente. A running Blockchain Data Manager instance captures blockchain events from the transaction node and sends data to the outbound connections.

    The new instance appears in the list of Blockchain Data Manager instances for the Azure Blockchain Service member.

    ![List of Blockchain Data Member instances](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.

> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

Blockchain Data Manager requires a smart contract ABI and deployed bytecode file to add the application.

### <a name="get-contract-abi-and-bytecode"></a>Get Contract ABI and bytecode

O ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode usar a [Extensão Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar a ABI do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar ABI do Contrato**.

    ![Painel do Visual Studio Code com a seleção de Copiar ABI do Contrato](./media/data-manager-portal/abi-devkit.png)

    O ABI do contrato é copiado para a área de transferência.

1. Salve a matriz **abi** como um arquivo JSON. Por exemplo, *abi.json*. Você usará esse arquivo em uma etapa posterior.

O Gerenciador de Dados do Blockchain requer o código de bytes implantado para o contrato inteligente. O código de bytes implantado é diferente do código de bytes do contrato inteligente. Você pode obter o código de bytes implantado do arquivo de metadados do contrato compilado.

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize o elemento **deployedBytecode** no arquivo JSON.
1. Copie o valor hexadecimal sem as aspas.

    ![Painel do Visual Studio Code com o código de bytes nos metadados](./media/data-manager-portal/bytecode-metadata.png)

1. Salve o valor de **código de bytes** como um arquivo JSON. Por exemplo, *bytecode.json*. Você usará esse arquivo em uma etapa posterior.

O exemplo a seguir mostra os arquivos *abi.json* e *bytecode.json* abertos no editor do VS Code. Os arquivos devem ser semelhantes.

![Exemplo de arquivos abi.json e bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar a ABI do contrato e a URL do código de bytes

O Gerenciador de Dados do Blockchain requer que os arquivos de código de bytes e da ABI e do contrato sejam acessíveis por meio de uma URL ao adicionar um aplicativo. Você pode usar uma conta de Armazenamento do Azure para fornecer uma URL acessível de modo privado.

#### <a name="create-storage-account"></a>Criar Conta de Armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Fazer upload de arquivos de contrato

1. Crie um novo contêiner para a conta de armazenamento. Selecione **Contêineres > Contêiner**.

    ![Criar um contêiner de conta de armazenamento](./media/data-manager-portal/create-container.png)

    | Campo | Descrição |
    |-------|-------------|
    | name  | Dê um nome ao contêiner. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *Privado (sem acesso anônimo)* |

1. Selecione **OK** para criar o contêiner.
1. Selecione o contêiner e selecione **Upload**.
1. Escolha os arquivos JSON que você criou na seção [Obter a ABI do contrato e o código de bytes](#get-contract-abi-and-bytecode).

    ![Carregar blob](./media/data-manager-portal/upload-blobs.png)

    Escolha **Carregar**.

#### <a name="generate-url"></a>Gerar a URL

Para cada blob, gere uma assinatura de acesso compartilhado.

1. Selecione o blob JSON da ABI.
1. Selecione **Gerar SAS**
1. Defina a expiração desejada para a assinatura de acesso e, em seguida, selecione **Gerar token SAS de blob e URL**.

    ![Gerar token SAS](./media/data-manager-portal/generate-sas.png)

1. Copie a **URL de SAS do blob** e salve-a para a próxima seção.
1. Repita as etapas de [Gerar URL](#generate-url) para o blob do código de bytes JSON.

### <a name="add-application-to-instance"></a>Add application to instance

1. Selecione sua instância de Gerenciador de Dados do Blockchain na lista de instâncias.
1. Selecione **Aplicativos de blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar um aplicativo de blockchain](./media/data-manager-portal/add-application.png)

    Insira o nome do aplicativo de blockchain, a URL da ABI do contrato inteligente e a URL do código de bytes.

    Configuração | Descrição
    --------|------------
    name | Insira um nome exclusivo para o aplicativo de blockchain rastrear.
    ABI do Contrato | Caminho da URL para o arquivo da ABI do contrato. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes do contrato | Caminho da URL para o arquivo de código de bytes. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Depois que o aplicativo é criado, ele aparece na lista de aplicativos de blockchain.

    ![Lista de aplicativos de blockchain](./media/data-manager-portal/artifact-list.png)

Você pode excluir a conta de Armazenamento do Azure ou usá-la para configurar mais aplicativos de blockchain. Se quiser excluir a conta de Armazenamento do Azure, você poderá excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos.

## <a name="stop-instance"></a>Stop instance

Stop the Blockchain Manager instance when you want to stop capturing blockchain events and sending data to the outbound connections. When the instance is stopped, no charges are incurred for Blockchain Data Manager. Para saber mais, confira os [preços](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Go to **Overview** and select **Stop**.

    ![Stop instance](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Próximos passos

Try creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Tutorial: Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)