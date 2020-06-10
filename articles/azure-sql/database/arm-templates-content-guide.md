---
title: Modelos do Azure Resource Manager
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Use modelos do Azure Resource Manager para criar e configurar um Banco de Dados SQL do Azure e uma Instância Gerenciada de SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84023872"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Modelos do Azure Resource Manager para o Banco de Dados SQL do Azure e a Instância Gerenciada de SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Os modelos do Azure Resource Manager permitem que você defina a infraestrutura como um código e implante suas soluções na nuvem do Azure para o Banco de Dados SQL do Azure e a Instância Gerenciada de SQL do Azure.

## <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/single-database)

A tabela a seguir inclui links para modelos do Azure Resource Manager para o Banco de Dados SQL do Azure.

| |  |
|---|---|
| [Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Esse modelo do Azure Resource Manager cria um banco de dados individual no Banco de Dados SQL do Azure e configura regras de firewall de IP no nível do servidor. |
| [Servidor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Esse modelo do Azure Resource Manager cria um servidor para o Banco de Dados SQL do Azure. |
| [Pool elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Esse modelo permite que você implante um pool elástico e atribua bancos de dados a ele. |
| [Grupos de failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Esse modelo cria dois servidores, um banco de dados individual e um grupo de failover no Banco de Dados SQL do Azure.|
| [Detecção de Ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Esse modelo permite que você implante um servidor e um conjunto de bancos de dados com a Detecção de Ameaças habilitada, com um endereço de email para alertas para cada banco de dados. A Detecção de Ameaças faz parte da oferta ATP (Proteção Avançada contra Ameaças) ao SQL e fornece uma camada de segurança que responde às ameaças potenciais em servidores e bancos de dados.|
| [Auditoria no Armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Esse modelo permite que você implante um servidor com a auditoria habilitada para gravar logs de auditoria em um armazenamento de blobs. A auditoria do Banco de Dados SQL do Azure acompanha eventos do banco de dados e os grava em um log de auditoria que pode colocado em sua conta de armazenamento do Azure, no workspace do OMS ou nos Hubs de Eventos.|
| [Auditoria no Hub de Eventos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Esse modelo permite que você implante um servidor com a auditoria habilitada para gravar logs de auditoria em um Hub de Eventos existente. Para enviar eventos de auditoria para o Hub de Eventos, defina as configurações de auditoria com `State` `Enabled` e defina `IsAzureMonitorTargetEnabled` como `true`. Além disso, defina as Configurações de Diagnóstico com a categoria de logs de diagnóstico `SQLSecurityAuditEvents` no banco de dados `master` (para a auditoria no nível do servidor). A auditoria acompanha eventos do banco de dados e os grava em um log de auditoria que pode colocado na sua conta de armazenamento do Azure, no workspace do OMS ou nos Hubs de Eventos.|
| [Aplicativo Web do Azure com o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Essa amostra cria um Aplicativo Web do Azure gratuito e um banco de dados no Banco de Dados SQL do Azure o nível de serviço "Básico".|
| [Aplicativo Web do Azure e Cache Redis com o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Esse modelo cria um Aplicativo Web, um Cache Redis e um Banco de Dados SQL no mesmo grupo de recursos, além de criar duas cadeias de conexão no Aplicativo Web para o Banco de Dados SQL e o Cache Redis.|
| [Importar dados do armazenamento de blobs usando o ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Esse modelo do Azure Resource Manager cria um Azure Data Factory V2 que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL.|
| [Cluster HDInsight com um Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Esse modelo permite que você crie um cluster HDInsight, um servidor SQL lógico, um banco de dados e duas tabelas. Esse modelo é usado pelo [artigo Usar o Sqoop com o Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplicativo Lógico do Azure que executa um procedimento armazenado SQL de acordo com um agendamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Esse modelo permite que você crie um Aplicativo Lógico que executará um procedimento armazenado SQL de acordo com um agendamento. Os argumentos para o procedimento podem ser colocados na seção do corpo do modelo.|

## <a name="azure-sql-managed-instance"></a>[Instância Gerenciada do SQL do Azure](#tab/managed-instance)

A tabela a seguir inclui links para modelos do Azure Resource Manager da Instância Gerenciada de SQL do Azure.

| |  |
|---|---|
| [Instância Gerenciada de SQL em uma nova VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Esse modelo do Azure Resource Manager cria uma VNet do Azure configurada e uma Instância Gerenciada de SQL na VNet. |
| [Ambiente de rede da Instância Gerenciada de SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Essa implantação criará uma Rede Virtual do Azure configurada com duas sub-redes: uma que será dedicada para as Instâncias Gerenciadas do SQL e a outra na qual você poderá colocar outros recursos (por exemplo, VMs, ambientes do Serviço de Aplicativo etc.). Esse modelo criará um ambiente de rede configurado corretamente, em que você poderá implantar as Instâncias Gerenciadas do SQL. |
| [Instância Gerenciada de SQL com a conexão P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Essa implantação criará uma Rede Virtual do Azure com duas sub-redes `ManagedInstance` e `GatewaySubnet`. A Instância Gerenciada de SQL será implantada na sub-rede da ManagedInstance. O gateway de rede virtual será criado na sub-rede `GatewaySubnet` e configurado para a conexão VPN ponto a site. |
| [Instância Gerenciada de SQL com uma máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Essa implantação criará uma Rede Virtual do Azure com duas sub-redes `ManagedInstance` e `Management`. A Instância Gerenciada de SQL será implantada na sub-rede `ManagedInstance`. A máquina virtual com a última versão do SSMS (SQL Server Management Studio) será implantada na sub-rede `Management`. |

---
