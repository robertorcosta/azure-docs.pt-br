---
title: Configurar regras de aplicativo de firewall do Azure com FQDNs do SQL
description: Neste artigo, você aprenderá a configurar os FQDNs do SQL nas regras de aplicativo do firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 28252b42264dc6c1be403e99689f845d7143b1f7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200461"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurar regras de aplicativo de firewall do Azure com FQDNs do SQL

Agora você pode configurar regras de aplicativo de firewall do Azure com FQDNs do SQL. Isso permite que você limite o acesso de suas redes virtuais somente às instâncias do SQL Server especificadas.

Com os FQDNs do SQL, você pode filtrar o tráfego:

- Do seu VNets para um banco de dados SQL do Azure ou um SQL Data Warehouse do Azure. Por exemplo: permitir acesso somente a *SQL-Server1.Database.Windows.net*.
- Do local para instâncias gerenciadas do SQL do Azure ou do SQL IaaS em execução no seu VNets.
- Do spoke para o spoke para instâncias gerenciadas do SQL do Azure ou a IaaS do SQL em execução no seu VNets.

A filtragem de FQDN do SQL tem suporte somente no [modo proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se você usar o SQL no modo de redirecionamento padrão, poderá filtrar o acesso usando a marca de serviço do SQL como parte das [regras de rede](overview.md#network-traffic-filtering-rules).
Se você usar portas não padrão para o tráfego de IaaS do SQL, poderá configurar essas portas nas regras de aplicativo de firewall.

As regras de aplicativo com FQDNs do SQL estão disponíveis atualmente em todas as regiões por meio do portal do Azure, CLI do Azure, REST e modelos.

## <a name="configure-using-azure-cli"></a>Configurar usando CLI do Azure

1. Implante um [Firewall do Azure usando CLI do Azure](deploy-cli.md).
2. Se você filtrar o tráfego para o banco de dados SQL do Azure, SQL Data Warehouse ou SQL Instância Gerenciada, verifique se o modo de conectividade SQL está definido como **proxy**. Para saber como alternar o modo de conectividade do SQL, consulte [configurações de conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > O modo SQL *proxy* pode resultar em mais latência em comparação com o *redirecionamento*. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam no Azure, você pode filtrar o acesso usando a [marca de serviço](service-tags.md) do SQL nas [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)do firewall.

3. Configurar uma regra de aplicativo com o FQDN do SQL para permitir o acesso a um SQL Server:

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
1. Implante um [Firewall do Azure usando CLI do Azure](deploy-cli.md).
2. Se você filtrar o tráfego para o banco de dados SQL do Azure, SQL Data Warehouse ou SQL Instância Gerenciada, verifique se o modo de conectividade SQL está definido como **proxy**. Para saber como alternar o modo de conectividade do SQL, consulte [configurações de conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > O modo SQL *proxy* pode resultar em mais latência em comparação com o *redirecionamento*. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam no Azure, você pode filtrar o acesso usando a [marca de serviço](service-tags.md) do SQL nas [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)do firewall.
3. Adicione a regra de aplicativo com o protocolo, a porta e o FQDN do SQL apropriados e, em seguida, selecione **salvar**.
   ![regra de aplicativo com FQDN do SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Acesse o SQL de uma máquina virtual em uma VNet que filtra o tráfego por meio do firewall. 
5. Valide se os [logs de firewall do Azure](log-analytics-samples.md) mostram o tráfego permitido.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SQL proxy e modos de redirecionamento, consulte [arquitetura de conectividade do banco de dados SQL do Azure](../sql-database/sql-database-connectivity-architecture.md).