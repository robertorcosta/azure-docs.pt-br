---
title: Pools de instâncias como fazer guia (visualização)
description: Este artigo descreve como criar e gerenciar pools de instâncias do Banco de Dados Azure SQL (visualização).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299355"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Pools de instâncias do Banco de Dados SQL do Azure (pré-visualização) como fazer

Este artigo fornece detalhes sobre como criar e gerenciar [pools de instâncias](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Operações de pool de instâncias

A tabela a seguir mostra as operações disponíveis relacionadas aos pools de instâncias e sua disponibilidade no portal Azure e no PowerShell.

|Comando|Portal do Azure|PowerShell|
|:---|:---|:---|
|Criar pool de instâncias|Não|Sim|
|Pool de instâncias de atualização (número limitado de propriedades)|Não |Sim |
|Verifique o uso e as propriedades do pool de instâncias|Não|Sim |
|Excluir pool de instâncias|Não|Sim|
|Criar instância gerenciada dentro do pool de instâncias|Não|Sim|
|Atualizar o uso de recursos de instância gerenciada|Sim |Sim|
|Verifique o uso e as propriedades da instância gerenciada|Sim|Sim|
|Excluir instância gerenciada do pool|Sim|Sim|
|Criar um banco de dados em instância gerenciada colocado no pool|Sim|Sim|
|Exclua um banco de dados da instância gerenciada|Sim|Sim|

Comandos [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/) disponíveis

|Cmdlet |Descrição |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Cria um pool de instâncias do Banco de Dados SQL do Azure. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Retorna informações sobre o pool de instâncias Do Azure SQL. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Define propriedades para um pool de instâncias do Banco de Dados SQL do Azure. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Remove um pool de instâncias do Banco de Dados SQL do Azure. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Retorna informações sobre o uso do pool de instâncias Do Azure SQL. |


Para usar o PowerShell, [instale a versão mais recente do PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)e siga as instruções para [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para operações relacionadas a instâncias dentro de pools e [instâncias únicas,](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)use os comandos de instância gerenciada padrão, mas a propriedade *nome do pool de instâncias* deve ser preenchida ao usar esses comandos para uma instância em um pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Como implantar instâncias gerenciadas em pools

O processo de implantação de uma instância em um pool consiste nas duas etapas a seguir:

1. Implantação única do pool de instâncias. Esta é uma operação de longa duração, onde a duração é a mesma que a implantação de uma [única instância criada em uma sub-rede vazia](sql-database-managed-instance.md#managed-instance-management-operations).

2. Implantação de instâncias repetitivas em um pool de instâncias. O parâmetro de pool de instâncias deve ser explicitamente especificado como parte desta operação. Esta é uma operação relativamente rápida que normalmente leva até 5 minutos.

Na visualização pública, ambas as etapas são suportadas apenas usando modelos PowerShell e Resource Manager. A experiência do portal Azure não está disponível no momento.

Depois que uma instância gerenciada é implantada em um pool, você *pode* usar o portal Azure para alterar suas propriedades na página de nível de preços.


## <a name="create-an-instance-pool"></a>Criar um pool de instâncias

Para criar um pool de instâncias:

1. [Crie uma rede virtual com uma sub-rede](#create-a-virtual-network-with-a-subnet).
2. [Crie um pool de instâncias](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Criar uma rede virtual com uma sub-rede 

Para colocar vários pools de instâncias dentro da mesma rede virtual, consulte os seguintes artigos:

- [Determine o tamanho da sub-rede VNet para uma instância gerenciada do Azure SQL Database](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Crie uma nova rede virtual e uma sub-rede usando o modelo do [portal Azure](sql-database-managed-instance-create-vnet-subnet.md) ou siga as instruções para [preparar uma rede virtual existente](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Criar um pool de instâncias 

Depois de completar as etapas anteriores, você está pronto para criar um pool de instâncias.

As seguintes restrições se aplicam aos pools de instâncias:

- Apenas O Propósito Geral e o Gen5 estão disponíveis em pré-visualização pública.
- O nome da piscina pode conter apenas minúsculas, números e hífen, e não pode começar com um hífen.
- Se você quiser usar o AHB (Azure Hybrid Benefit), ele é aplicado no nível de pool de instâncias. Você pode definir o tipo de licença durante a criação do pool ou atualizá-lo a qualquer momento após a criação.

> [!IMPORTANT]
> A implantação de um pool de instâncias é uma operação de longa duração que leva aproximadamente 4,5 horas.

Para obter parâmetros de rede:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Para criar um pool de instâncias:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Como a implantação de um pool de instâncias é uma operação de longa duração, você precisa esperar até que ele seja concluído antes de executar qualquer uma das etapas a seguir neste artigo.

## <a name="create-a-managed-instance-inside-the-pool"></a>Crie uma instância gerenciada dentro da piscina 

Após a implantação bem-sucedida do pool de instâncias, é hora de criar uma instância dentro dele.

Para criar uma instância gerenciada, execute o seguinte comando:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Implantar uma instância dentro de uma piscina leva alguns minutos. Depois que a primeira instância for criada, podem ser criadas instâncias adicionais:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Criar um banco de dados dentro de uma instância 

Para criar e gerenciar bancos de dados em uma instância gerenciada que esteja dentro de um pool, use os comandos de instância única.

Para criar um banco de dados dentro de uma instância gerenciada:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Obtenha o uso de pool de instâncias 
 
Para obter uma lista de instâncias dentro de uma piscina:

```powershell
$instancePool | Get-AzSqlInstance
```


Para obter o uso de recursos do pool:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Para obter uma visão geral detalhada do uso geral do pool e instâncias dentro dele:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Para listar os bancos de dados em uma instância:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Há um limite de 100 bancos de dados por pool (não por instância).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Dimensione uma instância gerenciada dentro de uma piscina 


Depois de preencher uma instância gerenciada com bancos de dados, você pode atingir os limites de instância em relação ao armazenamento ou desempenho. Nesse caso, se o uso do pool não tiver sido excedido, você pode dimensionar sua instância.
Escalar uma instância gerenciada dentro de uma piscina é uma operação que leva alguns minutos. O pré-requisito para dimensionamento está disponível em vCores e armazenamento no nível de pool de instâncias.

Para atualizar o número de vCores e o tamanho do armazenamento:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Apenas para atualizar o tamanho do armazenamento:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Conecte-se a uma instância gerenciada dentro de uma piscina

Para conectar-se a uma instância gerenciada em um pool, são necessárias as duas etapas a seguir:

1. [Habilite o ponto final público para a instância](#enable-the-public-endpoint-for-the-instance).
2. [Adicione uma regra de entrada ao grupo de segurança de rede (NSG).](#add-an-inbound-rule-to-the-network-security-group)

Depois que ambas as etapas estiverem concluídas, você pode se conectar à instância usando um endereço de ponto final público, porta e credenciais fornecidas durante a criação da instância. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Habilite o ponto final público para a instância

Habilitar o ponto final público para uma instância pode ser feito através do portal Azure ou usando o seguinte comando PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Este parâmetro também pode ser definido durante a criação de instâncias.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Adicione uma regra de entrada ao grupo de segurança da rede 

Essa etapa pode ser feita através do portal Azure ou usando comandos PowerShell, e pode ser feita a qualquer momento após a sub-rede estar preparada para a instância gerenciada.

Para obter detalhes, consulte [Permitir tráfego de ponto final público no grupo de segurança da rede](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Mova uma instância única existente dentro de um pool de instâncias 
 
Mover instâncias dentro e fora de uma piscina é uma das limitações de visualização pública. Uma solução que pode ser usada depende da restauração point-in-time de bancos de dados de uma instância fora de um pool para uma instância que já está em um pool. 

Ambas as instâncias devem estar na mesma assinatura e região. A restauração entre região e assinatura cruzada não é suportada no momento.

Este processo tem um período de inatividade.

Para mover os bancos de dados existentes:

1. Pausar cargas de trabalho na instância gerenciada da a que você está migrando.
2. Gerar scripts para criar bancos de dados do sistema e executá-los na instância que está dentro do pool de instâncias.
3. Faça uma restauração point-in-time de cada banco de dados desde a instância única até a instância no pool.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Aponte sua aplicação para a nova instância e retome suas cargas de trabalho.

Se houver vários bancos de dados, repita o processo para cada banco de dados.


## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração do VNet, consulte [a configuração vnet de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Para uma partida rápida que cria uma instância gerenciada e restaura um banco de dados a partir de um arquivo de backup, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o DMS (Azure Database Migration Service, serviço de migração de banco de dados do Azure) para migração, consulte [a migração de instâncias gerenciadas usando o DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para obter um monitoramento avançado do desempenho do banco de dados de instância sacada gerenciada com inteligência incorporada para solução de [problemas, consulte Monitor Azure SQL Database usando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte o preço de [instância gerenciada do SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
