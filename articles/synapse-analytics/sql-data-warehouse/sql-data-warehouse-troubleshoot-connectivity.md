---
title: Solução de problemas de conectividade
description: Semproblemas de conectividade no pool Synapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8aaca40961b1294336b236305ecdffcc92c5a4d9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742583"
---
# <a name="troubleshooting-connectivity-issues"></a>Solucionar problemas de conectividade

Este artigo lista técnicas comuns de solução de problemas em torno da conexão ao seu banco de dados SQL Analytics.

- [Verifique a disponibilidade do serviço](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verificar se a operação está em pausa ou em dimensionamento](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Verificar as configurações de firewall](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Verificar as configurações do ponto de extremidade de serviço/VNet](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Verificar se os drivers são os mais recentes](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Verificar a cadeia de conexão](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemas de conexão intermitentes](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Mensagens de erro comuns](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verifique a disponibilidade do serviço

Verifique se o serviço está disponível. No portal Azure, vá para o pool Synapse SQL que você está tentando conectar. No painel TOC esquerdo, clique em **Diagnosticar e resolver problemas**.

![Selecione a saúde dos recursos](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O status do seu pool Synapse SQL será mostrado aqui. Se o serviço não estiver aparecendo como **Disponível,** verifique outras etapas.

![Serviço disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se a saúde do recurso mostrar que a instância do pool Synapse Synapse SqL está pausada ou dimensionada, siga a orientação para retomar sua instância.

![Serviço pausado](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Informações adicionais sobre a Saúde dos Recursos podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificar se a operação está em pausa ou em dimensionamento

Verifique o portal para ver se a instância do pool Synapse Synapse SQL está pausada ou dimensionada.

![Serviço pausado](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se você ver que seu serviço está pausado ou dimensionado, verifique se ele não está durante o seu cronograma de manutenção. No portal para a sua *visão geral*do pool Synapse SQL, você verá o cronograma de manutenção eleito.

![Cronograma de manutenção de visão geral](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, verifique com o administrador de TI para verificar se essa manutenção não é um evento agendado. Para retomar a instância do SQL Analytics, siga [estas etapas](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Verificar as configurações de firewall

O banco de dados SQL Analytics se comunica pela porta 1433.Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall da rede. Nesse caso, você não poderá se conectar ao servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433. Informações adicionais sobre configurações de firewall podem ser encontradas [aqui](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificar as configurações do ponto de extremidade de serviço/VNet

Se você está recebendo erros 40914 e 40615, consulte [a descrição e resolução de erros aqui](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Verificar se os drivers são os mais recentes

### <a name="software"></a>Software

Verifique se você está usando as ferramentas mais recentes para se conectar ao seu pool Synapse SQL:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Drivers

Verifique se você está usando as versões mais recentes do driver.O uso de uma versão mais antiga dos drivers pode resultar em comportamentos inesperados, já que os drivers mais antigos podem não suportar novos recursos.

- [ODBCODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Verificar a cadeia de conexão

Verifique se as cadeias de conexão estão configuradas corretamente.  Abaixo estão algumas amostras.  Você pode encontrar informações adicionais sobre [cadeias de conexão aqui](sql-data-warehouse-connection-strings.md).

Cadeia de conexão do ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

String de conexão ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Seqüência de conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadeia de conexão JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de conexão intermitentes

Verifique se você está com uma carga pesada sobre o servidor, com um grande número de solicitações enfileiradas. Você pode precisar aumentar seu pool Synapse SQL para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Erros 40914 e 40615, veja a [descrição e resolução](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)do erro aqui .

## <a name="still-having-connectivity-issues"></a>Ainda tem problemas de conectividade?

Crie um bilhete de [suporte para](sql-data-warehouse-get-started-create-support-ticket.md) que a equipe de engenharia possa apoiá-lo.
