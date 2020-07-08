---
title: Ler consultas em réplicas
description: O banco de dados SQL do Azure fornece a capacidade de balancear a carga de cargas de trabalho somente leitura usando a capacidade de réplicas somente leitura, chamada escala horizontal de leitura.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
titleSuffix: Azure SQL Database & SQL Managed Instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: a043eb5eed8f5554e42a113a3d7963be94da8a49
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985560"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Como parte da [arquitetura de alta disponibilidade](high-availability-sla.md#premium-and-business-critical-service-tier-availability), cada banco de dados nas camadas de serviço Premium e comercialmente crítico é automaticamente provisionado com uma réplica primária e várias réplicas secundárias. As réplicas secundárias são provisionadas com o mesmo tamanho de computação que a réplica primária. O recurso de *expansão de leitura* permite balancear a carga de cargas de trabalho somente leitura do banco de dados SQL usando a capacidade de uma das réplicas somente leitura em vez de compartilhar a réplica de leitura-gravação. Dessa forma, a carga de trabalho somente leitura será isolada da carga de trabalho principal de leitura/gravação e não afetará seu desempenho. O recurso destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análise. Nas camadas de serviço Premium e Comercialmente Crítico, os aplicativos podem obter benefícios de desempenho usando essa capacidade adicional sem custo adicional.

O recurso de expansão de leitura também está disponível na camada de serviço de hiperescala quando pelo menos uma réplica secundária é criada. Várias réplicas secundárias podem ser usadas se as cargas de trabalho somente leitura exigirem mais recursos do que estão disponíveis em uma réplica secundária. A arquitetura de alta disponibilidade das camadas de serviço Basic, Standard e Uso Geral não inclui nenhuma réplica. O recurso de expansão de leitura não está disponível nessas camadas de serviço.

O diagrama a seguir ilustra isso usando um banco de dados Comercialmente Crítico.

![Réplicas somente leitura](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

O recurso de expansão de leitura é habilitado por padrão nos novos bancos de dados Premium, Comercialmente Crítico e hiperescala. Para o hiperescala, uma réplica secundária é criada por padrão para novos bancos de dados. Se a cadeia de conexão SQL estiver configurada com `ApplicationIntent=ReadOnly` , o aplicativo será redirecionado pelo gateway para uma réplica somente leitura desse banco de dados. Para obter informações sobre como usar a `ApplicationIntent` propriedade, consulte [especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se você quiser garantir que o aplicativo se conecte à réplica primária, independentemente da `ApplicationIntent` configuração na cadeia de conexão SQL, você deve desabilitar explicitamente a expansão de leitura ao criar o banco de dados ou ao alterar sua configuração. Por exemplo, se você atualizar seu banco de dados da camada Standard ou Uso Geral para a camada Premium, Comercialmente Crítico ou de hiperescala e quiser ter certeza de que todas as suas conexões continuam a ir para a réplica primária, desabilite a expansão de leitura. Para obter detalhes sobre como desabilitá-lo, consulte [habilitar e desabilitar a expansão de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Não há suporte para Repositório de Dados de Consultas, eventos estendidos e recursos do SQL Profiler nas réplicas somente leitura.

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes momentos, pode haver uma pequena latência entre diferentes réplicas. A expansão de leitura dá suporte à consistência no nível da sessão. Isso significa que, se a sessão somente leitura se reconectar após um erro de conexão causado pela indisponibilidade de réplica, ela poderá ser redirecionada para uma réplica que não seja 100% atualizada com a réplica de leitura/gravação. Da mesma forma, se um aplicativo gravar dados usando uma sessão de leitura/gravação e lê-lo imediatamente usando uma sessão somente leitura, é possível que as atualizações mais recentes não fiquem visíveis imediatamente na réplica. A latência é causada por uma operação de refazer do log de transações assíncronas.

> [!NOTE]
> As latências de replicação dentro da região são baixas e essa situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Conectar-se a uma réplica somente leitura

Quando você habilita a expansão de leitura para um banco de dados, a `ApplicationIntent` opção na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica somente leitura. Especificamente, se o `ApplicationIntent` valor for `ReadWrite` (o valor padrão), a conexão será direcionada para a réplica de leitura/gravação do banco de dados. Isso é idêntico ao comportamento existente. Se o valor de `ApplicationIntent` é `ReadOnly`, a conexão é roteada para uma réplica somente leitura.

Por exemplo, a seguinte cadeia de conexão conecta o cliente a uma réplica somente leitura (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cadeias de conexão conecta o cliente a uma réplica de leitura-gravação (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verifique se uma conexão está pronta para réplica somente leitura

Você pode verificar se você está conectado a uma réplica somente leitura ao executar a consulta a seguir. Isso retornará READ_ONLY quando você estiver conectado a uma réplica somente leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> A qualquer momento, apenas uma das réplicas AlwaysON pode ser acessada pelas sessões ReadOnly.

## <a name="monitor-and-troubleshoot-a-read-only-replica"></a>Monitorar e solucionar problemas de uma réplica somente leitura

Quando conectado a uma réplica somente leitura, você pode acessar as métricas de desempenho usando a `sys.dm_db_resource_stats` DMV. Para acessar as estatísticas do plano de consulta, use as `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` DMVs e.

> [!NOTE]
> A DMV `sys.resource_stats` no banco de dados mestre lógico retorna o uso da CPU e os dados de armazenamento da réplica primária.

## <a name="enable-and-disable-read-scale-out"></a>Habilitar e desabilitar a expansão de leitura

A expansão de leitura é habilitada por padrão nas camadas de serviço Premium, Comercialmente Crítico e hiperescala. A expansão de leitura não pode ser habilitada nas camadas de serviço Basic, Standard ou Uso Geral. A expansão de leitura é desabilitada automaticamente em bancos de dados de hiperescala configurados com zero réplicas.

Você pode desabilitar e reabilitar a expansão de leitura em bancos de dados individuais e bancos de dados de pool elástico na camada de serviço Premium ou Comercialmente Crítico usando os métodos a seguir.

> [!NOTE]
> A capacidade de desabilitar a expansão de leitura é fornecida para compatibilidade com versões anteriores.

### <a name="azure-portal"></a>Portal do Azure

Você pode gerenciar a configuração de expansão de leitura na folha **Configurar** banco de dados.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo Azure Resource Manager continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos Azure Resource Manager são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

O gerenciamento da expansão de leitura no Azure PowerShell requer a versão de dezembro de 2016 Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Você pode desabilitar ou reabilitar a escala de leitura em Azure PowerShell invocando o cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando o valor desejado ( `Enabled` ou `Disabled` ) para o `-ReadScale` parâmetro.

Para desabilitar a expansão de leitura em um banco de dados existente (substituindo os itens pelos colchetes angulares pelos valores corretos para seu ambiente e descartando os colchetes):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Para desabilitar a expansão de leitura em um novo banco de dados (substituindo os itens nos colchetes angulares pelos valores corretos para seu ambiente e descartando os colchetes):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Para reabilitar a escala de leitura em um banco de dados existente (substituindo os itens nos colchetes angulares pelos valores corretos para seu ambiente e descartando os colchetes):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar um banco de dados com expansão de leitura desabilitada ou para alterar a configuração de um banco de dados existente, use o método a seguir com a `readScale` propriedade definida como `Enabled` ou `Disabled` , como na solicitação de exemplo a seguir.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [bancos de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-a-read-only-replica"></a>Usando TempDB em uma réplica somente leitura

O banco de dados TempDB não é replicado para as réplicas somente leitura. Cada réplica tem sua própria versão do banco de dados TempDB que é criada quando a réplica é criada. Ele garante que o TempDB pode ser atualizado e modificado durante a execução da consulta. Se sua carga de trabalho somente leitura depender do uso de objetos TempDB, você deverá criar esses objetos como parte do script de consulta.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usando a expansão de leitura com bancos de dados replicados geograficamente

Se você estiver usando a expansão de leitura para balancear a carga de cargas de trabalho somente leitura em um banco de dados que é replicado geograficamente (por exemplo, como um membro de um grupo de failover), verifique se a expansão de leitura está habilitada nos bancos de dados primário e secundário replicado geograficamente. Essa configuração garantirá que a mesma experiência de balanceamento de carga continue quando o aplicativo se conectar ao novo primário após o failover. 

Se você estiver se conectando ao banco de dados secundário replicado geograficamente com a escala de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas da mesma forma que direcionamos as conexões no banco de dados primário.  As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. Como o banco de dados secundário com replicação geográfica tem um ponto de extremidade diferente do banco de dados primário, historicamente, para acessar o secundário, não era necessário defini-lo `ApplicationIntent=ReadOnly` . Para garantir a compatibilidade com versões anteriores, o `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer conexão de cliente é permitida).

> [!NOTE]
> Não há suporte para Round Robin ou qualquer outro roteamento com balanceamento de carga entre as réplicas locais do banco de dados secundário.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a oferta de hiperescala do banco de dados SQL, consulte [camada de serviço de hiperescala](service-tier-hyperscale.md).
