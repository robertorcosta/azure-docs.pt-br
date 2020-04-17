---
title: Solucionando falhas de criação de clusters com o Azure HDInsight
description: Saiba como solucionar problemas de criação de clusterapache para o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: 3af7515995a305f41fb9b9f85deb9107de51c622
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453482"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Solucionando falhas de criação de clusters com o Azure HDInsight

Os seguintes problemas são as causas básicas mais comuns para falhas de criação de clusters:

- Problemas de permissão
- Restrições de políticas de recursos
- Firewalls
- Bloqueios de recurso
- Versões de componentes sem suporte
- Restrições ao nome da conta de armazenamento
- Paralisações de serviços

## <a name="permissions-issues"></a>Problemas de permissões

Se você estiver usando o Azure Data Lake `AmbariClusterCreationFailedErrorCode`Storage:::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support.":::Gen2 e receber o erro : " ", abra o portal Dozure, vá para sua conta de armazenamento e, sob controle de acesso (IAM), certifique-se de que o **Storage Blob Data Contributor** ou a função Storage **Blob Data Owner** tenha atribuído acesso à identidade **gerenciada atribuída ao Usuário** para a assinatura. Consulte [Configurar permissões para a identidade gerenciada na conta de data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) para obter instruções detalhadas.

Se você estiver usando o Azure Data Lake Storage Gen1, consulte instruções de configuração e configuração [aqui](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 não é suportado para clusters HBase e não é suportado na versão 4.0 do HDInsight.

Se estiver usando o Azure Storage, certifique-se de que o nome da conta de armazenamento seja válido durante a criação do cluster.

## <a name="resource-policy-restrictions"></a>Restrições de políticas de recursos

As políticas do Azure baseadas em assinatura podem negar a criação de endereços IP públicos. A criação do cluster HDInsight exige dois IPs públicos.  

Em geral, as seguintes políticas podem impactar a criação de clusters:

* Políticas que impeçam a criação de balanceadores de & de endereço IP dentro da assinatura.
* Política que impede a criação de conta de armazenamento.
* Política que impede a exclusão de recursos de rede (Endereço IP /Balanceadores de carga).

## <a name="firewalls"></a>Firewalls

Firewalls em sua rede virtual ou conta de armazenamento podem negar a comunicação com endereços IP de gerenciamento hdInsight.

Permitir o tráfego a partir dos endereços IP na tabela abaixo.

| Endereço IP de origem | Destino | Direção |
|---|---|---|
| 168.61.49.99 | *:443 | Entrada |
| 23.99.5.239 | *:443 | Entrada |
| 168.61.48.131 | *:443 | Entrada |
| 138.91.141.162 | *:443 | Entrada |

Adicione também os endereços IP específicos para a região onde o cluster é criado. Consulte [os endereços IP de gerenciamento do HDInsight](../hdinsight-management-ip-addresses.md) para obter uma listagem dos endereços de cada região do Azure.

Se você estiver usando uma rota expressa ou seu próprio servidor DNS personalizado, consulte [Planejar uma rede virtual para o Azure HDInsight - conectando várias redes](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Bloqueios de recursos  

Certifique-se de que não há [bloqueios em sua rede virtual e grupo de recursos](../../azure-resource-manager/management/lock-resources.md). Os clusters não podem ser criados ou excluídos se o grupo de recursos estiver bloqueado. 

## <a name="unsupported-component-versions"></a>Versões de componentes sem suporte

Certifique-se de que você está usando uma [versão suportada do Azure HDInsight](../hdinsight-component-versioning.md) e quaisquer [componentes Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) em sua solução.  

## <a name="storage-account-name-restrictions"></a>Restrições ao nome da conta de armazenamento

Os nomes das contas de armazenamento não podem ter mais de 24 caracteres e não podem conter um caractere especial. Essas restrições também se aplicam ao nome do contêiner padrão na conta de armazenamento.

Outras restrições de nomeação também se aplicam à criação de clusters. Consulte [as restrições de nome do cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)para obter mais informações.

## <a name="service-outages"></a>Paralisações de serviços

Verifique [o status do Azure](https://status.azure.com) para verificar eventuais paralisações ou problemas de serviço.

## <a name="next-steps"></a>Próximas etapas

* [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Use o Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Usar o Armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
