---
title: Otimizar para desenvolvimento e teste no Azure Cosmos DB
description: Este artigo explica como o Azure Cosmos DB oferece várias opções para desenvolvimento e teste do serviço gratuitamente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754890"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimizar o custo de desenvolvimento e teste no Azure Cosmos DB

Este artigo descreve as diferentes opções para usar o Azure Cosmos DB para desenvolvimento e teste gratuitamente.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulador do Azure Cosmos DB (versão local para download)

O [Emulador do Azure Cosmos DB](local-emulator.md) é uma versão local para download que imita o serviço de nuvem do Azure Cosmos DB. Você pode escrever e testar o código que usa as APIs do Azure Cosmos DB, mesmo se não tiver conexão à rede e sem incorrer em custos. O emulador do Azure Cosmos DB fornece um ambiente local para fins de desenvolvimento com alta fidelidade para o serviço de nuvem. Você pode desenvolver e testar seu aplicativo no local, sem criar uma assinatura do Azure. Quando estiver pronto para implantar seu aplicativo na nuvem, atualize a cadeia de conexão para se conectar ao ponto de extremidade do Azure Cosmos DB na nuvem, nenhuma outra modificação será necessária. Você também pode [configurar um pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB](tutorial-setup-ci-cd.md) no Azure DevOps para executar testes. Comece a usar visitando o artigo [Emulador do Azure Cosmos DB](local-emulator.md).

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente

[Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/), uma experiência gratuita que permite criar o banco de dados e as coleções, além de fazer experiências com o Azure Cosmos DB na nuvem. Você não precisa se inscrever no Azure, nem pagar nada. As contas de avaliação do Azure Cosmos DB estão disponíveis por um período limitado, no momento, 30 dias. Você pode renová-las a qualquer momento. Essas contas do Azure Cosmos DB ajudam a avaliar o programa, compilar e testar um aplicativo usando os guias de início rápido ou tutoriais. Você pode criar uma demonstração ou realizar um teste de unidade sem incorrer em custos. Ao usar as contas de avaliação gratuitas do Azure Cosmos DB, é possível avaliar os recursos Premium do Azure Cosmos DB gratuitamente, incluindo modelos de consistência, SLAs e distribuição turnkey global. Você pode criar um banco de dados com um máximo de 25 contêineres do Azure Cosmos e 10.000 RU/s de taxa de transferência. Você pode executar o aplicativo de exemplo sem se inscrever em uma conta do Azure ou usar seu cartão de crédito. Com a avaliação gratuita do Azure Cosmos DB, você pode criar uma conta do Azure Cosmos de várias regiões e executar um aplicativo nela em apenas alguns minutos. Para começar, consulte a página [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Conta gratuita do Azure

O Azure Cosmos DB está incluso na [conta gratuita do Azure](https://azure.microsoft.com/free), que oferece recursos e créditos do Azure gratuitamente por um determinado período de tempo. Especificamente para o Azure Cosmos DB, a conta gratuita oferece armazenamento de 5 GB e 400 RUs de taxa de transferência provisionada para o ano todo. Essa experiência permite que qualquer desenvolvedor teste facilmente os recursos do Azure Cosmos DB ou integre-os a outros serviços do Azure sem custo. Com a conta gratuita do Azure, você recebe um crédito de US$ 200,00 para gastar nos primeiros 30 dias. Você não será cobrado, mesmo se começar a usar os serviços, até que você opte por atualizar. Para começar, visite a página [Conta gratuita do Azure](https://azure.microsoft.com/free).

## <a name="next-steps"></a>Próximos passos

Para começar a usar o emulador ou as contas gratuitas do Azure Cosmos DB, leia os seguintes artigos:

* Saiba mais em [Otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais em [Otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)

