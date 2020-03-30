---
title: Tipos de conexão de instância gerenciada
description: Saiba mais sobre os tipos de conexão de instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819466"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Tipos de conexão de instância gerenciada pelo Azure SQL Database

Este artigo explica como os clientes se conectam à instância gerenciada do Azure SQL Database, dependendo do tipo de conexão. As amostras de script para alterar os tipos de conexão são fornecidas abaixo, juntamente com considerações relacionadas à alteração das configurações de conectividade padrão.

## <a name="connection-types"></a>Tipos de conexão

A instância gerenciada do Banco de Dados SQL do Azure suporta os seguintes dois tipos de conexão:

- **Redirecionamento (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados. Para habilitar a conectividade usando redirecionamento, você deve abrir firewalls e Grupos de Segurança de Rede (NSG) para permitir o acesso nas portas 1433 e 11000-11999. Os pacotes vão diretamente para o banco de dados e, portanto, há melhorias de desempenho de latência e de throughput usando Redirecionar sobre proxy.
- **Proxy (padrão):** Neste modo, todas as conexões estão usando um componente de gateway proxy. Para habilitar a conectividade, é necessário abrir apenas a porta 1433 para redes privadas e a porta 3342 para conexão pública. A escolha desse modo pode resultar em maior latência e menor rendimento, dependendo da natureza da carga de trabalho. Recomendamos enfaticamente a política de conexão de redirecionamento sobre a política de conexão de proxy para a menor latência e maior taxa de transferência.

## <a name="redirect-connection-type"></a>Redirecionar o tipo de conexão

O tipo de conexão de redirecionamento significa que, após a sessão TCP ser estabelecida no mecanismo SQL, a sessão do cliente obtém o IP virtual de destino do nó de cluster virtual do balanceador de carga. Os pacotes subseqüentes fluem diretamente para o nó de cluster virtual, contornando o gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> O tipo de conexão de redirecionamento atualmente funciona apenas para ponto final privado. Independentemente da configuração do tipo de conexão, as conexões que vêm através do ponto final público seriam através de um proxy.

## <a name="proxy-connection-type"></a>Tipo de conexão proxy

O tipo de conexão proxy significa que a sessão TCP é estabelecida usando o gateway e todos os pacotes subseqüentes fluem através dele. O diagrama a seguir ilustra esse fluxo de tráfego.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script para alterar as configurações do tipo de conexão usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O script powershell a seguir mostra como alterar o tipo de conexão para uma instância gerenciada para Redirecionar.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um banco de dados em uma instância gerenciada](sql-database-managed-instance-get-started-restore.md)
- Saiba como [configurar um ponto final público na instância gerenciada](sql-database-managed-instance-public-endpoint-configure.md)
- Conheça a [arquitetura de conectividade de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md)