---
title: Diferenças do Azure Data Factory
description: Saiba como os recursos de integração de dados do Azure Synapse Analytics diferem dos Azure Data Factory
services: synapse-analytics
author: shirleywangmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: shwang
ms.reviewer: jrasnick
ms.openlocfilehash: db5d05e1a211ce14926ee4031054669fff5110d9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930204"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics versus Azure Data Factory

No Azure Synapse Analytics, os recursos de integração de dados, como pipelines Synapse e fluxos de dados, são baseados nos Azure Data Factory. Para obter mais informações, consulte [o que é Azure data Factory](../../data-factory/introduction.md). Quase todos os recursos são idênticos ou semelhantes, e a documentação é compartilhada entre os dois serviços. Este artigo realça e identifica as diferenças atuais entre Azure Data Factory e o Azure Synapse.

Para ver se um recurso ou artigo Azure Data Factory se aplica ao Azure Synapse, verifique o moniker na parte superior do artigo.

![Aplica-se ao moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Aplica-se ao moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Recursos do Azure Data Factory não planejados para o Azure Synapse

Os recursos a seguir estão disponíveis no Azure Data Factory, mas não estão planejados para o Azure Synapse.

* **Pacotes SSIS de turno e vida:** No Azure Data Factory, você tem a capacidade de migrar e deslocar pacotes SSIS usando o Integration Runtime do SSIS. O tempo de execução de integração do SSIS e a atividade executar pacote SSIS não estão disponíveis em espaços de trabalho do Synapse. 
* **Vida útil:** A vida útil é uma configuração no tempo de execução de integração do Azure que permite que o cluster do Spark no mapeamento de fluxos de dados fique *quente* por um período de tempo após a conclusão de um fluxo de dados. Esse recurso não está disponível em espaços de trabalho do Synapse.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Recursos do Azure Synapse sem suporte no Azure Data Factory

Os recursos a seguir estão disponíveis no Azure Synapse, mas não são planejados para Azure Data Factory.

* **Monitoramento de trabalho do Spark dos fluxos de dados de mapeamento:** No Synapse, o mecanismo do Spark está contido na assinatura do usuário para que os usuários possam exibir logs do Spark detalhados. Na Azure Data Factory, a execução do trabalho ocorre em um cluster Spark gerenciado por Azure Data Factory e essas informações não estão disponíveis. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory recursos que se comportam de maneira diferente no Synapse

Os recursos a seguir se comportam de maneira diferente ou não existem atualmente no Azure Synapse. 

* **Fluxos de dados Wrangling:** A atividade de fluxo de dados Wrangling só está disponível no Azure Data Factory no momento.
* **A Galeria de modelos de solução:** No Azure Data Factory, os usuários podem encontrar modelos de pipeline na Galeria de modelos de solução. Nos espaços de trabalho do Synapse, o centro de conhecimento contém um conjunto diferente de modelos, juntamente com conjuntos de dados adicionais e scripts SQL. 
* **Integração com o Azure monitor:** Os espaços de trabalho do Synapse não são integrados ao Azure Monitor como Azure Data Factory.
* **Configuração de tempo de execução de integração híbrida:** Em um espaço de trabalho do Synapse, um usuário não pode ter um IR de VNet gerenciado e um Azure IR. Esse recurso tem suporte no Azure Data Factory.
* **Compartilhamento do Integration Runtime:** Os tempos de execução de integração auto-hospedados não podem ser compartilhados entre espaços de trabalho do Synapse. Esse recurso tem suporte no Azure Data Factory.
* **Tempos de execução de integração entre regiões para fluxos de dados:** Os fluxos de dados não podem ser executados em tempos de execução de integração em regiões diferentes de um espaço de trabalho Synapse. Esse recurso tem suporte no Azure Data Factory.

## <a name="next-steps"></a>Próximas etapas

Comece a usar a integração de dados em seu espaço de trabalho do Synapse aprendendo a [incluir dados em uma conta do Azure data Lake Storage Gen2](data-integration-data-lake.md).
