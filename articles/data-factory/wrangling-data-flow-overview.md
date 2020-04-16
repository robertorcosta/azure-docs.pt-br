---
title: Disputando fluxos de dados na fábrica de dados do Azure
description: Uma visão geral dos fluxos de dados de disputa na Fábrica de Dados do Azure
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408971"
---
# <a name="what-are-wrangling-data-flows"></a>O que estão disputando fluxos de dados?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

As organizações precisam fazer a preparação de dados e a disputa para uma análise precisa de dados complexos que continuam a crescer a cada dia. A preparação dos dados é necessária para que as organizações possam usar os dados em vários processos de negócios e reduzir o tempo de valorização.

A codificação dos fluxos de dados na Fábrica de Dados Do Azure permite que você faça a preparação de dados sem código na escala de nuvem iterativamente. A disputa de fluxos de dados integra-se ao [Power Query Online](https://docs.microsoft.com/power-query/) e disponibiliza as funções de Power Query M para usuários de fábrica de dados.

A disputa do fluxo de dados traduz M gerado pelo Power Query Online Mashup Editor em código de faísca para execução em escala de nuvem.

A disputa de fluxos de dados é especialmente útil para engenheiros de dados ou "integradores de dados cidadãos".

## <a name="use-cases"></a>Casos de uso

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rápida exploração e preparação interativa de dados

Vários engenheiros de dados e integradores de dados cidadãos podem explorar e preparar interativamente conjuntos de dados em escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados em lagos de dados, os usuários precisam de uma maneira eficaz de explorar e preparar conjuntos de dados. Por exemplo, você pode precisar criar um conjunto de dados que "tenha todas as informações demográficas do cliente para novos clientes desde 2017". Você não está mapeando um alvo conhecido. Você está explorando, disputando e preparando conjuntos de dados para atender a um requisito antes de publicá-lo no lago. A disputa de fluxos de dados é frequentemente usada para cenários de análise menos formais. Os conjuntos de dados preparados podem ser usados para fazer transformações e operações de aprendizado de máquina a jusante.

### <a name="code-free-agile-data-preparation"></a>Preparação ágil de dados sem código

Os integradores de dados dos cidadãos gastam mais de 60% do seu tempo procurando e preparando dados. Eles estão procurando fazê-lo de uma maneira livre de código para melhorar a produtividade operacional. Permitir que os integradores de dados dos cidadãos enriqueçam, modelem e publiquem dados usando ferramentas conhecidas como o Power Query Online de forma escalável melhora drasticamente sua produtividade. A disputa do fluxo de dados na Fábrica de Dados do Azure permite que o familiar editor de mashup saudoso do Power Query Online permita que os integradores de dados cidadãos corriquem erros rapidamente, padronhem dados e produzam dados de alta qualidade para apoiar as decisões de negócios.

### <a name="data-validation"></a>Validação de dados

Escaneie visualmente seus dados de forma livre de código para remover quaisquer outliers, anomalias e conformar-os a uma forma para análise rápida.

## <a name="supported-sources"></a>Fontes suportadas

| Conector | Formato de dados | Tipo de autenticação |
| -- | -- | --|
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | CSV | Chave de conta |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entidade de Serviço |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV | Chave da conta, diretor de serviços |
| [Banco de Dados SQL do Azure](connector-azure-sql-database.md) | - | Autenticação SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticação SQL |

## <a name="the-mashup-editor"></a>O editor de mashup

Quando você cria um fluxo de dados de disputa, todos os conjuntos de dados de origem tornam-se consultas de conjunto de dados e são colocados na pasta **ADFResource.** Por padrão, a Consulta ao Usuário apontará para a primeira consulta do conjunto de dados. Todas as transformações devem ser feitas na Consulta ao Usuário, pois as alterações nas consultas do conjunto de dados não são suportadas nem serão perpersistidas. Atualmente, não é suportado o renomeação, a adição e exclusão de consultas.

![Disputas](media/wrangling-data-flow/editor.png)

Atualmente, nem todas as funções de M do Power Query são suportadas para disputas de dados, apesar de estarem disponíveis durante a autoria. Ao construir seus fluxos de dados de disputa, você será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Para obter mais informações sobre transformações suportadas, consulte [as funções de fluxo de dados de disputa](wrangling-data-flow-functions.md).

Atualmente, a disputa do fluxo de dados só suporta a escrita em um pia.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [criar um fluxo de dados de disputa.](wrangling-data-flow-tutorial.md)