---
title: Tipos de conexão
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre os tipos de conexão do Azure SQL Instância Gerenciada
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e46c6d1c14d226522a1d534418b91076efeaaccf
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070710"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Tipos de conexão da Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como os clientes se conectam ao SQL do Azure Instância Gerenciada dependendo do tipo de conexão. Exemplos de script para alterar tipos de conexão são fornecidos abaixo, juntamente com considerações relacionadas à alteração das configurações de conectividade padrão.

## <a name="connection-types"></a>Tipos de conexão

O Azure SQL Instância Gerenciada dá suporte aos seguintes dois tipos de conexão:

- **Redirecionamento (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados. Para habilitar a conectividade usando o redirecionamento, você deve abrir firewalls e grupos de segurança de rede (NSG) para permitir o acesso nas portas 1433 e 11000-11999. Os pacotes vão diretamente para o banco de dados e, portanto, há melhorias de desempenho de latência e taxa de transferência usando o redirecionamento por proxy.
- **Proxy (padrão):** Nesse modo, todas as conexões estão usando um componente de gateway de proxy. Para habilitar a conectividade, somente a porta 1433 para redes privadas e a porta 3342 para conexão pública precisam ser abertas. A escolha desse modo pode resultar em maior latência e menor rendimento, dependendo da natureza da carga de trabalho. É altamente recomendável a política de conexão de redirecionamento pela política de conexão de proxy para a menor latência e taxa de transferência mais alta.

## <a name="redirect-connection-type"></a>Redirecionar tipo de conexão

No tipo de conexão redirecionar, depois que a sessão TCP é estabelecida com o mecanismo SQL, a sessão do cliente obtém o IP virtual de destino do nó do cluster virtual do balanceador de carga. Os pacotes subsequentes fluem diretamente para o nó do cluster virtual, ignorando o gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> O tipo de conexão de redirecionamento atualmente funciona apenas para um ponto de extremidade privado. Independentemente da configuração do tipo de conexão, as conexões provenientes do ponto de extremidade público seriam por meio de um proxy.

## <a name="proxy-connection-type"></a>Tipo de conexão de proxy

No tipo de conexão proxy, a sessão TCP é estabelecida usando o gateway e todos os pacotes subsequentes fluem através dele. O diagrama a seguir ilustra esse fluxo de tráfego.

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Alterando tipo de conexão

- **Usando o portal:** Para alterar o tipo de conexão usando o portal do Azure, abra a página rede virtual e use a configuração **tipo de conexão** para alterar o tipo de conexão e salvar as alterações.

- **Script para alterar as configurações de tipo de conexão usando o PowerShell:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O script do PowerShell a seguir mostra como alterar o tipo de conexão de uma instância gerenciada para o `Redirect` .

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

- [Restaurar um banco de dados para o SQL Instância Gerenciada](restore-sample-database-quickstart.md)
- Saiba como [configurar um ponto de extremidade público no SQL instância gerenciada](public-endpoint-configure.md)
- Saiba mais sobre a [arquitetura de conectividade do SQL instância gerenciada](connectivity-architecture-overview.md)
