---
title: Como aumentar a resiliência
titleSuffix: Azure Machine Learning
description: Saiba como tornar seus Azure Machine Learning recursos relacionados mais resilientes a interrupções usando uma configuração de alta disponibilidade.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095849"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Aumentar a resiliência de Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como tornar seus Azure Machine Learning recursos relacionados mais resilientes usando configurações de alta disponibilidade. Os serviços do Azure que Azure Machine Learning dependem podem ser configurados para alta disponibilidade. Este artigo fornece informações sobre quais serviços podem ser configurados para alta disponibilidade e links para informações sobre como configurar esses recursos.

> [!NOTE]
> Azure Machine Learning em si não oferece uma opção de recuperação de desastre.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Entender os serviços do Azure para Azure Machine Learning

Azure Machine Learning depende de vários serviços do Azure e tem várias camadas. Alguns deles são provisionados em sua assinatura do (cliente). Você é responsável pela configuração de alta disponibilidade desses serviços. Alguns são criados em uma assinatura da Microsoft e são gerenciados pela Microsoft.

* **Infraestrutura de Azure Machine Learning**: ambiente gerenciado pela Microsoft para Azure Machine Learning espaço de trabalho.

* **Recursos associados**: recursos provisionados em sua assinatura durante a criação de Azure Machine Learning espaço de trabalho. Eles incluem o armazenamento do Azure, o Azure Key Vault, o ACR (registro de contêiner do Azure) e o app insights. Você é responsável por configurações de alta disponibilidade para esses recursos.
  * O armazenamento padrão tem dados, como modelo, dados de log de treinamento e DataSet.
  * Key Vault tem credenciais para armazenamento, ACR, armazenamentos de dados.
  * ACR tem imagem do Docker para o ambiente de treinamento e inferência.
  * O app insights é para monitoramento Azure Machine Learning.

* **Recursos de computação**: recursos que você cria após a implantação do espaço de trabalho. Por exemplo, você pode criar uma instância de computação ou um cluster de cálculo para treinar um modelo de aprendizado de máquina.
  * Cluster de computação e instância de computação: ambiente de desenvolvimento de modelo gerenciado pela Microsoft.
  * Outros recursos: são os recursos de computação que podem ser anexados a Azure Machine Learning como o AKS (serviço kubernetes do Azure), Azure Databricks, instâncias de contêiner do Azure (ACI) e HDInsight. Você é responsável pela configuração de alta disponibilidade.

* **Armazenamentos de dados adicionais**: Azure Machine Learning pode montar armazenamentos de dados adicionais, como o armazenamento do Azure, o Azure data Lake Storage e o banco de dados SQL do Azure para o treinamento.  Eles estão dentro de sua assinatura e você é responsável pela configuração de alta disponibilidade.

A tabela a seguir mostra quais serviços são gerenciados pela Microsoft, que são gerenciados por você e que são altamente disponíveis por padrão:

| Serviço | Gerenciado por | HA por padrão |
| ----- | ----- | ----- |
| **Infraestrutura de Azure Machine Learning** | Microsoft | |
| **Recursos associados** |
| Armazenamento do Azure | Você | |
| Cofre de Chave do Azure | Você | ✓ |
| Registro de Contêiner do Azure | Você | |
| Application Insights | Você | NA |
| **Recursos de computação** |
| Instância de computação | Microsoft |  |
| Cluster de computação | Microsoft |  |
| Outros recursos, como o serviço kubernetes do Azure, <br>Azure Databricks, instância de contêiner do Azure, Azure HDInsight | Você |  |
| Armazenamentos de **dados adicionais** , como o armazenamento do Azure, o Azure SQL Database,<br> Banco de dados do Azure para PostgreSQL, banco de dados do Azure para MySQL, <br>Sistema de arquivos Azure Databricks | Você | |

Use as informações no restante deste documento para aprender as ações necessárias para fazer com que cada um desses serviços seja altamente disponível.

## <a name="associated-resources"></a>Recursos associados

> [!IMPORTANT]
> Azure Machine Learning não dá suporte ao failover de conta de armazenamento padrão usando armazenamento com redundância geográfica (GRS) ou armazenamento com redundância de zona geográfica (GZRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou armazenamento com redundância de acesso de leitura de zona geográfica (RA-GZRS).

Verifique se a configuração de alta disponibilidade de cada recurso com informações abaixo.

* **Armazenamento do Azure**: para definir a configuração de alta disponibilidade, consulte [redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: ele fornece o serviço de alta disponibilidade padrão e nenhuma ação do usuário é necessária.  Consulte [Azure Key Vault disponibilidade e redundância](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Registro de contêiner do Azure**: escolha SKU Premium para replicação geográfica. Consulte [replicação geográfica no registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application insights**: não fornece configuração de alta disponibilidade. Você pode ajustar o período de retenção de dados e os detalhes em [coleta de dados, retenção e armazenamento em Application insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Recursos de computação

Verifique se a configuração de alta disponibilidade de cada recurso com a documentação abaixo.

* **Serviço kubernetes do Azure**: Veja [as práticas recomendadas para continuidade dos negócios e recuperação de desastre no AKs (serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) ) e [crie um cluster AKs (serviço kubernetes do Azure) que usa zonas de disponibilidade](https://docs.microsoft.com/azure/aks/availability-zones). Se o cluster AKS foi criado por Azure Machine Learning (usando a CLI do Studio, SDK ou ML), não há suporte para alta disponibilidade entre regiões.
* **Azure Databricks**: consulte [recuperação de desastre regional para Azure Databricks clusters](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Instância de contêiner do Azure**: o orquestrador ACI é responsável pelo failover. Consulte [instâncias de contêiner do Azure e orquestradores de contêiner](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure hdinsight**: consulte [serviços de alta disponibilidade com suporte do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Armazenamentos de dados adicionais

Verifique se a configuração de alta disponibilidade de cada recurso com a documentação abaixo.

* **Contêiner de blob do Azure/compartilhamento de arquivos do Azure/Azure data Lake Gen2**: igual ao armazenamento padrão.
* **Azure data Lake Gen1**: consulte[diretrizes de recuperação de desastre para dados em Azure data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Banco de dados SQL do Azure**: consulte [alta disponibilidade e banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Banco de dados do Azure para PostgreSQL**: consulte [conceitos de alta disponibilidade no banco de dados do Azure para PostgreSQL-servidor único](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Banco de dados do Azure para MySQL**: consulte [entender a continuidade dos negócios no banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Sistema de arquivos do databricks**: consulte [recuperação de desastre regional para clusters de Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Se você fornecer sua própria chave (chave gerenciada pelo cliente) para implantar Azure Machine Learning espaço de trabalho, Cosmos DB também será provisionado em sua assinatura. Nesse caso, você é responsável por sua alta disponibilidade. Consulte [alta disponibilidade com o Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Próximas etapas

Para implantar Azure Machine Learning com recursos associados com suas configurações de alta disponibilidade, use um [modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).