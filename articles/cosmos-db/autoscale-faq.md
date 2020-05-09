---
title: Perguntas frequentes sobre a taxa de transferência provisionada de dimensionamento automático no Azure Cosmos DB
description: Perguntas frequentes sobre a taxa de transferência provisionada de dimensionamento automático para bancos de dados Azure Cosmos DB e contêineres
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791775"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Perguntas frequentes sobre a taxa de transferência provisionada de dimensionamento automático no Azure Cosmos DB

Com a taxa de transferência provisionada de dimensionamento automático, Azure Cosmos DB gerenciará e dimensionará automaticamente o RU/s do seu contêiner ou banco de dados com base no uso. Este artigo responde às perguntas mais frequentes sobre o dimensionamento automático.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-autoscale-supported-for-all-apis"></a>O dimensionamento automático tem suporte para todas as APIs?
Sim, o dimensionamento automático tem suporte para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para MongoDB.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>O dimensionamento automático tem suporte para contas de vários mestres?
Sim, o dimensionamento automático tem suporte para contas de vários mestres. O máximo de RU/s está disponível em cada região que é adicionada à conta Cosmos. 

### <a name="what-is-the-pricing-for-autoscale-"></a>Qual é o preço do dimensionamento automático?
Consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Azure Cosmos DB para obter detalhes. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Como fazer habilitar o dimensionamento automático para meus contêineres ou bancos de dados?
O dimensionamento automático pode ser habilitado em novos contêineres e bancos de dados criados usando o portal do Azure.

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>Há suporte para CLI ou SDK para criar contêineres ou bancos de dados com taxa de transferência provisionada de dimensionamento automático?
No momento, você só pode criar recursos com a taxa de transferência provisionada de dimensionamento automático do portal do Azure. O suporte para CLI e SDK ainda não está disponível.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Posso habilitar o dimensionamento automático em um contêiner ou banco de dados existente?
No momento, você pode habilitar o dimensionamento automático em novos contêineres e bancos de dados ao criá-los. O suporte para habilitar o dimensionamento automático em contêineres e bancos de dados existentes ainda não está disponível. Você pode migrar os contêineres existentes para um novo contêiner usando [Azure data Factory](../data-factory/connector-azure-cosmos-db.md) ou o [feed de alterações](change-feed.md). 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>Posso desativar o dimensionamento automático em um contêiner ou banco de dados?
Sim, você pode desativar o dimensionamento automático alternando para a opção ' padrão (manual) ' para a taxa de transferência provisionada. Na versão atual, depois de alternar do dimensionamento automático para a taxa de transferência provisionada padrão, você não pode habilitar o dimensionamento automático novamente para o mesmo recurso. 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Há suporte para dimensionamento automático para bancos de dados de taxa de transferência compartilhada?
Sim, o dimensionamento automático tem suporte para bancos de dados de taxa de transferência compartilhada. Para habilitar esse recurso, selecione dimensionamento automático e a opção **provisionar taxa de transferência** ao criar o banco de dados. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Qual é o número de coleções permitidas por banco de dados de taxa de transferência compartilhada quando o dimensionamento automático está habilitado?
Para bancos de dados de taxa de transferência compartilhada com dimensionamento automático habilitado, o número de coleções permitidas é: MIN (25, Max RU/s do banco de dados/1000). Por exemplo, se a taxa de transferência máxima do banco de dados for de 20.000 RU/s, o banco de dados poderá ter MIN (25, 20.000 RU/s/1000) = 20 coleções. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção máxima de RU/s?  
O limite de armazenamento em GB para cada máximo de RU/s é: máximo RU/s do banco de dados ou contêiner/100. Por exemplo, se o máximo de RU/s for 20.000 RU/s, o recurso poderá dar suporte a 200 GB de armazenamento. Consulte o artigo [limites de dimensionamento automático](provision-throughput-autoscale.md#autoscale-limits) para obter as opções máximas de ru/s e armazenamento disponíveis. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontecerá se eu exceder o limite de armazenamento associado à minha taxa de transferência máxima?
Se o limite de armazenamento associado à taxa de transferência máxima do banco de dados ou contêiner for excedido, Azure Cosmos DB aumentará automaticamente a taxa de transferência máxima para a próxima camada mais alta que pode dar suporte a esse nível de armazenamento. Por exemplo, suponha que um banco de dados ou contêiner seja provisionado com a opção de taxa de transferência máxima de 4000 RU/s, que tem um limite de armazenamento de 50 GB. Se o armazenamento do recurso aumentar para 100 GB, o máximo de RU/s do banco de dados ou do contêiner será aumentado automaticamente para 20.000 RU/s, que pode dar suporte a até 200 GB. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Com que rapidez o dimensionamento automático será feito com base nos picos de tráfego?
Com o dimensionamento automático, você pode escalar ou reduzir verticalmente as RU/s no intervalo mínimo e máximo de RU/s, com base no tráfego de entrada. A cobrança é feita a uma granularidade de 1 hora, em que você é cobrado pelas RU/s mais altas em uma hora específica.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>Posso especificar um valor de taxa de transferência máxima (RU/s) personalizado para dimensionamento automático?
No momento, você pode selecionar entre [quatro opções](provision-throughput-autoscale.md#autoscale-limits) para taxa de transferência máxima (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Posso aumentar o máximo de RU/s (mover para uma camada superior) no banco de dados ou no contêiner? 
Sim. Na opção **dimensionar & configurações** para seu contêiner ou opção de **escala** para seu banco de dados, você pode selecionar um máximo de ru/s mais alto para dimensionamento automático. Essa é uma operação de expansão assíncrona que pode levar algum tempo para ser concluída (geralmente 4-6 horas, dependendo do RU/s selecionado), pois o serviço provisiona mais recursos para dar suporte à escala mais alta. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Posso reduzir o máximo de RU/s (mover para uma camada inferior) no banco de dados ou no contêiner?
Sim. Contanto que o armazenamento atual do banco de dados ou do contêiner esteja abaixo do [limite de armazenamento](#what-is-the-storage-limit-associated-with-each-max-rus-option) associado à camada máxima de ru/s para a qual você deseja reduzir, reduza o máximo de ru/s para essa camada. Por exemplo, se você selecionou 20.000 RU/s como o máximo de RU/s, poderá reduzir verticalmente o máximo de RU/s para 4000 RU/s se tiver menos de 50 GB de armazenamento (o limite de armazenamento associado a 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre as partições máximas RU/s e físicas?
Quando você seleciona primeiro o máximo de RU/s, o Azure Cosmos DB provisionar: Max RU/s/10.000 RU/s = n º de partições físicas. Cada [partição física](partition-data.md#physical-partitions) pode dar suporte a até 10.000 ru/s e armazenamento de 50 GB. À medida que o tamanho do armazenamento cresce, Azure Cosmos DB irá dividir automaticamente as partições para adicionar mais partições físicas para lidar com o aumento de armazenamento ou aumentar a camada máxima de RU/s se [o armazenamento exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O máximo de RU/s do banco de dados ou contêiner é dividido uniformemente em todas as partições físicas. Assim, a taxa de transferência total que qualquer partição física pode dimensionar é: máximo RU/s do banco de dados ou do contêiner/# partições físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se as solicitações de entrada excederem o máximo de RU/s do banco de dados ou do contêiner?
Se os RU/s consumidos geral excederem o máximo de RU/s do contêiner ou banco de dados, as solicitações que excederem o máximo de RU/s serão limitadas e retornarão um código de status 429. As solicitações que resultam em mais de 100% de utilização normalizada também serão limitadas. A utilização normalizada é definida como o máximo da utilização de RU/s em todas as partições físicas. Por exemplo, suponha que a taxa de transferência máxima seja de 20.000 RU/s e que você tenha duas partições físicas, P_1 e P_2, cada uma capaz de dimensionar para 10.000 RU/s. Em um determinado segundo, se P_1 tiver usado 6000 RUs e P_2 8000 RUs, a utilização normalizada será máxima (6000 RU/10.000 RU, 8000 RU/10.000 RU) = 0,8.

> [!NOTE]
> Os SDKs de cliente do Azure Cosmos DB e as ferramentas de importação de dados (Azure Data Factory, biblioteca de executores em massa) tentam automaticamente novamente em 429s, para que 429s ocasionais sejam bem. Um alto número de 429s sustentado pode indicar que você precisa aumentar o máximo de RU/s ou examinar sua estratégia de particionamento para uma [partição ativa](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Ainda é possível ver 429s (limitação/limitação de taxa) quando o dimensionamento automático está habilitado? 
Sim. É possível ver 429s em dois cenários. Primeiro, quando as RU/s consumidas geral excederem o máximo de RU/s do contêiner ou do banco de dados, o serviço limitará as solicitações de acordo. 

Em segundo lugar, se houver uma partição ativa, ou seja, um valor de chave de partição lógica que tenha uma quantidade desproporcionalmente maior de solicitações comparada a outros valores de chave de partição, é possível que a partição física subjacente exceda seu orçamento de RU/s. Como prática recomendada, para evitar partições ativas, [escolha uma boa chave de partição](partitioning-overview.md#choose-partitionkey) que resulte em uma distribuição uniforme do armazenamento e da taxa de transferência. 

Por exemplo, se você selecionar a opção de taxa de transferência máxima de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro partições físicas, cada partição física poderá ser dimensionada para até 5000 RU/s. Se houver uma partição ativa em uma chave de partição lógica específica, você verá 429s quando a partição física subjacente na qual ela reside excede 5000 RU/s, ou seja, exceder 100% de utilização normalizada.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [habilitar o dimensionamento automático em um contêiner ou banco de dados Cosmos do Azure](provision-throughput-autoscale.md#create-db-container-autoscale).
* Saiba mais sobre os [benefícios da taxa de transferência provisionada com dimensionamento automático ](provision-throughput-autoscale.md#autoscale-benefits).
* Saiba mais sobre [partições lógicas e físicas](partition-data.md).
