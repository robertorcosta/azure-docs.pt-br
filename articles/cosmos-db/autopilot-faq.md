---
title: Perguntas frequentes sobre throughput no modo de piloto automático Azure Cosmos DB
description: Perguntas frequentes sobre o modo piloto automático para bancos de dados e contêineres do Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483304"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Perguntas frequentes sobre o throughput provisionado no modo de piloto automático Azure Cosmos DB (Preview)
Com o modo piloto automático, o Azure Cosmos DB gerenciará e dimensionará automaticamente os RU/s do seu contêiner ou banco de dados com base no uso. Este artigo responde a perguntas comumente feitas sobre o modo piloto automático. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-autopilot-mode-supported-for-all-apis"></a>O modo piloto automático é suportado para todas as APIs?
Sim, o modo piloto automático é suportado para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>O modo piloto automático é suportado para contas multi-master?
Sim, o modo piloto automático é suportado para contas multi-master. Os RU/s max estão disponíveis em cada região que é adicionada à conta Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Qual é o preço do piloto automático?
Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Azure Cosmos DB para obter detalhes. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Como habilitar piloto automático para meus contêineres ou bancos de dados?
O modo piloto automático pode ser ativado em novos contêineres e bancos de dados criados usando o portal Azure. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Existe suporte cli ou SDK para criar contêineres ou bancos de dados com modo piloto automático?
Atualmente, na versão de pré-visualização, você só pode criar recursos com o modo piloto automático a partir do portal Azure. O suporte para CLI e SDK ainda não está disponível.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Posso ativar o piloto automático em um contêiner ou banco de dados existente?
Atualmente, você pode habilitar o piloto automático em novos contêineres e bancos de dados ao criá-los. O suporte para habilitar o modo piloto automático em contêineres e bancos de dados existentes ainda não está disponível. Você pode migrar os contêineres existentes para um novo contêiner usando [a Fábrica de Dados do Azure](../data-factory/connector-azure-cosmos-db.md) ou alterar a [alimentação](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Posso desligar o modo piloto automático em um contêiner ou banco de dados?
Sim, você pode desligar o piloto automático mudando para a opção 'Manual' para o throughput provisionado. Na versão de pré-visualização, depois de mudar do modo piloto automático para o modo manual, você não pode ativar o piloto automático novamente para o mesmo recurso. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>O modo piloto automático é suportado para bancos de dados de throughput compartilhados?
Sim, o modo piloto automático é suportado para bancos de dados de throughput compartilhados. Para habilitar esse recurso, selecione o modo piloto automático e a opção **de throughput provision** ao criar o banco de dados. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Qual é o número de coletas permitidas por banco de dados de throughput compartilhado quando o piloto automático está ativado?
Para bancos de dados de throughput compartilhados com o modo piloto automático ativado, o número de coleções permitidas é: MIN(25, Max RU/s de banco de dados / 1000). Por exemplo, se o throughput máximo do banco de dados for de 20.000 RU/s, o banco de dados pode ter MIN(25, 20.000 RU/s / 1000) = 20 coleções. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção max RU/s?  
O limite de armazenamento em GB para cada RU/s máximo é: Max RU/s de banco de dados ou contêiner / 100. Por exemplo, se o máximo ru/s for de 20.000 RU/s, o recurso pode suportar 200 GB de armazenamento. Consulte o artigo [de limites do piloto automático](provision-throughput-autopilot.md#autopilot-limits) para as opções de armazenamento e RU/s max disponíveis. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontece se eu exceder o limite de armazenamento associado ao meu throughput máximo?
Se o limite de armazenamento associado ao throughput máximo do banco de dados ou do contêiner for excedido, o Azure Cosmos DB aumentará automaticamente o throughput máximo para o próximo nível mais alto que pode suportar esse nível de armazenamento. Por exemplo, suponha que um banco de dados ou contêiner esteja provisionado com a opção de throughput máximo de 4000 RU/s, que tem um limite de armazenamento de 50 GB. Se o armazenamento do recurso aumentar para 100 GB, o máximo ru/s do banco de dados ou contêiner será automaticamente aumentado para 20.000 RU/s, o que pode suportar até 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Com que rapidez o piloto automático aumentará e descerá com base em picos de tráfego?
O piloto automático aumentará instantaneamente ou reduzirá os RU/s dentro da faixa mínima e máxima de RU/s, com base no tráfego de entrada. O faturamento é feito em uma granularidade de 1 hora, onde você é cobrado pelo ru/s mais alto em uma hora específica. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Posso especificar um valor de throughput máximo personalizado (RU/s) para o modo piloto automático?
Atualmente, durante a versão de pré-visualização, você pode selecionar entre [quatro opções](provision-throughput-autopilot.md#autopilot-limits) para throughput máximo (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Posso aumentar o máximo de RU/s (passar para um nível mais alto) no banco de dados ou no contêiner? 
Sim. Na opção **'Configurações de & de Escala** para o seu contêiner' ou na opção **Dimensionar** para o seu banco de dados, você pode selecionar um RU/s máximo mais alto para piloto automático. Esta é uma operação de escala assíncrona que pode levar algum tempo para ser concluída (normalmente de 4 a 6 horas, dependendo do RU/s selecionado) à medida que o serviço disponibiliza mais recursos para suportar a escala mais alta. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Posso reduzir o máximo de RU/s (passar para um nível inferior) no banco de dados ou no contêiner?
Sim. Enquanto o armazenamento atual do banco de dados ou do contêiner estiver abaixo do limite de [armazenamento](#what-is-the-storage-limit-associated-with-each-max-rus-option) associado ao nível máximo de RU/s que você deseja reduzir, você pode reduzir o máximo RU/s para esse nível. Por exemplo, se você tiver selecionado 20.000 RU/s como ru/s max, você pode reduzir o ru/s máximo para 4000 RU/s se você tiver menos de 50 GB de armazenamento (o limite de armazenamento associado a 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre o max RU/s e as partições físicas?
Quando você selecionar pela primeira vez o MAX RU/s, o Azure Cosmos DB irá provisionar: Max RU/s / 10.000 RU/s = # de partições físicas. Cada [partição física](partition-data.md#physical-partitions) pode suportar até 10.000 RU/s e 50 GB de armazenamento. À medida que o tamanho do armazenamento cresce, o Azure Cosmos DB dividirá automaticamente as partições para adicionar mais partições físicas para lidar com o aumento de armazenamento ou aumentar o nível máximo de RU/s se o armazenamento [exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O Max RU/s do banco de dados ou contêiner é dividido uniformemente em todas as partições físicas. Assim, o throughput total que qualquer partição física pode dimensionar é: Max RU/s de banco de dados ou contêiner / # partições físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se as solicitações recebidas excederem o máximo ru/s do banco de dados ou contêiner?
Se o RU/s total consumido exceder o máximo RU/s do contêiner ou banco de dados, as solicitações que excedam o máximo ru/s serão estranguladas e retornarão um código de status 429. As solicitações que resultam em mais de 100% de aproveitamento normalizado também serão estranguladas. A utilização normalizada é definida como o máximo da utilização de RU/s em todas as partições físicas. Por exemplo, suponha que seu throughput máximo seja de 20.000 RU/s e você tenha duas partições físicas, P_1 e P_2, cada uma capaz de dimensionar para 10.000 RU/s. Em um determinado segundo, se P_1 tiver usado 6000 RUs, e P_2 8000 RUs, a utilização normalizada é MAX(6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0.8.

> [!NOTE]
> Os SDKs cliente sdks e ferramentas de importação de dados do Azure Cosmos (Azure Data Factory, biblioteca de executores em massa) retentam automaticamente em 429s, então 429s ocasionais estão bem. Um número elevado sustentado de 429s pode indicar que você precisa aumentar o máximo de RU/s ou rever sua estratégia de particionamento para uma [partição quente](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Ainda é possível ver 429s (limitação de taxa/estrangulamento) quando o piloto automático está ativado? 
Sim. É possível ver 429s em dois cenários. Primeiro, quando o RU/s total consumido exceder o máximo RU/s do contêiner ou banco de dados, o serviço irá acelerar as solicitações de acordo. 

Em segundo lugar, se houver uma partição quente, ou seja, um valor-chave de partição lógico que tenha uma quantidade desproporcionalmente maior de solicitações em comparação com outros valores-chave de partição, é possível que a partição física subjacente exceda seu orçamento RU/s. Como uma prática recomendada, para evitar partições quentes, [escolha uma boa chave de partição](partitioning-overview.md#choose-partitionkey) que resulte em uma distribuição uniforme de armazenamento e throughput. 

Por exemplo, se você selecionar a opção de throughput máximo de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro partições físicas, cada partição física pode ser escalonada automaticamente até 5000 RU/s. Se houver uma partição quente em uma determinada chave de partição lógica, você verá 429s quando a partição física subjacente em que reside excede 5000 RU/s, ou seja, excede 100% de utilização normalizada.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [ativar o piloto automático em um contêiner ou banco de dados do Azure Cosmos](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Conheça os [benefícios do piloto automático](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Saiba mais sobre [partições lógicas e físicas.](partition-data.md)
