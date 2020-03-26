---
title: Usar o Geth para anexar ao Azure Blockchain Service
description: Anexar a uma instância do Geth no nó de transação Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455838"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Início Rápido: Usar o Geth para anexar a um nó de transação do Azure Blockchain Service

Neste Início Rápido, você usa o cliente Geth para se anexar a uma instância do Geth em um nó de transação do Azure Blockchain Service. Uma vez anexado, você usa o console do JavaScript Geth para chamar uma API JavaScript Dapp web3.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instalar o [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Concluir [Início Rápido: Criar um membro do blockchain usando o portal do Azure](create-member.md) ou [Início Rápido: Criar um membro do blockchain do Azure Blockchain Service usando a CLI do Azure](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Obter a cadeia de conexão do Geth

Você pode obter a cadeia de conexão Geth para um nó de transação do Azure Blockchain Service no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Acesse o membro do Azure Blockchain Service. Selecione **Nós de transação** e o link do nó de transação padrão.

    ![Selecionar o nó de transação padrão](./media/connect-geth/transaction-nodes.png)

1. Selecione **Cadeias de conexão**.
1. Copie a cadeia de conexão de **HTTPS (Chave de acesso 1)** . Você precisará da cadeia de caracteres para a próxima seção.

    ![Cadeia de conexão](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Conectar-se ao Geth

1. Abra um prompt de comando ou shell.
1. Use o subcomando attach do Geth para se anexar à instância em execução do Geth no nó de transação. Cole a cadeia de conexão como um argumento para o subcomando attach. Por exemplo:

    ``` bash
    geth attach <connection string>
    ```

1. Depois que estiver conectado ao console do Ethereum do nó de transação, você poderá chamar a API do Dapp JavaScript do web3 ou a API do administrador.

    Por exemplo, use a API a seguir para descobrir a chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Neste exemplo, a chainId é 661.

    ![Opção do serviço Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Para se desconectar do console, digite `exit`.

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você usou o cliente Geth para se anexar a uma instância do Geth em um nó de transação do serviço Azure Blockchain. Experimente o próximo tutorial para usar o Azure Blockchain Development Kit para Ethereum para criar, implantar e executar uma função de contrato inteligente por uma transação.

> [!div class="nextstepaction"]
> [Criar, compilar e implantar contratos inteligentes no Azure Blockchain Service](send-transaction.md)
