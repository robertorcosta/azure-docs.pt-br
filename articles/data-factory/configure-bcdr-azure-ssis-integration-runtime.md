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
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592080"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurar o Azure-SSIS Integration Runtime com a replicação geográfica e o failover do Banco de Dados SQL do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar a replicação geográfica do Azure-SSIS Integration Runtime com o Banco de Dados SQL do Azure para o banco de dados SSISDB. Quando ocorre um failover, você pode garantir que o IR do Azure-SSIS continue trabalhando com o banco de dados secundário.

Para obter mais informações sobre a replicação geográfica e o failover do Banco de Dados SQL, consulte [Visão geral: grupos de replicação geográfica ativa e de failover automático](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Failover de Azure-SSIS IR com Instância Gerenciada do Banco de Dados SQL do Azure

### <a name="prerequisites"></a>Pré-requisitos

Instância Gerenciada do Banco de Dados SQL do Azure usa **DMK (chave mestra de banco de dados)** para ajudar a proteger dados, credenciais e informações de conexão armazenadas no banco de dado. Para habilitar a descriptografia automática de DMK, uma cópia da chave é criptografada usando a **SMK (chave mestra do servidor)**. Mas o SMK não é replicado no grupo de failover, portanto, você precisa adicionar uma senha adicional nas instâncias primária e secundária para a descriptografia de DMK após o failover.

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

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - IR Azure-SSIS está apontando para o ponto de extremidade do ouvinte de leitura / gravação

Se desejar Azure-SSIS IR ponto de extremidade do ouvinte de leitura/gravação, você precisará apontar para o ponto de extremidade do servidor primário primeiro. Depois de colocar o SSISDB no grupo de failover, você pode alterar para o ponto de extremidade do ouvinte de leitura/gravação e reiniciar Azure-SSIS IR.

#### <a name="solution"></a>Solução

Quando o failover ocorre, você precisa fazer o seguinte:

1. Parar Azure-SSIS IR na região primária.

2. Edite Azure-SSIS IR com a nova região, VNET e informações de URI de SAS de instalação personalizada da instância secundária. Como Azure-SSIS IR está apontando para o ouvinte de leitura/gravação e o ponto de extremidade é transparente para Azure-SSIS IR, você não precisa editar o ponto de extremidade.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reinicie Azure-SSIS IR.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - o Azure-SSIS IR está apontando para o terminal principal do servidor

O cenário será adequado se Azure-SSIS IR estiver apontando para o ponto de extremidade do servidor primário.

#### <a name="solution"></a>Solução

Quando o failover ocorre, você precisa fazer o seguinte:

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

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Cenário 3-Azure-SSIS IR está apontando para o ponto de extremidade público de Instância Gerenciada do Banco de Dados SQL do Azure

O cenário será adequado se o Azure-SSIS IR estiver apontando para o ponto de extremidade público de Instância Gerenciada do Banco de Dados SQL do Azure e não ingressar na VNET. A única diferença entre o cenário 2 e esses cenários é que você não precisa editar informações de VNET de Azure-SSIS IR após o failover.

#### <a name="solution"></a>Solução

Quando o failover ocorre, você precisa fazer o seguinte:

1. Parar Azure-SSIS IR na região primária.

2. Edite Azure-SSIS IR com novas informações de região e ponto de extremidade da instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reinicie Azure-SSIS IR.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 4-anexando um SSISDB existente (catálogo do SSIS) a um novo Azure-SSIS IR

Esse cenário é adequado se você quiser provisionar um novo Azure-SSIS IR na região secundária ou se quiser que o SSISDB continue trabalhando com uma nova Azure-SSIS IR em uma nova região quando ocorrer um desastre ou Azure-SSIS IR em uma região atual.

#### <a name="solution"></a>Solução

Quando o failover ocorre, você precisa fazer o seguinte:

> [!NOTE]
> A etapa 4 (criação de IR) precisa ser feita por meio do PowerShell. Portal do Azure relatará um erro informando que o SSISDB já existe.

1. Parar Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
    ```sql
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



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Failover de Azure-SSIS IR com o banco de dados SQL do Azure

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - IR Azure-SSIS está apontando para o ponto de extremidade do ouvinte de leitura / gravação

Esse cenário é adequado Azure-SSIS IR está apontando para o ponto de extremidade do ouvinte de leitura/gravação do grupo de failover e o servidor do banco de dados SQL *não* está configurado com a regra de ponto de extremidade de serviço de rede virtual. Se desejar Azure-SSIS IR ponto de extremidade do ouvinte de leitura/gravação, você precisará apontar para o ponto de extremidade do servidor primário primeiro. Depois de colocar o SSISDB no grupo de failover, você pode alterar para o ponto de extremidade do ouvinte de leitura/gravação e reiniciar Azure-SSIS IR.

#### <a name="solution"></a>Solução

Quando ocorre um failover, ele é transparente para o IR do Azure-SSIS. O Azure-SSIS IR conecta-se automaticamente ao novo primário do grupo de failover. Se desejar atualizar a região ou outras informações no Azure-SSIS IR, você poderá interrompê-la, editá-la e reiniciá-la.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - o Azure-SSIS IR está apontando para o terminal principal do servidor

O cenário será adequado se Azure-SSIS IR estiver apontando para o ponto de extremidade do servidor primário.

#### <a name="solution"></a>Solução

Quando o failover ocorre, você precisa fazer o seguinte:

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

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 – anexando um SSISDB existente (catálogo do SSIS) a um novo Azure-SSIS IR

Esse cenário é adequado se você quiser provisionar um novo Azure-SSIS IR na região secundária ou se quiser que o SSISDB continue trabalhando com uma nova Azure-SSIS IR em uma nova região quando ocorrer um desastre ou Azure-SSIS IR em uma região atual.

#### <a name="solution"></a>Solução

> [!NOTE]
> A etapa 4 (criação de IR) precisa ser feita por meio do PowerShell. Portal do Azure relatará um erro informando que o SSISDB já existe.

1. Parar Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
    ```sql
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


## <a name="next-steps"></a>Próximas etapas

Considere estas outras opções de configuração para o IR do Azure-SSIS:

- [Configurar o Azure-SSIS Integration Runtime para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizar configuração do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisionar a Enterprise Edition para o Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
