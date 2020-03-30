---
title: Habilitando a configuração de sub-rede auxiliada pelo serviço para a instância gerenciada do banco de dados SQL do Azure
description: Habilitando a configuração de sub-rede auxiliada pelo serviço para a instância gerenciada do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533259"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Habilitando a configuração de sub-rede auxiliada pelo serviço para a instância gerenciada do banco de dados SQL do Azure
A configuração de sub-rede auxiliada por serviços fornece gerenciamento automatizado de configuração de rede para as instâncias gerenciadas por sub-redes. Com a configuração de sub-rede auxiliada pelo serviço, o usuário permanece no controle total do acesso aos dados (fluxos de tráfego TDS), enquanto a instância gerenciada assume a responsabilidade de garantir o fluxo ininterrupto do tráfego de gerenciamento para cumprir o SLA.

Grupos de segurança de rede configurados automaticamente e regras de tabela de rota são visíveis ao cliente e anotados com prefixo _Microsoft.Sql-managedInstances_UseOnly__.

A configuração auxiliada pelo serviço é ativada automaticamente `Microsoft.Sql/managedInstances` quando você ativa a delegação de [sub-rede](../virtual-network/subnet-delegation-overview.md) para provedor de recursos.

> [!IMPORTANT] 
> Uma vez que a sub-delegação esteja ligada, você não poderá desligá-la até remover o último cluster virtual da sub-rede. Para obter mais detalhes sobre como excluir o cluster virtual, consulte o [artigo](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)a seguir .

> [!NOTE] 
> Como a configuração de sub-rede auxiliada por serviço é um recurso essencial para a manutenção do SLA, a partir de 1º de maio de 2020, não será possível implantar instâncias gerenciadas em sub-redes que não são delegadas ao provedor de recursos de instância gerenciada. Em 1º de julho de 2020, todas as sub-redes que contenham instâncias gerenciadas serão automaticamente delegadas ao provedor de recursos de instância gerenciada. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Habilitando a sub-delegação para novas implantações
Para implantar a instância gerenciada na sub-rede `Microsoft.Sql/managedInstances` vazia, você precisa delegá-la ao provedor de recursos conforme descrito no [artigo](../virtual-network/manage-subnet-delegation.md)seguinte . _Observe que o artigo `Microsoft.DBforPostgreSQL/serversv2` referenciado usa provedor de recursos, por exemplo. Em vez disso, `Microsoft.Sql/managedInstances` você precisará usar o provedor de recursos._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Habilitando a delegação de sub-rede para implantações existentes

Para habilitar a delegação de sub-rede para a implantação de instância gerenciada existente, você precisa descobrir a sub-rede virtual onde ela está colocada. 

Para saber isso, `Virtual network/subnet` você `Overview` pode verificar na lâmina do portal da sua instância gerenciada.

Como alternativa, você pode executar os seguintes comandos powershell para aprender isso. Substitua **o id de assinatura** pelo seu ID de assinatura. Substitua também o **nome rg** pelo grupo de recursos para sua instância gerenciada e substitua o nome **mi com** o nome da sua instância gerenciada.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Uma vez que você encontrar a sub-rede de instância gerenciada, você precisa delegá-la ao `Microsoft.Sql/managedInstances` provedor de recursos conforme descrito no [artigo](../virtual-network/manage-subnet-delegation.md)a seguir . _Observe que o artigo `Microsoft.DBforPostgreSQL/serversv2` referenciado usa provedor de recursos, por exemplo. Em vez disso, `Microsoft.Sql/managedInstances` você precisará usar o provedor de recursos._


> [!IMPORTANT]
> Ativar a configuração auxiliada pelo serviço não causa failover ou interrupção na conectividade para instâncias gerenciadas que já estão na sub-rede.
