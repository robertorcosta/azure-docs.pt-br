---
title: Configure as regras do aplicativo Azure Firewall com SQL FQDNs
description: Neste artigo, você aprende como configurar SQL FQDNs nas regras do aplicativo Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501505"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configure as regras do aplicativo Azure Firewall com SQL FQDNs

> [!IMPORTANT]
> As regras do aplicativo Azure Firewall com SQL FQDNs estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Agora você pode configurar as regras do aplicativo Azure Firewall com FQDNs SQL. Isso permite limitar o acesso de suas redes virtuais apenas às instâncias especificadas do servidor SQL.

Com SQL FQDNs, você pode filtrar o tráfego:

- De seus VNets a um banco de dados SQL do Azure ou um Armazém de Dados SQL do Azure. Por exemplo: Só permite o acesso a *sql-server1.database.windows.net*.
- De instâncias gerenciadas no local ao Azure SQL Managed Instances ou SQL IaaS em execução em seus VNets.
- De conversa com o Azure SQL Managed Instances ou SQL IaaS em execução em seus VNets.

Durante a visualização pública, a filtragem SQL FQDN é suportada apenas no [modo proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se você usar SQL no modo de redirecionamento padrão, poderá filtrar o acesso usando a tag de serviço SQL como parte das regras da [rede](overview.md#network-traffic-filtering-rules).
Se você usar portas não padrão para tráfego SQL IaaS, poderá configurar essas portas nas regras do aplicativo de firewall.

As regras de aplicação com SQL FQDNs estão atualmente disponíveis em todas as regiões através do portal Azure, Azure CLI, REST e modelos.

## <a name="configure-using-azure-cli"></a>Configure usando o Azure CLI

1. Implante [um Firewall Azure usando o Azure CLI](deploy-cli.md).
2. Se você filtrar o tráfego para o Banco de Dados SQL do Azure, o SQL Data Warehouse ou a SQL Managed Instance, certifique-se de que o modo de conectividade SQL está definido como **Proxy**. Para saber como mudar o modo de conectividade SQL, consulte [as configurações de conectividade Do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > O modo *proxy* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam dentro do Azure, você pode filtrar o acesso usando a [tag de serviço](service-tags.md) SQL nas regras da [rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)de firewall .

3. Configure uma regra de aplicativo com O SQL FQDN para permitir o acesso a um servidor SQL:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurar usando o portal do Azure
1. Implante [um Firewall Azure usando o Azure CLI](deploy-cli.md).
2. Se você filtrar o tráfego para o Banco de Dados SQL do Azure, o SQL Data Warehouse ou a SQL Managed Instance, certifique-se de que o modo de conectividade SQL está definido como **Proxy**. Para saber como mudar o modo de conectividade SQL, consulte [as configurações de conectividade Do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > O modo *proxy* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam dentro do Azure, você pode filtrar o acesso usando a [tag de serviço](service-tags.md) SQL nas regras da [rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)de firewall .
3. Adicione a regra do aplicativo com o protocolo, porta e SQL FQDN apropriados e, em seguida, selecione **Salvar**.
   ![regra de aplicação com SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Acesse o SQL de uma máquina virtual em um VNet que filtra o tráfego através do firewall. 
5. Valide se [os registros do Firewall do Azure](log-analytics-samples.md) mostram que o tráfego é permitido.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os modos de proxy e redirecionamento do SQL, consulte [a arquitetura de conectividade do banco de dados Azure SQL](../sql-database/sql-database-connectivity-architecture.md).