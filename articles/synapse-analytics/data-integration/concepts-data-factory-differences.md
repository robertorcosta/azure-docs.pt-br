---
title: Diferenças do Azure Data Factory
description: Saiba como os recursos de integração de dados do Azure Synapse Analytics diferem dos Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91340800"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics versus Azure Data Factory

No Azure Synapse Analytics, os recursos de integração de dados, como pipelines Synapse e fluxos de dados, são baseados nos Azure Data Factory. Para obter mais informações, consulte [o que é Azure data Factory](../../data-factory/introduction.md). Quase todos os recursos são idênticos ou semelhantes, e a documentação é compartilhada entre os dois serviços. Este artigo realça e identifica as diferenças atuais entre Azure Data Factory e o Azure Synapse.

Para ver se um recurso ou artigo Azure Data Factory se aplica ao Azure Synapse, verifique o moniker na parte superior do artigo.

![Aplica-se ao moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Aplica-se ao moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Recursos do Azure Data Factory não planejados para o Azure Synapse

Os recursos a seguir estão disponíveis no Azure Data Factory, mas não estão planejados para o Azure Synapse.

* A capacidade de comparar e deslocar pacotes SSIS.
* Floco de neve como um coletor na atividade de cópia e no fluxo de dados de mapeamento.
* A configuração de vida útil do fluxo de dados de mapeamento do tempo de execução de integração do Azure.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Recursos do Azure Synapse sem suporte no Azure Data Factory

Os recursos a seguir estão disponíveis no Azure Synapse, mas não são planejados para Azure Data Factory.

* O monitoramento de trabalho do Spark dos fluxos de dados de mapeamento só está disponível no Synapse. No Synapse, o mecanismo do Spark está contido na assinatura do usuário para que os usuários possam exibir logs do Spark detalhados. Na Azure Data Factory, a execução do trabalho ocorre em um cluster Spark gerenciado por Azure Data Factory. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory recursos que se comportam de maneira diferente no Synapse

Os recursos a seguir se comportam de maneira diferente ou não existem atualmente no Azure Synapse. 

* Fluxos de dados Wrangling
* A Galeria de modelos de solução
* Integração do git e uma solução de CI/CD nativa
* Integração com o Azure monitor
* Renomeação de recursos após a publicação
* Configuração de tempo de execução de integração híbrida em um espaço de trabalho Synapse. Um usuário não pode ter um IR de VNet gerenciada e um Azure IR.
* Compartilhamento do Integration Runtime entre espaços de trabalho do Synapse

## <a name="next-steps"></a>Próximas etapas

Comece a usar a integração de dados em seu espaço de trabalho do Synapse aprendendo a [incluir dados em uma conta do Azure data Lake Storage Gen2](data-integration-data-lake.md).
