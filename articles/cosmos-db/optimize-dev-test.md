---
title: Otimizar para desenvolvimento e teste no Azure Cosmos DB
description: Este artigo explica como o Azure Cosmos DB oferece várias opções para desenvolvimento e teste do serviço gratuitamente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246676"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimizar o custo de desenvolvimento e teste no Azure Cosmos DB

Este artigo descreve as diferentes opções para usar o Azure Cosmos DB para desenvolvimento e testes gratuitamente, bem como técnicas para otimizar o custo em desenvolvimento ou contas de teste.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulador do Azure Cosmos DB (versão local para download)

O [Emulador do Azure Cosmos DB](local-emulator.md) é uma versão local para download que imita o serviço de nuvem do Azure Cosmos DB. Você pode escrever e testar o código que usa as APIs do Azure Cosmos DB, mesmo se não tiver conexão à rede e sem incorrer em custos. O emulador do Azure Cosmos DB fornece um ambiente local para fins de desenvolvimento com alta fidelidade para o serviço de nuvem. Você pode desenvolver e testar seu aplicativo no local, sem criar uma assinatura do Azure. Quando estiver pronto para implantar seu aplicativo na nuvem, atualize a cadeia de conexão para se conectar ao ponto de extremidade do Azure Cosmos DB na nuvem, nenhuma outra modificação será necessária. Você também pode [configurar um pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB](tutorial-setup-ci-cd.md) no Azure DevOps para executar testes. Comece a usar visitando o artigo [Emulador do Azure Cosmos DB](local-emulator.md).

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB nível livre 
O nível livre do Azure Cosmos DB facilita a partida, o desenvolvimento e o teste de seus aplicativos ou até mesmo executar pequenas cargas de trabalho de produção gratuitamente. Quando o nível gratuito estiver ativado em uma conta, você receberá os primeiros 400 RU/s e 5 GB de armazenamento na conta gratuitamente. Você também pode criar um banco de dados de throughput compartilhado com 25 contêineres que compartilham 400 RU/s no nível do banco de dados, todos cobertos por nível livre (limite 5 bancos de dados de throughput compartilhados em uma conta de nível livre). O nível gratuito dura indefinidamente durante toda a vida da conta e vem com todos os [benefícios e recursos](introduction.md#key-benefits) de uma conta azure Cosmos DB regular, incluindo armazenamento e throughput ilimitados (RU/s), SLAs, alta disponibilidade, distribuição global turnkey em todas as regiões do Azure e muito mais. Você pode ter até uma conta de nível gratuito por assinatura do Azure e deve optar por entrar ao criar a conta. Para começar, [crie uma nova conta com nível gratuito ativado](create-cosmosdb-resources-portal.md). Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter mais detalhes. 

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente

[Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) é uma experiência gratuita que permite que você experimente com o Azure Cosmos DB na nuvem sem se inscrever em uma conta do Azure ou usar seu cartão de crédito. As contas Try Azure Cosmos DB estão disponíveis por tempo limitado, atualmente 30 dias. Você pode renová-las a qualquer momento. Experimente as contas do Azure Cosmos DB facilita a avaliação do Azure Cosmos DB, a construção e o teste de um aplicativo ou o uso dos Quickstarts ou tutoriais. Você também pode criar uma demonstração, realizar testes unitários ou até mesmo criar uma conta de várias regiões e executar um aplicativo nela sem incorrer em custos. Em uma conta Try Azure Cosmos DB, você pode ter um banco de dados de throughput compartilhado com um máximo de 25 contêineres e 20.000 RU/s de throughput, ou um contêiner com até 5000 RU/s. Para começar, consulte a página [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Conta gratuita do Azure

O Azure Cosmos DB está incluso na [conta gratuita do Azure](https://azure.microsoft.com/free), que oferece recursos e créditos do Azure gratuitamente por um determinado período de tempo. Especificamente para o Azure Cosmos DB, a conta gratuita oferece armazenamento de 5 GB e 400 RUs de taxa de transferência provisionada para o ano todo. Essa experiência permite que qualquer desenvolvedor teste facilmente os recursos do Azure Cosmos DB ou integre-os a outros serviços do Azure sem custo. Com a conta gratuita do Azure, você recebe um crédito de US$ 200,00 para gastar nos primeiros 30 dias. Você não será cobrado, mesmo se começar a usar os serviços, até que você opte por atualizar. Para começar, visite a página [Conta gratuita do Azure](https://azure.microsoft.com/free).

## <a name="use-shared-throughput-databases"></a>Use bancos de dados de throughput compartilhados

Em um [banco de dados de throughput compartilhado,](set-throughput.md#set-throughput-on-a-database)todos os contêineres dentro do banco de dados compartilham o throughput provisionado (RU/s) do banco de dados. Por exemplo, se você provisionare um banco de dados com 400 RU/s e tiver quatro contêineres, todos os quatro contêineres compartilharão os 400 RU/s. Em um ambiente de desenvolvimento ou teste, onde cada contêiner pode ser acessado com menos freqüência e, portanto, exigir menos do que o mínimo de 400 RU/s, colocar contêineres em um banco de dados de throughput compartilhado pode ajudar a otimizar o custo. 

Por exemplo, suponha que sua conta de desenvolvimento ou teste tenha quatro contêineres. Se você criar quatro contêineres com throughput dedicado (mínimo de 400 RU/s), seu RU/s total será de 1600 RU/s. Em contraste, se você criar um banco de dados de throughput compartilhado (mínimo 400 RU/s) e colocar seus contêineres lá, seu RU/s total será de apenas 400 RU/s. Em geral, bancos de dados de throughput compartilhados são ótimos para cenários onde você não precisa de throughput garantido em qualquer contêiner individual.  Saiba mais sobre [bancos de dados de throughput compartilhados.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o emulador ou as contas gratuitas do Azure Cosmos DB, leia os seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)

