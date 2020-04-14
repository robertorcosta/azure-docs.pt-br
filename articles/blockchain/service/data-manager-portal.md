---
title: Configure o Blockchain Data Manager usando o portal Azure - Azure Blockchain Service
description: Crie e gerencie o Blockchain Data Manager para o Azure Blockchain Service usando o portal Azure.
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 08f5a4a807087afce13dd4a6e96c0e9dd0a36103
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260591"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurar o Gerenciador de Dados Blockchain usando o portal do Azure

Configure o Blockchain Data Manager para o Azure Blockchain Service para capturar dados blockchain e enviá-los para um Tópico de Grade de Eventos do Azure.

Para configurar uma instância do Blockchain Data Manager, você:

* Crie uma instância do Blockchain Data Manager para um nó de transação do Azure Blockchain Service
* Adicione suas aplicações blockchain

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart completo: Crie um membro blockchain usando o portal Azure](create-member.md) ou [Quickstart: Crie um membro blockchain do Azure Blockchain Service usando o Azure CLI](create-member-cli.md). O nível Padrão *de* Serviço blockchain do Azure é recomendado ao usar o Blockchain Data Manager.
* Criar um [tópico da grade de eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Aprenda sobre [Manipuladores de eventos na Grade de Eventos do Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain conecta e monitora um nó de transação Azure Blockchain Service. Somente usuários com acesso ao nó de transação podem criar uma conexão. Uma instância captura todos os dados brutos de bloqueio e de transação provenientes do nó de transação. O Blockchain Data Manager publica uma mensagem **RawBlockAndTransactionMsg** que é um superconjunto de informações devolvidas do web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) e obter consultas [de transações.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)

Uma conexão de saída envia os dados de blockchain para a Grade de Eventos do Azure. Você configura uma conexão de saída única quando cria a instância. O Gerenciador de Dados de Blockchain é compatível com várias conexões de saída aos Tópico da Grade de Eventos para qualquer instância especificada do Gerenciador de Dados de Blockchain. Você pode enviar dados de blockchain para um único destino ou para vários destinos. Para adicionar outro destino, basta adicionar conexões de saída adicionais à instância.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do Azure Blockchain Service que deseja conectar-se ao Blockchain Data Manager. Selecione **Gerenciador de Dados do Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar Gerenciador de Dados do Blockchain](./media/data-manager-portal/add-instance.png)

    Insira os seguintes detalhes:

    Configuração | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para um Gerenciador de Dados do Blockchain conectado. O nome blockchain Data Manager pode conter letras e números minúsculos e tem um comprimento máximo de 20 caracteres.
    Nó de transação | Escolha um nó de transação. Apenas nós de transação que você tenha acesso de leitura estão listados.
    Nome da conexão | Insira um nome exclusivo para a conexão de saída pela qual os dados da transação de blockchain são enviados.
    Ponto de extremidade da Grade de Eventos | Escolha um tópico de grade de eventos na mesma assinatura que a instância do Blockchain Data Manager.

1. Selecione **OK**.

    Leva menos de um minuto para criar uma instância do Gerenciador de Dados do Blockchain. Depois que a instância for implantada, ela será iniciada automaticamente. Uma instância do Blockchain Data Manager em execução captura eventos blockchain do nó de transação e envia dados para as conexões de saída.

    A nova instância aparece na lista de instâncias do Blockchain Data Manager para o membro do Azure Blockchain Service.

    ![Lista de instâncias de membros de dados de blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Adicionar aplicativo blockchain

Se você adicionar um aplicativo blockchain, o Blockchain Data Manager decodifica o estado de propriedade e evento para o aplicativo. Caso contrário, apenas dados de bloco bruto e transações brutas são enviados. O Blockchain Data Manager também descobre endereços de contrato quando o contrato é implantado. Você pode adicionar vários aplicativos blockchain a uma instância do Blockchain Data Manager.

> [!IMPORTANT]
> Atualmente, os aplicativos blockchain que declaram [tipos de array solidity](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) ou [tipos de mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não são totalmente suportados. As propriedades declaradas como array ou tipos de mapeamento não serão decodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg.*

O Blockchain Data Manager requer um contrato inteligente ABI e um arquivo bytecode implantado para adicionar o aplicativo.

### <a name="get-contract-abi-and-bytecode"></a>Obter contrato ABI e bytecode

O ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode usar a [Extensão Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar a ABI do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar ABI do Contrato**.

    ![Painel do Visual Studio Code com a seleção de Copiar ABI do Contrato](./media/data-manager-portal/abi-devkit.png)

    O ABI do contrato é copiado para a área de transferência.

1. Salve a matriz **abi** como um arquivo JSON. Por exemplo, *abi.json*. Você usará esse arquivo em uma etapa posterior.

O Gerenciador de Dados do Blockchain requer o código de bytes implantado para o contrato inteligente. O código de bytes implantado é diferente do código de bytes do contrato inteligente. Use a extensão do Azure Blockchain Development Kit para copiar o código de bytes para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Selecione **Copiar Código de Bites de Transação**.

    ![Painel do Visual Studio Code com a seleção de Copiar Código de Bytes da Transação](./media/data-manager-portal/bytecode-devkit.png)

    O código de bytes é copiado para a área de transferência.

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
    | Nome  | Dê um nome ao contêiner. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *Privado (sem acesso anônimo)* |

1. Selecione **OK** para criar o contêiner.
1. Selecione o contêiner e selecione **Upload**.
1. Escolha os arquivos JSON que você criou na seção [Obter a ABI do contrato e o código de bytes](#get-contract-abi-and-bytecode).

    ![Carregar blob](./media/data-manager-portal/upload-blobs.png)

    Selecione **Carregar**.

#### <a name="generate-url"></a>Gerar a URL

Para cada blob, gere uma assinatura de acesso compartilhado.

1. Selecione o blob JSON da ABI.
1. Selecione **Gerar SAS**
1. Defina a expiração desejada para a assinatura de acesso e, em seguida, selecione **Gerar token SAS de blob e URL**.

    ![Gerar token SAS](./media/data-manager-portal/generate-sas.png)

1. Copie a **URL de SAS do blob** e salve-a para a próxima seção.
1. Repita as etapas de [Gerar URL](#generate-url) para o blob do código de bytes JSON.

### <a name="add-application-to-instance"></a>Adicionar aplicativo à instância

1. Selecione sua instância de Gerenciador de Dados do Blockchain na lista de instâncias.
1. Selecione **Aplicativos de blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar um aplicativo de blockchain](./media/data-manager-portal/add-application.png)

    Insira o nome do aplicativo de blockchain, a URL da ABI do contrato inteligente e a URL do código de bytes.

    Configuração | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para o aplicativo de blockchain rastrear.
    ABI do Contrato | Caminho da URL para o arquivo da ABI do contrato. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes do contrato | Caminho da URL para o arquivo de código de bytes. Para obter mais informações, confira [Criar a ABI do contrato e a URL do código de bytes](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Depois que o aplicativo é criado, ele aparece na lista de aplicativos de blockchain.

    ![Lista de aplicativos de blockchain](./media/data-manager-portal/artifact-list.png)

Você pode excluir a conta de Armazenamento do Azure ou usá-la para configurar mais aplicativos de blockchain. Se quiser excluir a conta de Armazenamento do Azure, você poderá excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos.

## <a name="stop-instance"></a>Exemplo de parada

Interrompa a instância do Blockchain Manager quando você quiser parar de capturar eventos blockchain e enviar dados para as conexões de saída. Quando a ocorrência é interrompida, não são cobradas taxas para o Blockchain Data Manager. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Vá para **Visão Geral** e selecione **Stop**.

    ![Exemplo de parada](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Próximas etapas

Experimente o próximo tutorial criando um explorador de mensagens de transação blockchain usando o Blockchain Data Manager e o Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Usar o Gerenciador de Dados do Blockchain para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md)