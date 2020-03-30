---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086190"
---
O Azure Data Factory é um serviço multilocatário que tem os seguintes limites padrão para garantir que as assinaturas dos clientes estejam protegidas das cargas de trabalho uns dos outros. Para elevar os limites até o máximo para sua assinatura, entre em contato com o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite padrão | Limite máximo |
| -------- | ------------- | ------------- |
| Data factories em uma assinatura do Azure | 800 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, como pipelines, conjuntos de dados, gatilhos, serviços vinculados e tempos de execução de integração, dentro de uma fábrica de dados | 5.000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total de núcleos de CPU para Runtimes de integração Azure-SSIS uma assinatura | 256 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipeline simultâneo é executado por fábrica de dados que é compartilhado entre todos os gasodutos na fábrica | 10.000  | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividade externa simultânea é executada por assinatura por [região de Runtime de Integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>As atividades externas são gerenciadas em tempo de execução de integração, mas são executadas em serviços vinculados, incluindo Databricks, procedimento armazenado, HDInsights, Web e outros.</small> | 3000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividade simultânea do Pipeline é executada por assinatura por [região de Runtime de Integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>As atividades do pipeline são executadas em tempo de execução de integração, incluindo Pesquisa, GetMetadata e Exclusão.</small>| 1000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações simultâneas de autoria por assinatura por [região de Runtime de Integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Incluindo conexão de teste, procurar lista de pastas e lista de tabelas, visualizar dados. | 200 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de integração de dados simultâneas<sup>1</sup> consumo por assinatura por [região de Execução de Integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo da região 1<sup>2</sup>: 6000<br>Grupo regional 2<sup>2</sup>: 3000<br>Grupo regional 3<sup>2</sup>: 1500 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividades máximas por gasoduto, que inclui atividades internas para contêineres | 40 | 40 |
| Número máximo de tempos de execução de integração vinculados que podem ser criados contra um único tempo de execução de integração auto-hospedado | 100 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parâmetros máximos por gasoduto | 50 | 50 |
| Itens ForEach | 100.000 | 100.000 |
| Para cada paralelismo | 20 | 50 |
| As corridas máximas enfileiradas por gasoduto | 100 | 100 |
| Caracteres por expressão | 8.192 | 8.192 |
| Intervalo mínimo de gatilho da janela de queda | 15 min | 15 min |
| Tempo máximo para a atividade do gasoduto é executado | 7 dias | 7 dias |
| Bytes por objeto para objetos de tubulação<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para conjunto de dados e objetos de serviço vinculados<sup>3</sup> | 100 KB | 2.000 KB |
| Unidades de integração de dados<sup>1</sup> por atividade de cópia executadas | 256 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gravar chamadas à API | 1.200/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ler chamadas à API | 12.500/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Consultas de monitoramento por minuto | 1,000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações CRUD de entidade por minuto | 50 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tempo máximo de sessão de depuração do fluxo de dados | 8 horas | 8 horas |
| Número simultâneo de fluxos de dados por fábrica | 50 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número simultâneo de sessões de depuração de fluxo de dados por usuário por fábrica | 3 | 3 |
| Fluxo de dados Azure IR TTL limite | 4 horas | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> A unidade de integração de dados (DIU) é usada em uma operação de cópia nuvem-em-nuvem, saiba mais sobre unidades de integração de [dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre faturamento, consulte [os preços da Fábrica de Dados Do Azure](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [O Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) está [disponível globalmente](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade com os dados, a eficiência e os custos reduzidos de saída de rede. 

| Grupo regional | Regiões | 
| -------- | ------ |
| Grupo da região 1 | Eua Central, Leste dos EUA, Leste dos EUA2, Europa Norte, Europa Ocidental, Oeste dos EUA, Oeste dos EUA 2 |
| Grupo da região 2 | Austrália Leste, Austrália Sudeste, Brasil Sul, Índia Central, Japão Leste, Norte central dos EUA, Centro-Sul dos EUA, Sudeste Asiático, Centro-Oeste dos EUA |
| Grupo 3 da região | Canadá Central, Ásia Oriental, França Central, Coréia Central, Reino Unido Sul |

<sup>3</sup> Pipeline, conjunto de dados e objetos de serviço vinculados representam um agrupamento lógico da sua carga de trabalho. Os limites para esses objetos não se relacionam com a quantidade de dados que você pode mover e processar com a Fábrica de Dados Do Azure. A Fábrica de Dados foi projetada para dimensionar para lidar com petabytes de dados.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Pipelines em um data factory |2.500 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados dentro de uma fábrica de dados |5.000 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos de tubulação<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para conjunto de dados e objetos de serviço vinculados<sup>1</sup> |100 KB |2.000 KB |
| Núcleos de cluster azure HDInsight demanda dentro de uma assinatura<sup>2</sup> |60 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimentação de dados em nuvem por atividade de cópia executam<sup>3</sup> |32 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Contagem novas tentativas de execução de atividade do pipeline |1,000 |MaxInt (32 bits) |

<sup>1</sup> Pipeline, conjunto de dados e objetos de serviço vinculados representam um agrupamento lógico da sua carga de trabalho. Os limites para esses objetos não se relacionam com a quantidade de dados que você pode mover e processar com a Fábrica de Dados Do Azure. A Fábrica de Dados foi projetada para dimensionar para lidar com petabytes de dados.

<sup>2</sup> Os núcleos sob demanda HDInsight são alocados da assinatura que contém o data factory. Como resultado, o limite anterior é o limite principal imposto pela Fábrica de Dados para núcleos HDInsight sob demanda. É diferente do limite principal associado à sua assinatura do Azure.

<sup>3</sup> A unidade de movimentação de dados em nuvem (DMU) para a versão 1 é usada em uma operação de cópia em nuvem para nuvem, saiba mais sobre unidades de movimentação de dados em [nuvem (versão 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre faturamento, consulte [os preços da Fábrica de Dados Do Azure](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre novas tentativas |1 segundo |1 segundo |
| Valor de tempo limite de nova tentativa |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem limites para chamadas à API. Você pode fazer chamadas de API em um intervalo dentro dos [limites de API do Gerenciador de Recursos do Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
