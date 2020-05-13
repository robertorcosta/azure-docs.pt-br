---
title: Configurar o tempo de execução de integração do Azure-SSIS para failover do banco de dados SQL
description: Este artigo descreve como configurar o tempo de execução de integração do Azure-SSIS com a replicação geográfica do banco de dados SQL do Azure e o failover para o banco de dados SSISDB
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
ms.openlocfilehash: 795247cd0d6adfd27115b73c1d0de02e6810d670
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201134"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Configurar o tempo de execução de integração do Azure-SSIS com replicação geográfica e failover do banco de dados SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar o IR (Integration Runtime) do Azure-SSIS com a replicação geográfica do banco de dados SQL do Azure para o banco de dados SSISDB. Quando ocorre um failover, você pode garantir que o IR do Azure-SSIS continue trabalhando com o banco de dados secundário.

Para obter mais informações sobre a replicação geográfica e o failover do Banco de Dados SQL, consulte [Visão geral: grupos de replicação geográfica ativa e de failover automático](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>Failover de Azure-SSIS IR com uma instância gerenciada do banco de dados SQL

### <a name="prerequisites"></a>Pré-requisitos

Uma instância gerenciada do banco de dados SQL do Azure usa uma *DMK (chave mestra de banco de dados)* para ajudar a proteger dados, credenciais e informações de conexão armazenadas em um banco de dado. Para habilitar a descriptografia automática de DMK, uma cópia da chave é criptografada por meio da *SMK (chave mestra do servidor)*. 

O SMK não é replicado em um grupo de failover. Você precisa adicionar uma senha nas instâncias primária e secundária para descriptografia de DMK após o failover.

1. Execute o seguinte comando para SSISDB na instância primária. Esta etapa adiciona uma nova senha de criptografia.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Crie um grupo de failover em uma instância gerenciada do banco de dados SQL do Azure.

3. Execute **sp_control_dbmasterkey_password** na instância secundária usando a nova senha de criptografia.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: Azure-SSIS IR está apontando para um ponto de extremidade de ouvinte de leitura/gravação

Se você quiser que o Azure-SSIS IR aponte para um ponto de extremidade de ouvinte de leitura/gravação, você precisará apontar para o ponto de extremidade do servidor primário primeiro. Depois de colocar o SSISDB em um grupo de failover, você pode alterar para o ponto de extremidade do ouvinte de leitura/gravação e reiniciar o Azure-SSIS IR.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com a nova região, rede virtual e informações de URI de SAS (assinatura de acesso compartilhado) para a instalação personalizada na instância secundária. Como o Azure-SSIS IR está apontando para um ouvinte de leitura/gravação e o ponto de extremidade é transparente para o Azure-SSIS IR, você não precisa editar o ponto de extremidade.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: Azure-SSIS IR está apontando para um ponto de extremidade do servidor primário

Esse cenário será adequado se o Azure-SSIS IR estiver apontando para um ponto de extremidade do servidor primário.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com novas informações de região, ponto de extremidade e rede virtual para a instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>Cenário 3: Azure-SSIS IR está apontando para um ponto de extremidade público de uma instância gerenciada do banco de dados SQL

Esse cenário será adequado se o Azure-SSIS IR estiver apontando para um ponto de extremidade público de uma instância gerenciada do banco de dados SQL do Azure e não ingressar em uma rede virtual. A única diferença do cenário 2 é que você não precisa editar informações de rede virtual para o Azure-SSIS IR após o failover.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com as novas informações de região e ponto de extremidade da instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 4: anexar uma instância SSISDB existente (catálogo do SSIS) a uma nova Azure-SSIS IR

Esse cenário é adequado se você quiser que o SSISDB trabalhe com um novo Azure-SSIS IR em uma nova região quando ocorrer um desastre Azure Data Factory ou Azure-SSIS IR na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer o failover, execute as etapas a seguir.

> [!NOTE]
> Use o PowerShell para a etapa 4 (criação do IR). Caso contrário, o portal do Azure relatará um erro informando que o SSISDB já existe.

1. Pare o Azure-SSIS IR na região primária.

2. Execute um procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de ** \< new_data_factory_name \> ** e ** \< new_integration_runtime_name \> **.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Crie um novo data factory chamado ** \< new_data_factory_name \> ** na nova região.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Para obter mais informações sobre esse comando do PowerShell, consulte [criar um data Factory do Azure usando o PowerShell](quickstart-create-data-factory-powershell.md).

4. Crie um novo Azure-SSIS IR chamado ** \< new_integration_runtime_name \> ** na nova região usando Azure PowerShell.

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

    Para obter mais informações sobre esse comando do PowerShell, consulte [criar o tempo de execução de integração do Azure-SSIS no Azure data Factory](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Failover de Azure-SSIS IR com o banco de dados SQL

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: Azure-SSIS IR está apontando para um ponto de extremidade de ouvinte de leitura/gravação

Esse cenário é adequado quando:

- O Azure-SSIS IR está apontando para o ponto de extremidade do ouvinte de leitura/gravação do grupo de failover.
- O servidor do banco de dados SQL *não* está configurado com a regra para o ponto de extremidade do serviço de rede virtual.

Se você quiser que o Azure-SSIS IR aponte para um ponto de extremidade de ouvinte de leitura/gravação, você precisará apontar para o ponto de extremidade do servidor primário primeiro. Depois de colocar o SSISDB em um grupo de failover, você pode alterar para um ponto de extremidade de ouvinte de leitura/gravação e reiniciar o Azure-SSIS IR.

#### <a name="solution"></a>Solução

Quando ocorre o failover, ele é transparente para o Azure-SSIS IR. O Azure-SSIS IR conecta-se automaticamente ao novo primário do grupo de failover. 

Se desejar atualizar a região ou outras informações na Azure-SSIS IR, você poderá interrompê-la, editá-la e reiniciá-la.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: Azure-SSIS IR está apontando para um ponto de extremidade do servidor primário

Esse cenário será adequado se o Azure-SSIS IR estiver apontando para um ponto de extremidade do servidor primário.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com novas informações de região, ponto de extremidade e rede virtual para a instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3: anexar um SSISDB existente (catálogo do SSIS) a um novo Azure-SSIS IR

Esse cenário é adequado se você quiser provisionar um novo Azure-SSIS IR em uma região secundária. Também é adequado se você quiser que o SSISDB continue trabalhando com um novo Azure-SSIS IR em uma nova região quando ocorrer um desastre Azure Data Factory ou Azure-SSIS IR na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer o failover, execute as etapas a seguir.

> [!NOTE]
> Use o PowerShell para a etapa 4 (criação do IR). Caso contrário, o portal do Azure relatará um erro informando que o SSISDB já existe.

1. Pare o Azure-SSIS IR na região primária.

2. Execute um procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de ** \< new_data_factory_name \> ** e ** \< new_integration_runtime_name \> **.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Crie um novo data factory chamado ** \< new_data_factory_name \> ** na nova região.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Para obter mais informações sobre esse comando do PowerShell, consulte [criar um data Factory do Azure usando o PowerShell](quickstart-create-data-factory-powershell.md).

4. Crie um novo Azure-SSIS IR chamado ** \< new_integration_runtime_name \> ** na nova região usando Azure PowerShell.

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

    Para obter mais informações sobre esse comando do PowerShell, consulte [criar o tempo de execução de integração do Azure-SSIS no Azure data Factory](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Próximas etapas

Considere estas outras opções de configuração para o IR do Azure-SSIS:

- [Configurar o tempo de execução de integração do Azure-SSIS para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizar configuração do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisione Enterprise Edition para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
