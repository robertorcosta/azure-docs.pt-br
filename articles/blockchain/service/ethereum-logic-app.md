---
title: Como usar o conector do Ethereum Blockchain com os Aplicativos Lógicos do Azure
description: Como usar o conector do Ethereum Blockchain com os Aplicativos Lógicos do Azure para disparar funções de contrato inteligente e responder a eventos de contrato inteligente.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720669"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Como usar o conector do Ethereum Blockchain com os Aplicativos Lógicos do Azure

Use o [conector do Ethereum Blockchain](https://docs.microsoft.com/connectors/blockchainethereum/) com os [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/) para executar ações de contrato inteligente e responder a eventos de contrato inteligente. Por exemplo, você deseja criar um microsserviço baseado em REST que retorna informações de um razão do blockchain. Usando um aplicativo lógico, você pode aceitar solicitações HTTP que consultam informações armazenadas em um razão do blockchain.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o pré-requisito opcional [Início Rápido: usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md). O início rápido orienta você pela instalação do [Azure Blockchain Development Kit para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e pela configuração do ambiente de desenvolvimento de blockchain.

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Os Aplicativos Lógicos do Azure ajudam você a agendar e automatizar processos empresariais e fluxos de trabalho quando precisar integrar sistemas e serviços. Primeiro, crie uma lógica que usa o conector do Ethereum Blockchain.

1. No [portal do Azure](https://portal.azure.com), escolha **Criar um recurso > Integração > Aplicativo Lógico**.
1. Em **Criar aplicativo lógico**, forneça detalhes da localização em que o aplicativo lógico deverá ser criado. Quando terminar, selecione **Criar**.

    Para obter mais informações sobre como criar Aplicativos Lógicos do Azure, confira [Criar Aplicativos Lógicos do Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois que o Azure implantar seu aplicativo, selecione o recurso do aplicativo lógico.
1. No Designer de Aplicativos Lógicos, em **Modelos**, escolha **Aplicativo Lógico em Branco**.

Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho.

O conector do Ethereum Blockchain tem um gatilho e várias ações. O gatilho ou a ação que você usará depende do cenário.

Escolha uma das seguintes seções se o fluxo de trabalho:

* É disparado quando um evento ocorre no blockchain, [use o gatilho de evento](#use-the-event-trigger).
* Consulta ou implanta um contrato inteligente, [use ações](#use-actions).
* Segue um cenário comum, [gere um fluxo de trabalho usando o kit de desenvolvedor](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Usar o gatilho de evento

Use gatilhos de evento do Ethereum Blockchain quando desejar que um aplicativo lógico seja executado após um evento de contrato inteligente. Por exemplo, você deseja enviar um email quando uma função de contrato inteligente é chamada.

1. No Designer de Aplicativo Lógico, escolha o conector do Ethereum Blockchain.
1. Na guia **Disparar**, escolha **Quando um evento de contrato inteligente ocorrer**.
1. Altere ou [crie uma conexão de API](#create-an-api-connection) para o Azure Blockchain Service.
1. Insira os detalhes sobre o contrato inteligente que deseja verificar em busca de eventos.

    ![Propriedades do gatilho de evento](./media/ethereum-logic-app/event-properties.png)

    | Propriedade | DESCRIÇÃO |
    |----------|-------------|
    | **ABI do Contrato** | O ABI (interface binária de aplicativo) do contrato define as interfaces do contrato inteligente. Como [obter o ABI do contrato](#get-contract-abi). |
    | **Endereço do contrato inteligente** | O endereço do contrato é o endereço de destino do contrato inteligente no Ethereum Blockchain. Como [obter o endereço do contrato](#get-contract-address). |
    | **Nome do evento** | Escolha um evento de contrato inteligente a ser verificado. O evento dispara o aplicativo lógico. |
    | **Intervalo** e **Frequência** | Escolha a frequência com que você deseja verificar o evento. |

1. Clique em **Salvar**.

Para concluir o aplicativo lógico, você pode adicionar uma nova etapa que executa uma ação com base no gatilho de evento do Ethereum Blockchain. Por exemplo, envie um email.

## <a name="use-actions"></a>Usar ações

Use as ações do Ethereum Blockchain quando desejar que um aplicativo lógico execute uma ação no razão do blockchain. Por exemplo, você deseja criar um microsserviço baseado em REST que chama uma função de contrato inteligente quando uma solicitação HTTP é feita a um aplicativo lógico.

As ações do conector exigem um gatilho. Use uma ação de conector do Ethereum Blockchain como a próxima etapa após um gatilho. Por exemplo, um gatilho de solicitação HTTP para um microsserviço.

1. No Designer de Aplicativo Lógico, selecione **Nova etapa** após um gatilho.
1. Escolha o conector do Ethereum Blockchain.
1. Na guia **Ações**, escolha uma das ações disponíveis.

    ![Propriedades da ação](./media/ethereum-logic-app/action-properties.png)

1. Altere ou [crie uma conexão de API](#create-an-api-connection) para o Azure Blockchain Service.
1. Dependendo da ação escolhida, forneça os detalhes a seguir sobre a função de contrato inteligente.

    | Propriedade | DESCRIÇÃO |
    |----------|-------------|
    | **ABI do Contrato** | O ABI (interface binária de aplicativo) do contrato define as interfaces do contrato inteligente. Como [obter o ABI do contrato](#get-contract-abi). |
    | **Código de bytes do contrato** | O código de bytes do contrato inteligente compilado. Como [obter o código de bytes do contrato](#get-contract-bytecode). |
    | **Endereço do contrato inteligente** | O endereço do contrato é o endereço de destino do contrato inteligente no Ethereum Blockchain. Como [obter o endereço do contrato](#get-contract-address). |
    | **Nome da função de contrato inteligente** | Escolha o nome da função de contrato inteligente para a ação. A lista é populada com base nos detalhes do ABI do contrato. |

    Depois de escolher um nome de função de contrato inteligente, você poderá ver campos obrigatórios para parâmetros de função. Insira os valores ou o conteúdo dinâmico necessário para o seu cenário.

Agora você pode usar seu aplicativo lógico. Quando o evento do aplicativo lógico é disparado, a ação do Ethereum Blockchain é executada. Por exemplo, um gatilho de solicitação HTTP executa uma ação do Ethereum Blockchain para consultar um valor de estado do contrato inteligente, resultando em uma resposta HTTP que retorna o valor.

## <a name="generate-a-workflow"></a>Gerar um fluxo de trabalho

A extensão do Visual Studio Code para o Azure Blockchain Development Kit para Ethereum pode gerar fluxos de trabalho de aplicativo lógico para cenários comuns. Há quatro cenários disponíveis:

* Publicação de dados em um Banco de Dados SQL do Azure
* Publicação de eventos em uma Grade de Eventos do Azure ou um Barramento de Serviço do Azure
* Publicação de relatório
* Microsserviço baseado em REST

 O Azure Blockchain Development Kit usa o Truffle para simplificar o desenvolvimento do blockchain. Para gerar um aplicativo lógico com base em um contrato inteligente, você precisará de uma solução do Truffle para o contrato inteligente. Você também precisará de uma conexão com a rede de consórcio do Azure Blockchain Service. Para obter mais informações, confira [Início Rápido – Usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md).

Por exemplo, as seguintes etapas geram um aplicativo lógico do microsserviço baseado em REST, com base no contrato inteligente **HelloBlockchain** do início rápido:

1. Na barra lateral do gerenciador do VS Code, expanda a pasta **contracts** na solução.
1. Clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Gerar microsserviços para contratos inteligentes** no menu.

    ![Gerar um aplicativo lógico](./media/ethereum-logic-app/generate-logic-app.png)

1. Na paleta de comandos, escolha **Aplicativo Lógico**.
1. Insira o **endereço do contrato**. Para obter mais informações, confira [Como obter o endereço do contrato](#get-contract-address).
1. Escolha a assinatura do Azure e o grupo de recursos para o aplicativo lógico.

    Os arquivos de código e de configuração de aplicativos lógicos são gerados no diretório **generatedLogicApp**.

1. Exiba o diretório **generatedLogicApp/HelloBlockchain**. Há um arquivo JSON do aplicativo lógico para cada função, evento e propriedade de contrato inteligente.
1. Abra o arquivo **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** e copie o conteúdo.

    ![JSON para a propriedade RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. No aplicativo lógico, selecione **Exibição de código do aplicativo lógico**. Substitua o JSON existente pelo JSON do aplicativo lógico gerado.

    ![Substituir a configuração de aplicativos lógicos na exibição de código](./media/ethereum-logic-app/code-view.png)

1. Selecione **Designer** para alternar para a exibição de designer.
1. O aplicativo lógico inclui as etapas básicas para o cenário. No entanto, você precisa atualizar os detalhes de configuração do conector do Ethereum Blockchain.
1. Selecione a etapa **Conexões** e altere ou [crie uma conexão de API](#create-an-api-connection) com o Azure Blockchain Service.

    ![Aplicativo lógico de microsserviço](./media/ethereum-logic-app/microservice-logic-app.png)

1. Agora você pode usar seu aplicativo lógico. Para testar o microsserviço baseado em REST, emita uma solicitação HTTP POST para a URL de solicitação do aplicativo lógico. Copie a **URL HTTP POST** da etapa **Quando uma solicitação HTTP for recebida**.

    ![URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. Use o cURL para criar uma solicitação HTTP POST. Substitua o texto do espaço reservado **\<URL HTTP POST\>** pela URL da etapa anterior.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    O comando do cURL retorna uma resposta do aplicativo lógico. Nesse caso, a saída da função de contrato inteligente **RequestMessage**.

    ![Saída da propriedade RequestMessage](./media/ethereum-logic-app/curl.png)

Para obter mais informações sobre como usar o kit de desenvolvimento, confira a página [wiki do Azure Blockchain Development Kit para Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Criar uma conexão de API

Uma conexão de API com um blockchain é necessária para o conector do Ethereum Blockchain. Você pode usar o conector de API para vários aplicativos lógicos. Algumas propriedades são necessárias e outras dependem do cenário.

> [!IMPORTANT]
> Uma chave privada ou um endereço de conta e uma senha são necessários para a criação de transações em um blockchain. Apenas uma forma de autenticação é necessária. Você não precisa fornecer os detalhes da chave privada e da conta. A consulta de contratos não exige uma transação. Se você estiver usando ações que consultam o estado do contrato, a chave privada ou o endereço da conta e a senha não serão necessários.

Para configurar uma conexão com um membro do Azure Blockchain Service, a lista a seguir mostra as propriedades que podem ser necessárias, dependendo do cenário.

| Propriedade | DESCRIÇÃO |
|----------|-------------|
|**Nome da conexão** | Nome da conexão de API. Obrigatório. |
|**Ponto de extremidade RPC do Ethereum** | Endereço HTTP do nó de transação do Azure Blockchain Service. Obrigatório. Como [obter o ponto de extremidade RPC](#get-rpc-endpoint). |
|**Chave privada** | Chave privada da conta do Ethereum. A chave privada ou o endereço da conta e a senha são necessários para as transações. Como [obter a chave privada](#get-private-key). |
|**Endereço da conta** | Endereço da conta do membro do Azure Blockchain Service. A chave privada ou o endereço da conta e a senha são necessários para as transações. Como [obter o endereço da conta](#get-account-address). |
|**Senha da conta** | A senha da conta é definida quando você cria o membro. Para obter informações sobre como redefinir a senha, confira [Conta do Ethereum](consortium.md#ethereum-account).|

## <a name="get-rpc-endpoint"></a>Obter o ponto de extremidade RPC

O endereço do ponto de extremidade RPC do Azure Blockchain Service é necessário para se conectar a uma rede de blockchain. Obtenha o endereço do ponto de extremidade usando o Azure Blockchain Development Kit para Ethereum ou o portal do Azure.

**Como usar o kit de desenvolvimento:**

1. Em **Azure Blockchain Service** no Visual Studio Code, clique com o botão direito do mouse no consórcio.
1. Selecione **Copiar ponto de extremidade RPC**.

    ![Copiar ponto de extremidade RPC](./media/ethereum-logic-app/devkit-rpc.png)

    O ponto de extremidade RPC é copiado para a área de transferência.

**Como usar o portal do Azure:**

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue para o membro do serviço Azure Blockchain. Selecione **Nós de transação** e o link do nó de transação padrão.

    ![Selecionar o nó de transação padrão](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecione **Cadeias de conexão > Chaves de acesso**.
1. Copie o endereço do ponto de extremidade de **HTTPS (chave de acesso 1)** ou chave de acesso 2.

    ![Cadeia de conexão](./media/ethereum-logic-app/connection-string.png)

    O ponto de extremidade RPC é a URL HTTPS, incluindo o endereço e a chave de acesso do nó de transação do membro do Azure Blockchain Service.

## <a name="get-private-key"></a>Obter chave privada

A chave privada da conta do Ethereum pode ser usada para autenticação ao enviar uma transação para o blockchain. As chaves pública e privada da conta do Ethereum são geradas com base em um mnemônico de 12 palavras. O Azure Blockchain Development Kit para Ethereum gera um mnemônico quando você se conecta a um membro do consórcio do Azure Blockchain Service. Obtenha o endereço do ponto de extremidade usando a extensão do kit de desenvolvimento.

1. No Visual Studio Code, abra a paleta de comandos (F1).
1. Escolha **Azure Blockchain: Recuperar a chave privada**.
1. Selecione o mnemônico que você salvou ao se conectar ao membro do consórcio.

    ![Selecionar mnemônico](./media/ethereum-logic-app/private-key.png)

    A chave privada é copiada para a área de transferência.

## <a name="get-account-address"></a>Obter endereço da conta

A conta e a senha do membro podem ser usadas para autenticação ao enviar uma transação para o blockchain. A senha é definida quando você cria o membro.

1. Na portal do Azure, acesse a página Visão geral do Azure Blockchain Service.
1. Copie o endereço da **conta do membro**.

    ![Copiar a conta do membro](./media/ethereum-logic-app/member-account.png)

Para obter mais informações sobre o endereço da conta e a senha, confira [Conta do Ethereum](consortium.md#ethereum-account).

## <a name="get-contract-abi"></a>Obter o ABI do contrato

O ABI (interface binária de aplicativo) do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Obtenha o ABI do contrato usando o Azure Blockchain Development Kit para Ethereum ou por meio do arquivo de metadados do contrato do compilador Solidity.

**Como usar o kit de desenvolvimento:**

Se você usou o kit de desenvolvimento ou o Truffle para criar o contrato inteligente, use a extensão para copiar o ABI do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Escolha **Copiar ABI do contrato**.

    ![Copiar o ABI do contrato usando o DevKit](./media/ethereum-logic-app/abi-devkit.png)

    O ABI do contrato é copiado para a área de transferência.

**Como usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize a seção **abi** no arquivo JSON.
1. Copie a matriz JSON de **abi**.

    ![Seção do ABI do contrato nos metadados](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Obter código de bytes do contrato

O código de bytes do contrato é o contrato inteligente compilado executado pela máquina virtual do Ethereum. Obtenha o código de bytes do contrato usando o Azure Blockchain Development Kit para Ethereum ou por meio do compilador Solidity.

**Como usar o kit de desenvolvimento:**

Se você usou o kit de desenvolvimento ou o Truffle para criar o contrato inteligente, use a extensão para copiar o código de bytes do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **build/contracts** do projeto do Solidity.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Escolha **Copiar código de bytes do contrato**.

    ![Copiar o código de bytes do contrato usando o DevKit](./media/ethereum-logic-app/bytecode-devkit.png)

    O código de bytes do contrato é copiado para a área de transferência.

**Como usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize o elemento **bytecode** no arquivo JSON.
1. Copie o valor de **bytecode**.

    ![Copiar o código de bytes usando metadados](./media/ethereum-logic-app/bytecode-metadata.png)

**Como usar o compilador Solidity:**

Use o comando `solc --bin <smart contract>.sol` para gerar o código de bytes do contrato.

## <a name="get-contract-address"></a>Obter o endereço do contrato

O endereço do contrato é o endereço de destino do contrato inteligente no Ethereum Blockchain. Use esse endereço para enviar uma transação ou um estado de consulta de um contrato inteligente. Obtenha o endereço do contrato na saída de migração do Truffle ou no arquivo de metadados do contrato.

**Como usar a saída de migração do Truffle:**

O Truffle exibe o endereço do contrato após a implantação do contrato inteligente. Copie o **endereço do contrato** da saída.

![Endereço do contrato da saída do Truffle](./media/ethereum-logic-app/contract-address-truffle.png)

**Como usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados do contrato contido na pasta **build/contracts** do projeto do Solidity. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **.json**.
1. Localize a seção **networks** no arquivo JSON.
1. As redes privadas são identificadas por uma ID de rede de inteiro. Localize o valor do endereço na seção da rede.
1. Copie o valor de **address**.

![Endereço do contrato dos metadados](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Próximas etapas

Assista a [cenários comuns que conectam o blockchain usando os Aplicativos Lógicos do Azure](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
