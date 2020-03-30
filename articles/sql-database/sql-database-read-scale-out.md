---
title: Leia consultas em réplicas
description: O Banco de Dados SQL do Azure fornece a capacidade de equilibrar cargas de trabalho somente de leitura usando a capacidade de réplicas somente leitura - chamada Read Scale-Out.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206938"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Como parte da [arquitetura de alta disponibilidade,](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)cada banco de dados no nível de serviço Premium e Business Critical é automaticamente provisionado com uma réplica primária e várias réplicas secundárias. As réplicas secundárias são provisionadas com o mesmo tamanho de computação da réplica primária. O recurso **Read Scale-Out** permite que você balance cargas de trabalho somente leitura do Banco de Dados SQL usando a capacidade de uma das réplicas somente de leitura em vez de compartilhar a réplica de leitura-gravação. Dessa forma, a carga de trabalho somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso destina-se aos aplicativos que incluem cargas de trabalho somente leitura separadas logicamente, como análises. Nos níveis de serviço Premium e Business Critical, os aplicativos podem ganhar benefícios de desempenho usando essa capacidade adicional sem custo adicional.

O recurso **Read Scale-Out** também está disponível no nível de serviço Hyperscale quando pelo menos uma réplica secundária é criada. Várias réplicas secundárias podem ser usadas se cargas de trabalho somente leitura requerem mais recursos do que disponíveis em uma réplica secundária. A arquitetura de alta disponibilidade dos níveis de serviço básico, padrão e de uso geral não inclui nenhuma réplica. O recurso **''Descontar escala de** leitura' não está disponível nessas camadas de serviço.

O diagrama a seguir ilustra-o usando um banco de dados Business Critical.

![Réplicas somente leitura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

O recurso ''Desserlador de leitura' é habilitado por padrão em novos bancos de dados Premium, Business Critical e Hyperscale. Para Hyperscale, uma réplica secundária é criada por padrão para novos bancos de dados. Se a seqüência de conexão SQL estiver configurada, `ApplicationIntent=ReadOnly`o aplicativo será redirecionado pelo gateway para uma réplica somente leitura desse banco de dados. Para obter informações sobre `ApplicationIntent` como usar a propriedade, consulte [Especificando a Intenção de Aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se você deseja garantir que o aplicativo se conecte à réplica primária, independentemente da `ApplicationIntent` configuração na seqüência de conexões SQL, você deve desativar explicitamente a escala de leitura ao criar o banco de dados ou ao alterar sua configuração. Por exemplo, se você atualizar seu banco de dados do nível Padrão ou Propósito Geral para o nível Premium, Business Critical ou Hyperscale e quiser garantir que todas as suas conexões continuem indo para a réplica principal, desative a escala de leitura. Para obter detalhes sobre como desativá-lo, consulte [Ativar e desativar a escala de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Os recursos do Consulta Data Store, Extended Events e SQL Profiler não são suportados nas réplicas somente de leitura.

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes momentos, pode haver uma pequena latência entre diferentes réplicas. A Expansão de leitura oferece suporte a consistência em nível de sessão. Isso significa que, se a sessão somente leitura se reconectar após um erro de conexão causado pela indisponibilidade de réplica, ela pode ser redirecionada para uma réplica que não esteja 100% atualizada com a réplica de leitura-gravação. Da mesma forma, se um aplicativo grava dados usando uma sessão de leitura e os lê imediatamente usando uma sessão somente leitura, é possível que as atualizações mais recentes não sejam imediatamente visíveis na réplica. A latência é causada por uma operação de refazer de log de transação assíncrona.

> [!NOTE]
> As latências de replicação na região são baixas e essa situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Conectar-se a uma réplica somente leitura

Quando você habilita a expansão de leitura para um banco de dados, a opção `ApplicationIntent` na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica somente leitura. Especificamente, se o valor `ApplicationIntent` é `ReadWrite` (o valor padrão), a conexão será direcionada para a réplica de leitura-gravação do banco de dados. Isso é idêntico ao comportamento existente. Se o valor de `ApplicationIntent` é `ReadOnly`, a conexão é roteada para uma réplica somente leitura.

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

Você pode verificar se você está conectado a uma réplica somente leitura ao executar a consulta a seguir. Ela retornará READ_ONLY quando conectado a uma réplica somente leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Em um determinado momento qualquer apenas uma das réplicas de AlwaysON fica acessível por sessões somente leitura.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitoramento e solução de problemas réplica somente para leitura

Quando conectado a uma réplica somente leitura, você pode `sys.dm_db_resource_stats` acessar as métricas de desempenho usando o Detran. Para acessar as estatísticas do `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` plano `sys.dm_exec_sql_text` de consulta, use o e os DMVs.

> [!NOTE]
> O `sys.resource_stats` Detran no banco de dados mestre lógico retorna os dados de uso e armazenamento da CPU da réplica principal.

## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar o Escalonamento de Leitura

O Read Scale-Out é ativado por padrão nos níveis de serviço Premium, Business Critical e Hyperscale. A leitura de escala não pode ser ativada nos níveis de serviço Básico, Padrão ou Propósito Geral. O Read Scale-Out é desativado automaticamente em bancos de dados Hyperscale configurados com 0 réplicas.

Você pode desativar e reativar o Read Scale-Out em bancos de dados únicos e bancos de dados de pool elásticos no nível de serviço Premium ou Business Critical usando os seguintes métodos.

> [!NOTE]
> A capacidade de desativar a Leitura scale-Out é fornecida para compatibilidade retrógrada.

### <a name="azure-portal"></a>Portal do Azure

Você pode gerenciar a configuração de escala de leitura na lâmina de banco de dados **Configurar.**

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Gerenciar a Expansão de leitura no Microsoft Azure PowerShell requer a versão de dezembro de 2016 do Microsoft Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Você pode desativar ou reativar a escala de leitura no Azure PowerShell invocando o cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando o valor desejado – `Enabled` ou `Disabled` -- para o `-ReadScale` parâmetro.

Para desativar a escala de leitura em um banco de dados existente (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e soltando os suportes angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Para desativar a escala de leitura em um novo banco de dados (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e soltando os suportes angulares):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Para reativar a escala de leitura em um banco de dados existente (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e soltando os suportes angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar um banco de dados com a escala de leitura desativada ou `readScale` alterar `Enabled` a `Disabled` configuração de um banco de dados existente, use o seguinte método com a propriedade definida ou como na solicitação de amostra abaixo.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [Bancos de dados - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Usando tempDB em réplica somente leitura

O banco de dados TempDB não é replicado nas réplicas somente de leitura. Cada réplica tem sua própria versão do banco de dados TempDB que é criada quando a réplica é criada. Ele garante que o TempDB é atualizável e pode ser modificado durante a execução da consulta. Se sua carga de trabalho somente leitura depender do uso de objetos TempDB, você deve criar esses objetos como parte do seu script de consulta.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usar escala de leitura com bancos de dados replicados geograficamente

Se você estiver usando a Leitura de Escala para equilibrar cargas de trabalho somente de leitura em um banco de dados que seja replicado geo-replicado (por exemplo, como membro de um grupo de failover), certifique-se de que a escala de leitura esteja habilitada nos bancos de dados secundários primários e geo-replicados. Essa configuração garantirá que a mesma experiência de balanceamento de carga continue quando o aplicativo se conectar à nova primária após o failover. Se estiver se conectando ao banco de dados secundário geograficamente replicado com escala de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas da mesma forma que roteamos conexões no banco de dados primário.  As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. Como o banco de dados secundário geo-replicado tem um ponto final diferente do banco de `ApplicationIntent=ReadOnly`dados primário, historicamente para acessar o secundário que não era necessário definir . Para garantir a compatibilidade com versões anteriores, o `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer conexão de cliente é permitida).

> [!NOTE]
> Não é suportado o round-robin ou qualquer outro roteamento balanceado de carga entre as réplicas locais do banco de dados secundário.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a oferta de hyperscale do banco de dados SQL, consulte [o nível de serviço Hyperscale](./sql-database-service-tier-hyperscale.md).
