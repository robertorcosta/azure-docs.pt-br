---
title: Configuração de regras de aplicativo do Firewall do Azure com FQDNs do SQL
description: Neste artigo, você aprenderá a configurar FQDNs do SQL nas regras de aplicativo do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: 8b67574f435681d8071eda1ad954dcafb5124cbf
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655110"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configuração de regras de aplicativo do Firewall do Azure com FQDNs do SQL

Agora é possível configurar as regras de aplicativo do Firewall do Azure com FQDNs do SQL. Isso permite limitar o acesso das redes virtuais apenas às instâncias do SQL Server especificadas.

Com os FQDNs do SQL, é possível filtrar o tráfego:

- Das redes virtuais para um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure. Por exemplo:  Permitir acesso apenas a *sql-server1.database.windows.net*.
- Do ambiente local para Instâncias Gerenciadas do SQL do Azure ou do IaaS do SQL em execução nas suas redes virtuais.
- De spoke-to-spoke para Instâncias Gerenciadas do SQL do Azure ou do IaaS do SQL em execução nas suas redes virtuais.

A filtragem de FQDN do SQL tem suporte apenas no [modo de proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se usar o SQL no modo de redirecionamento padrão, você pode filtrar o acesso usando a marca de serviço do SQL como parte das [regras de rede](overview.md#network-traffic-filtering-rules).
Se usar portas não padrão para o tráfego de IaaS do SQL, você pode configurar essas portas nas regras de aplicativo do firewall.

## <a name="configure-using-azure-cli"></a>Configuração usando a CLI do Azure

1. Implantar um [Firewall do Azure usando a CLI do Azure](deploy-cli.md).
2. Se filtrar o tráfego para o Banco de Dados SQL do Azure, SQL Data Warehouse ou Instância Gerenciada do SQL, verifique se o modo de conectividade de SQL está definido como **Proxy**. Para saber como alternar o modo de conectividade do SQL, veja [Configurações de Conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > O modo *Proxy* do SQL pode resultar em mais latência em comparação com o *redirecionamento*. Se quiser continuar a usar o modo de redirecionamento, que é o padrão para clientes que se conectam com o Azure, filtre o acesso usando a [marca de serviço](service-tags.md) do SQL nas [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule) do firewall.

3. Configure uma regra de aplicativo com o FQDN do SQL para permitir o acesso a um SQL Server:

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

## <a name="configure-using-the-azure-portal"></a>Configuração usando o portal do Azure
1. Implantar um [Firewall do Azure usando a CLI do Azure](deploy-cli.md).
2. Se filtrar o tráfego para o Banco de Dados SQL do Azure, SQL Data Warehouse ou Instância Gerenciada do SQL, verifique se o modo de conectividade de SQL está definido como **Proxy**. Para saber como alternar o modo de conectividade do SQL, veja [Configurações de Conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > O modo *Proxy* do SQL pode resultar em mais latência em comparação com o *redirecionamento*. Se quiser continuar a usar o modo de redirecionamento, que é o padrão para clientes que se conectam com o Azure, filtre o acesso usando a [marca de serviço](service-tags.md) do SQL nas [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule) do firewall.
3. Adicione a regra de aplicativo com o protocolo, a porta e o FQDN do SQL apropriados e, em seguida, selecione **Salvar**.
   ![regras de aplicativo com o FQDN do SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Acesse o SQL de uma máquina virtual em uma rede virtual que filtra o tráfego através do firewall. 
5. Valide se os [logs do Firewall do Azure](log-analytics-samples.md) mostram que o tráfego é permitido.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os modos de proxy e redirecionamento do SQL, veja [Arquitetura de conectividade do Banco de Dados SQL do Azure](../sql-database/sql-database-connectivity-architecture.md).