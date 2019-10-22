---
title: Usar o Visual Studio Code – Azure Blockchain Service
description: Tutorial sobre como usar a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code para criar e implantar um contrato inteligente no Azure Blockchain Service.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 13a5993a14e386dc7d24c7464610bbf1ace4b9cb
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329238"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>Tutorial: Usar o Visual Studio Code para criar e implantar contratos inteligentes

Neste tutorial, use a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code para criar e implantar um contrato inteligente no Azure Blockchain Service. Use também o Truffle para executar uma função de contrato inteligente por meio de uma transação.

Você usa o Azure Blockchain Development Kit for Ethereum para:

> [!div class="checklist"]
> * Criar um contrato inteligente
> * Implantar um contrato inteligente
> * Executar uma função de contrato inteligente por meio de uma transação
> * Estado do contrato de consulta

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir [Início Rápido: usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md)

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O Azure Blockchain Development Kit for Ethereum usa modelos de projeto e ferramentas do Truffle para ajudar a realizar scaffold, compilar e implantar contratos. Antes de começar, conclua o pré-requisito [Início Rápido: usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md). O início rápido percorre a instalação e a configuração do Azure Blockchain Development Kit para Ethereum.

1. Na paleta de comandos do VS Code, escolha **Azure Blockchain: Novo projeto do Solidity**.
1. Escolha **Criar projeto básico**.
1. Crie uma pasta chamada `HelloBlockchain` e escolha **Selecionar novo caminho de projeto**.

O Azure Blockchain Development Kit cria e inicializa um novo projeto do Solidity para você. O projeto básico inclui um contrato inteligente **HelloBlockchain** de exemplo e todos os arquivos necessários para compilação e implantação no membro do consórcio no Azure Blockchain Service. Pode demorar alguns minutos para que o projeto seja criado. Você pode monitorar o progresso no painel do terminal do VS Code selecionando a saída para o Azure Blockchain.

A estrutura do projeto é semelhante ao exemplo abaixo:

   ![Projeto do Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Compilar um contrato inteligente

Os contratos inteligentes estão localizados no diretório **contracts** do projeto. Você compila contratos inteligentes antes de implantá-los em um blockchain. Use o comando **Compilar Contratos** para compilar todos os contratos inteligentes no projeto.

1. Na barra lateral do explorer do VS Code, expanda a pasta **contracts** no projeto.
1. Clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Compilar Contratos** no menu.

    ![Compilar contratos](./media/send-transaction/build-contracts.png)

O Azure Blockchain Development Kit usa o Truffle para compilar os contratos inteligentes.

![Compilar saída](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Implantar um contrato inteligente

O Truffle usa scripts de migração para implantar seus contratos em uma rede Ethereum. As migrações são arquivos JavaScript localizados no diretório **migrations** do projeto.

1. Para implantar o contrato inteligente, clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Implantar Contratos** no menu.
1. Escolha a rede de consórcio do Azure Blockchain na paleta de comandos. A rede de blockchain de consórcio foi adicionada ao arquivo de configuração do Truffle do projeto quando você criou o projeto.
1. Escolha **Gerar mnemônico**. Escolha um nome de arquivo e salve o arquivo mnemônico na pasta do projeto. Por exemplo, `myblockchainmember.env`. O arquivo mnemônico é usado para gerar uma chave privada Ethereum para o membro do blockchain.

O Azure Blockchain Development Kit usa o Truffle para executar o script de migração para implantar os contratos no blockchain.

![Contrato implantado com êxito](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chamar uma função de contrato

A função **SendRequest** do contrato **HelloBlockchain** altera a variável de estado **RequestMessage**. A alteração do estado de uma rede de blockchain é feita por meio de uma transação. Você pode criar um script para executar a função **SendRequest** por meio de uma transação.

1. Crie um arquivo na raiz do seu projeto do Truffle e nomeie-o `sendrequest.js`. Adicione o código JavaScript Web3 a seguir ao arquivo.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Quando o Azure Blockchain Development Kit cria um projeto, o arquivo de configuração do Truffle é gerado com os detalhes do ponto de extremidade de rede de blockchain de consórcio. Abra **truffle-config.js** no projeto. O arquivo de configuração lista duas redes: uma denominada desenvolvimento e outra com o mesmo nome que o consórcio.
1. No painel do terminal do VS Code, use o Truffle para executar o script na rede de blockchain de consórcio. Na barra de menus do painel do terminal, selecione a guia **Terminal** e o **PowerShell** na lista suspensa.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Substitua \<rede de blockchain\> pelo nome da rede de blockchain definida em **truffle-config.js**.

O Truffle executa o script na rede de blockchain.

![Saída do script](./media/send-transaction/execute-transaction.png)

Quando você executa uma função de contrato por meio de uma transação, a transação não é processada até que um bloco seja criado. As funções destinadas a serem executadas por meio de uma transação retornam uma ID de transação em vez de um valor retornado.

## <a name="query-contract-state"></a>Estado do contrato de consulta

As funções de contrato inteligente podem retornar o valor atual das variáveis de estado. Vamos adicionar uma função para retornar o valor de uma variável de estado.

1. Em **HelloBlockchain.sol**, adicione uma função **getMessage** ao contrato inteligente **HelloBlockchain**.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    A função retorna a mensagem armazenada em uma variável de estado com base no estado atual do contrato.

1. Clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Compilar Contratos** no menu para compilar as alterações ao contrato inteligente.
1. Para implantar, clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Implantar Contratos** no menu. Quando solicitado, escolha a rede de consórcio do Azure Blockchain na paleta de comandos.
1. Em seguida, crie um script usando para chamar a função **getMessage**. Crie um arquivo na raiz do seu projeto do Truffle e nomeie-o `getmessage.js`. Adicione o código JavaScript Web3 a seguir ao arquivo.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. No painel do terminal do VS Code, use o Truffle para executar o script na rede de blockchain. Na barra de menus do painel do terminal, selecione a guia **Terminal** e o **PowerShell** na lista suspensa.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Substitua \<rede de blockchain\> pelo nome da rede de blockchain definida em **truffle-config.js**.

Chamando a função getMessage, o script consulta o contrato inteligente. O valor atual da variável de estado **RequestMessage** é retornado.

![Saída do script](./media/send-transaction/execute-get.png)

Observe que o valor não é **Olá, blockchain!** . Em vez disso, o valor retornado é um espaço reservado. Quando você altera e implanta o contrato, um novo endereço de contrato é atribuído a ele, enquanto valores no construtor do contrato inteligente são atribuídos às variáveis de estado. O script de migração de exemplo do Truffle **2_deploy_contracts.js** implanta o contrato inteligente e passa um valor de espaço reservado como um argumento. O construtor define a variável de estado **RequestMessage** para o valor de espaço reservado e esse é o valor que é retornado.

1. Para definir a variável de estado **RequestMessage** e consultar o valor, execute os scripts **sendrequest.js** e **getmessage.js** novamente.

    ![Saída do script](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** define a variável de estado **RequestMessage** para **Olá, blockchain!** e **getmessage.js** consulta o contrato quanto ao valor da variável de estado **RequestMessage** e retorna **Olá, blockchain!** .

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado no pré-requisito Início Rápido: *Criar um membro do blockchain*.

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir.
1. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um projeto do Solidity de exemplo usando o Azure Blockchain Development Kit. Você criou e implantou um contrato inteligente e, em seguida, chamou uma função por meio de uma transação em uma rede de blockchain de consórcio hospedada no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Como desenvolver aplicativos de blockchain usando o serviço Azure Blockchain](develop.md)
