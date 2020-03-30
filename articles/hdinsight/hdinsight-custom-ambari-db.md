---
title: Banco de dados Personalizado Apache Ambari no Azure HDInsight
description: Aprenda a criar clusters HDInsight com seu próprio banco de dados Apache Ambari personalizado.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240169"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configure clusters HDInsight com um DB Ambari personalizado

Apache Ambari simplifica o gerenciamento e o monitoramento de um aglomerado Apache Hadoop. A Ambari fornece uma Interface do Web fácil de usar e a API REST. O Ambari está incluído nos clusters HDInsight e é usado para monitorar o cluster e fazer alterações de configuração.

Na criação normal de clusters, como descrito em outros artigos, como [Configurar clusters no HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)o Ambari é implantado em um [banco de dados S0 Azure SQL](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) que é gerenciado pelo HDInsight e não é acessível aos usuários.

O recurso Personalizado Ambari DB permite que você implante um novo cluster e configure o Ambari em um banco de dados externo que você gerencia. A implantação é feita com um modelo do Azure Resource Manager. Este recurso tem os seguintes benefícios:

- Personalização - você escolhe o tamanho e a capacidade de processamento do banco de dados. Se você tiver grandes clusters processando cargas de trabalho intensivas, um banco de dados Ambari com especificações mais baixas pode se tornar um gargalo para as operações de gerenciamento.
- Flexibilidade - você pode dimensionar o banco de dados conforme necessário para atender às suas necessidades.
- Control - você pode gerenciar backups e segurança para seu banco de dados de forma que se encaixe com os requisitos de suas organizações.

O restante deste artigo discute os seguintes pontos:

- requisitos para usar o recurso Personalizado Ambari DB
- as etapas necessárias para fornecer clusters HDInsight usando seu próprio banco de dados externo para Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Requisitos personalizados do Ambari DB

Você pode implantar um Ambari DB personalizado com todos os tipos e versões de cluster. Vários clusters não podem usar o mesmo Ambari DB.

O Ambari DB personalizado tem os seguintes outros requisitos:

- Você deve ter um servidor e banco de dados Azure SQL DB existente.
- O banco de dados que você fornece para a configuração ambari deve estar vazio. Não deve haver tabelas no esquema dbo padrão.
- O usuário usado para se conectar ao banco de dados deve ter permissões SELECT, CREATE TABLE e INSERT no banco de dados.
- Ative a opção de [Permitir acesso aos serviços do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) no servidor SQL do Azure, onde você hospedará o Ambari.
- Os endereços IP de gerenciamento do serviço HDInsight precisam ser permitidos no SQL Server. Consulte [os endereços IP de gerenciamento](hdinsight-management-ip-addresses.md) do HDInsight para obter uma lista dos endereços IP que devem ser adicionados ao firewall do servidor SQL.

Ao hospedar seu Apache Ambari DB em um banco de dados externo, lembre-se dos seguintes pontos:

- Você é responsável pelos custos adicionais do Azure SQL DB que detém a Ambari.
- Faça backup do seu Ambari DB personalizado periodicamente. O Azure SQL Database gera backups automaticamente, mas o período de tempo de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Implantar clusters com um DB Ambari personalizado

Para criar um cluster HDInsight que usa seu próprio banco de dados Ambari externo, use o [modelo personalizado Ambari DB Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Edite os `azuredeploy.parameters.json` parâmetros no para especificar informações sobre seu novo cluster e o banco de dados que irá conter Ambari.

Você pode iniciar a implantação usando o Azure CLI. Substitua pelo `<RESOURCEGROUPNAME>` grupo de recursos onde deseja implantar seu cluster.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Próximas etapas

- [Usar armazenamentos de metadados externos no Azure HDInsight](hdinsight-use-external-metadata-stores.md)