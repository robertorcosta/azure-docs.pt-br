---
title: Visão geral do desenvolvimento do Azure Blockchain Service
description: Introdução no desenvolvimento de soluções no Azure Blockchain Service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348374"
---
# <a name="azure-blockchain-service-development-overview"></a>Visão geral do desenvolvimento do Azure Blockchain Service

Com o Azure Blockchain Service, você pode criar redes blockchain de consórcio para permitir cenários corporativos como rastreamento de ativos, token digital, lealdade e recompensa, cadeia de suprimentos financeira e procedência. As seções a seguir introduzem o desenvolvimento do Azure Blockchain Service para implementar soluções corporativas de blockchain.

## <a name="connecting-to-azure-blockchain-service"></a>Conectando-se ao Serviço blockchain do Azure

Existem diferentes tipos de clientes para redes blockchain, incluindo nós completos, nós leves e clientes remotos. O Azure Blockchain Service constrói uma rede blockchain que inclui nomes. Você pode usar diferentes clientes como sua porta de entrada para o Azure Blockchain Service para o desenvolvimento de blockchain. O Azure Blockchain Service oferece autenticação básica ou chave de acesso como ponto final de desenvolvimento. A seguir estão clientes populares que você pode usar connect.

### <a name="visual-studio-code"></a>Visual Studio Code

Você pode se conectar a membros do consórcio usando a extensão Azure Blockchain Development Kit Visual Studio Code. Uma vez conectado a um consórcio, você pode criar, compilar e implantar contratos inteligentes em um membro do consórcio do Azure Blockchain Service.

Para desenvolver soluções sofisticadas de blockchain corporativa, uma estrutura de desenvolvimento é necessária para se conectar a diferentes redes blockchain e gerenciar ciclos de vida de contratos inteligentes. A maioria dos projetos interage com pelo menos dois nódulos blockchain. Os desenvolvedores usam uma blockchain local durante o desenvolvimento. Quando o aplicativo está pronto para teste ou lançamento, o desenvolvedor é implantado em uma rede blockchain. Por exemplo, a principal rede pública do Ethereum ou o Azure Blockchain Service. O Azure Blockchain Development Kit para extensão Ethereum no Visual Studio Code usa truffle. Truffle é uma estrutura popular de desenvolvimento de blockchain para escrever, compilar, implantar e testar aplicativos descentralizados em blockchains Ethereum. Você também pode pensar na Truffle como uma estrutura que tenta integrar perfeitamente o desenvolvimento de contratos inteligentes e o desenvolvimento web tradicional.

Para obter mais informações, consulte [Quickstart: Use o Visual Studio Code para se conectar a uma rede de consórcio saqueador a azure Blockchain Service](connect-vscode.md).

### <a name="metamask"></a>MetaMask

MetaMask é uma carteira baseada em navegador (cliente remoto), cliente RPC e explorador de contratos básicos. Ao contrário de outras carteiras de navegador, metaMask injeta uma instância web3 no contexto JavaScript do navegador, agindo como um cliente RPC que se conecta a uma variedade de blockchains Ethereum *(mainnet,* *Ropsten testnet,* *Kovan testnet,* nó RPC local, etc.). Você pode configurar o RPC personalizado facilmente para se conectar ao Azure Blockchain Service e iniciar o desenvolvimento de blockchain usando o Remix.

Para obter mais informações, consulte [Quickstart: Use metaMask para conectar e implantar um contrato inteligente](connect-metamask.md)

### <a name="geth"></a>Geth

Geth é a interface de linha de comando para executar um nó Ethereum completo implementado no Go. Você não precisa executar o nó completo, mas pode iniciar seu console interativo que fornece um ambiente de tempo de execução JavaScript expondo uma API JavaScript para interagir com o Azure Blockchain Service.

Para obter mais informações, consulte [Quickstart: Use Geth para anexar a um nó de transação do Azure Blockchain Service](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Transações privadas do Ethereum Quorum

Quorum é um protocolo de livro-razão distribuído baseado em Ethereum com transação mais privacidade de contrato e novos mecanismos de consenso. Os principais aprimoramentos sobre o Go-Ethereum incluem:

* **Privacidade** - O Quorum apoia transações privadas e contratos privados por meio da separação pública e privada do Estado e utiliza trocas de mensagens criptografadas ponto a ponto para transferência direcionada de dados privados para participantes da rede.
* **Mecanismos de consenso alternativos** - o consenso de prova de trabalho ou de prova de participação não é necessário para uma rede perecida. Quorum oferece múltiplos mecanismos de consenso que são projetados para cadeias de consórcios como RAFT e IBFT.O Azure Blockchain Service usa o mecanismo de consenso IBFT.
* **Permissão por** pares - a permissão de nó e pares usando contratos inteligentes garante que apenas partes conhecidas possam se juntar à rede.
* **Desempenho Superior** - Quorum oferece desempenho superior ao público Geth.

## <a name="block-explorers"></a>Exploradores de bloco

Os exploradores de bloco são navegadores blockchain on-line que exibem conteúdo de blocos individuais, dados de endereço de transação e histórico. As informações básicas do bloco estão disponíveis através do Azure Monitor no Azure Blockchain Service. No entanto, se você precisar de mais informações detalhadas durante o desenvolvimento, os exploradores de bloco podem ser úteis.  Os seguintes exploradores de bloco trabalham com o Azure Blockchain Service:

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Você também pode construir seu próprio explorador de blocos usando o Blockchain Data Manager e o Azure Cosmos DB, veja [Tutorial: Use o Blockchain Data Manager para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Medição de TPS

Como o blockchain é usado em cenários mais corporativos, a velocidade das transações por segundo (TPS) é importante para evitar gargalos e ineficiências do sistema. Altas taxas de transação podem ser difíceis de manter dentro de uma blockchain descentralizada. Uma medição precisa do TPS pode ser afetada por diferentes fatores, como thread do servidor, tamanho da fila de transações, latência da rede e segurança. Se você precisar medir a velocidade do TPS durante o desenvolvimento, uma ferramenta de código aberto popular é [chainhammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Próximas etapas

Experimente um início rápido usando o Azure Blockchain Development Kit para Ethereum para anexar a um consórcio no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Usar o Visual Studio Code para se conectar ao Azure Blockchain Service](connect-vscode.md)