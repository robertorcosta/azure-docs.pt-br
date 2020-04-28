---
title: Configurar Azure-SSIS Integration Runtime para failover do banco de dados SQL
description: Este artigo descreve como configurar o Azure-SSIS Integration Runtime com a replicação geográfica e o failover do Banco de Dados SQL do Azure para o banco de dados SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188715"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurar o Azure-SSIS Integration Runtime com a replicação geográfica e o failover do Banco de Dados SQL do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como configurar a replicação geográfica do Azure-SSIS Integration Runtime com o Banco de Dados SQL do Azure para o banco de dados SSISDB. Quando ocorre um failover, você pode garantir que o IR do Azure-SSIS continue trabalhando com o banco de dados secundário.

Para obter mais informações sobre a replicação geográfica e o failover do Banco de Dados SQL, consulte [Visão geral: grupos de replicação geográfica ativa e de failover automático](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Failover de Azure-SSIS IR com Instância Gerenciada do Banco de Dados SQL do Azure

### <a name="prerequisites"></a>Pré-requisitos
1. Execute o comando abaixo no SSISDB na instância primária. Esta etapa está adicionando uma nova senha de criptografia.
```sql
  ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
```

2. Criar grupo de failover em Instância Gerenciada do Banco de Dados SQL do Azure.

3. Execute **sp_control_dbmasterkey_password** na instância secundária, usando a nova senha de criptografia.
```sql
  EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
    @password = N'<password>', @action = N'add';  
  GO
```

### <a name="solution"></a>Solução
Quando ocorrer um failover, se você quiser usar Azure-SSIS IR existentes na região primária:
1. Parar Azure-SSIS IR na região primária.

2. Edite Azure-SSIS IR com novas informações de região, ponto de extremidade e VNET da instância secundária.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Reinicie Azure-SSIS IR.

4. Altere o nome do servidor em **ConnectionManager** dos pacotes do SSIS com o nome do servidor de instância secundária e reimplante esses pacotes e execute.

Se você quiser provisionar um novo Azure-SSIS IR na região secundária:
> [!NOTE]
> A etapa 4 (criação de IR) precisa ser feita por meio do PowerShell. Portal do Azure relatará um erro informando que o SSISDB já existe.
1. Parar Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Crie um novo data Factory chamado ** \<new_data_factory_name\> ** na nova região. Para obter mais informações, consulte criar um data factory.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
```
  Para obter mais informações sobre esse comando do PowerShell, consulte [criar um data Factory do Azure usando o PowerShell](quickstart-create-data-factory-powershell.md)

4. Crie um novo Azure-SSIS ir chamado ** \<new_integration_runtime_name\> ** na nova região usando Azure PowerShell.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
```

  Para obter mais informações sobre esse comando do PowerShell, consulte [criar o runtime de integração do Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md)

5. Altere o nome do servidor em **ConnectionManager** dos pacotes do SSIS com o nome do servidor de instância secundária e reimplante esses pacotes e execute.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Failover de Azure-SSIS IR com o banco de dados SQL do Azure

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - IR Azure-SSIS está apontando para o ponto de extremidade do ouvinte de leitura / gravação

#### <a name="conditions"></a>Condições

Esta seção se aplica quando as seguintes condições forem verdadeiras:

- A prefeitura diz que "os principais problemas de saúde são os seguintes:

  AND

- O servidor do banco de dados SQL está *não* configurado com a regra de ponto de extremidade de serviço de rede virtual.

#### <a name="solution"></a>Solução

Quando ocorre um failover, ele é transparente para o IR do Azure-SSIS. O Azure-SSIS IR conecta-se automaticamente ao novo primário do grupo de failover.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - o Azure-SSIS IR está apontando para o terminal principal do servidor

#### <a name="conditions"></a>Condições

Esta seção se aplica quando uma das seguintes condições for verdadeira:

- O IR do Azure-SSIS está apontando para o endpoint do servidor principal do grupo de failover. Esse ponto de extremidade é alterado quando ocorre um failover.

  OU

- O servidor do Banco de Dados SQL do Azure é configurado com a regra de ponto de extremidade de serviço de rede virtual.


#### <a name="solution"></a>Solução

1. Parar Azure-SSIS IR na região primária.

2. Edite Azure-SSIS IR com novas informações de região, ponto de extremidade e VNET da instância secundária.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Reinicie Azure-SSIS IR.

4. Altere o nome do servidor em **ConnectionManager** dos pacotes do SSIS com o nome do servidor de instância secundária e reimplante esses pacotes e execute.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 – anexando um SSISDB existente (catálogo do SSIS) a um novo Azure-SSIS IR

Quando um ADF ou Azure-SSIS IR desastre ocorre na região atual, você pode fazer com que o SSISDB continue trabalhando com uma nova Azure-SSIS IR em uma nova região.

#### <a name="solution"></a>Solução

> [!NOTE]
> A etapa 4 (criação de IR) precisa ser feita por meio do PowerShell. Portal do Azure relatará um erro informando que o SSISDB já existe.

1. Parar Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Crie um novo data Factory chamado ** \<new_data_factory_name\> ** na nova região. Para obter mais informações, consulte criar um data factory.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
```
  Para obter mais informações sobre esse comando do PowerShell, consulte [criar um data Factory do Azure usando o PowerShell](quickstart-create-data-factory-powershell.md)

4. Crie um novo Azure-SSIS ir chamado ** \<new_integration_runtime_name\> ** na nova região usando Azure PowerShell.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
```

  Para obter mais informações sobre esse comando do PowerShell, consulte [criar o runtime de integração do Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md)

5. Altere o nome do servidor em **ConnectionManager** dos pacotes do SSIS com o nome do servidor de instância secundária e reimplante esses pacotes e execute.


## <a name="next-steps"></a>Próximas etapas

Considere estas outras opções de configuração para o IR do Azure-SSIS:

- [Configurar o Azure-SSIS Integration Runtime para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizar configuração do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisionar a Enterprise Edition para o Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
