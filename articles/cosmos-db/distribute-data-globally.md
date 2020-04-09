---
title: Distribuir dados globalmente com o Azure Cosmos DB
description: Aprenda sobre a replicação geográfica em escala de planeta, vários mestres, failover e recuperação de dados usando bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados de vários modelos distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982116"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuição de dados global com o Azure Cosmos DB – visão geral

As aplicações de hoje são necessárias para serem altamente responsivas e sempre on-line. Para obter baixa latência e alta disponibilidade, instâncias desses aplicativos precisam ser implantadas em datacenters próximos aos seus usuários. Esses aplicativos geralmente são implantados em vários datacenters e são chamados de distribuídos globalmente. Os aplicativos distribuídos globalmente precisam de um banco de dados distribuído globalmente que possa replicar os dados de maneira transparente em qualquer lugar do mundo, a fim de permitir que os aplicativos operem em uma cópia dos dados próxima de seus usuários. 

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente projetado para fornecer baixa latência, escalabilidade elástica de taxa de transferência, semântica bem definida para consistência de dados e alta disponibilidade. Em suma, se o seu aplicativo precisa de tempo de resposta rápido garantido em qualquer lugar do mundo, se é necessário estar sempre on-line, e precisa de escalabilidade ilimitada e elástica de throughput e armazenamento, você deve construir seu aplicativo no Azure Cosmos DB.

É possível configurar os bancos de dados para serem distribuídos globalmente e disponíveis em qualquer uma das regiões do Azure. Para diminuir a latência, coloque os dados perto de onde seus usuários estão. A escolha das regiões requeridas depende do alcance global de sua aplicação e onde seus usuários estão localizados. Cosmos DB replica os dados de forma transparente para todas as regiões associadas à sua conta Cosmos. Ele fornece uma única imagem do sistema do banco de dados do Azure Cosmos globalmente distribuído e dos contêineres que o aplicativo pode ler e gravar localmente. 

Com o Azure Cosmos DB, você poderá adicionar ou remover as regiões associadas à sua conta a qualquer momento. Seu aplicativo não precisa ser pausado ou reimplementado para adicionar ou remover uma região. Ele continua a estar altamente disponível o tempo todo devido aos recursos de multi-homing que o serviço fornece nativamente.

![Topologia de implantação altamente disponível](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Crie aplicativos ativos globais.** Com seu novo protocolo de replicação multi-master, cada região suporta tanto gravações quanto leituras. O recurso multimestre também permite:

- Gravação elástica ilimitada e escalabilidade de leitura. 
- 99,999% de leitura e gravação de disponibilidade em todo o mundo.
- Garantia de leituras e gravações atendidas em menos de 10 milissegundos no percentil 99.

Ao usar as APIs multi-homing Azure Cosmos DB, seu aplicativo está ciente da região mais próxima e pode enviar solicitações para essa região. A região mais próxima é identificada sem alterações de configuração. À medida que você adiciona e remove regiões de e para sua conta do Azure Cosmos, seu aplicativo não precisa ser reimplantado ou pausado, ele continua a estar altamente disponível em todos os momentos.

**Crie aplicativos altamente responsivos.** Seu aplicativo pode realizar leituras e gravações quase em tempo real contra todas as regiões escolhidas para o seu banco de dados. O Azure Cosmos DB lida internamente com a replicação de dados entre regiões com garantias de nível de consistência do nível selecionado.

**Crie aplicativos altamente disponíveis.** A execução de um banco de dados em várias regiões do mundo aumenta a disponibilidade de um banco de dados. Se uma região não estiver disponível, outras regiões manipularão automaticamente as solicitações do aplicativo. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Manter a continuidade dos negócios durante paralisações regionais.** O Azure Cosmos DB dá suporte ao [failover automático](how-to-manage-database-account.md#automatic-failover) durante uma ou mais interrupções regionais. Durante uma indisponibilidade regional, o Azure Cosmos DB continuará mantendo seus SLAs de latência, disponibilidade, consistência e taxa de transferência. Para ajudar a garantir que toda a sua aplicação esteja altamente disponível, o Cosmos DB oferece uma API de failover manual para simular uma paralisação regional. Ao usar essa API, você pode executar exercícios regulares de continuidade de negócios.

**Dimensione ler e escrever throughput globalmente.** Você pode permitir que todas as regiões sejam escritas e elásticamente leituras e gravações em todo o mundo. O throughput que seu aplicativo configura em um banco de dados do Azure Cosmos ou um contêiner é garantido para ser entregue em todas as regiões associadas à sua conta do Azure Cosmos. O throughput provisionado é garantido por [SLAs apoiadas financeiramente.](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação do Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma compensação entre consistência e desempenho. Usar esses modelos de consistência permitem que você crie aplicativos distribuídos globalmente com facilidade.

## <a name="next-steps"></a><a id="Next Steps"></a>Próximas etapas

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global - nos bastidores](global-dist-under-the-hood.md)
* [Como configurar multi-master em seus aplicativos](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelos de consistência programáveis no Cosmos DB](consistency-levels.md)
* [Escolher o nível certo de coerência para o seu aplicativo](consistency-levels-choosing.md)
* [Níveis de coerência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)

