---
title: Controles de Acesso à Rede
description: Visão geral dos controles de acesso à rede para o Azure SQL Database e Data Warehouse para gerenciar o acesso e configurar um banco de dados único ou pooled.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 8b4ee679b21d904f997f727f5f26275c86acc9c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414405"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Controles de acesso à rede azure SQL e Data Warehouse

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo faz *não* se aplica à **Instância Gerenciada do Banco de Dados SQL do Azure**. para obter mais informações sobre a configuração de rede, consulte [conectar-se a uma instância gerenciada](sql-database-managed-instance-connect-app.md) .

Quando você cria um novo Azure SQL Server a partir do [portal Azure,](sql-database-single-database-get-started.md)o resultado é um ponto final público no formato, *yourservername.database.windows.net*.

Você pode usar os seguintes controles de acesso à rede para permitir seletivamente o acesso ao Banco de Dados SQL através do ponto final público:
- Permitir serviços do Azure: Quando definido como ON, outros recursos dentro do limite do Azure, por exemplo, uma Máquina Virtual Do Azure, podem acessar o Banco de Dados SQL

- Regras de firewall IP: Use esse recurso para permitir explicitamente conexões a partir de um endereço IP específico, por exemplo, de máquinas locais

Você também pode permitir acesso privado ao Banco de Dados SQL de [Redes Virtuais](../virtual-network/virtual-networks-overview.md) através de:
- Regras de firewall da Rede Virtual: Use esse recurso para permitir o tráfego de uma rede virtual específica dentro do limite do Azure

- Link privado: use esse recurso para criar um ponto final privado para o Azure SQL Server dentro de uma rede virtual específica



Veja o vídeo abaixo para obter uma explicação de alto nível sobre esses controles de acesso e o que eles fazem:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Permitir serviços do Azure 
Durante a criação de um novo Azure SQL Server [do portal Azure](sql-database-single-database-get-started.md), essa configuração é deixada sem controle.



Você também pode alterar essa configuração através do painel de firewall depois que o Azure SQL Server for criado da seguinte forma.
  
 ![Captura de tela do firewall do servidor de gerenciamento][2]

Quando definido como **ON** Azure SQL Server permite comunicações de todos os recursos dentro do limite do Azure, que podem ou não fazer parte da sua assinatura.

Em muitos casos, a configuração **ON** é mais permissiva do que a maioria dos clientes quer. Eles podem querer definir essa configuração como **OFF** e substituí-la por regras mais restritivas de firewall IP ou regras de firewall da Rede Virtual. Isso afeta os seguintes recursos que são executados em VMs no Azure que não fazem parte do seu VNet e, portanto, se conectam ao Banco de Dados Sql através de um endereço IP do Azure.

### <a name="import-export-service"></a>Serviço de Importação/Exportação
O Serviço de Exportação de Importação não funciona quando **o acesso ao Azure** é definido como **OFF**. No entanto, você pode contornar o problema [executando manualmente sqlpackage.exe a partir de um VM Azure ou realizando a exportação](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) diretamente em seu código usando a API DACFx.

### <a name="data-sync"></a>Sincronização de Dados
Para usar o recurso de sincronização de dados com **permitir o acesso aos serviços do Azure** definido **sAqueado,** você precisa criar entradas de regras de firewall individuais para [adicionar endereços IP](sql-database-server-level-firewall-rule.md) da tag de serviço **Sql** para a região que hospeda o banco de dados **do Hub.**
Adicione essas regras de firewall de nível de servidor aos servidores lógicos que hospedam bancos de dados **Hub** e **Member** ( que podem estar em diferentes regiões)

Use o seguinte script PowerShell para gerar os endereços IP correspondentes à tag de serviço Sql para a região oeste dos EUA
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag retorna o intervalo global para Tag de Serviço Sql, apesar de especificar o parâmetro Localização. Certifique-se de filtrá-lo para a região que hospeda o banco de dados hub usado pelo seu grupo de sincronização

Observe que a saída do script PowerShell está na notação CIDR (Classless Inter-Domain Routing) e isso precisa ser convertido em um formato de endereço IP Start and End usando [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) como este
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Faça as seguintes etapas adicionais para converter todos os endereços IP do formato de endereço IP para Iniciar e Encerrar.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Agora você pode adicioná-las como regras de firewall distintas e, em seguida, definir **permitir que os serviços do Azure acessem o servidor** para OFF.


## <a name="ip-firewall-rules"></a>Regras de firewall de IP
O firewall baseado em IP é um recurso do Azure SQL Server que impede todo o acesso ao seu servidor de banco de dados até que você adicione explicitamente [endereços IP](sql-database-server-level-firewall-rule.md) das máquinas clientes.


## <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Além das regras de IP, o firewall do Azure SQL Server permite definir *regras de rede virtuais*.  
Para saber mais, consulte [os pontos finais e regras do serviço da Rede Virtual para o Banco de Dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md) ou assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia do Azure Networking  
Esteja ciente dos seguintes termos de Rede Azure à medida que você explora as regras de firewall da Rede Virtual

**Rede virtual:** Você pode ter redes virtuais associadas à sua assinatura do Azure 

**Sub-rede:** uma rede virtual contém **sub-redes**. Todas as máquinas virtuais do Azure (VMs) que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computadores. Nós de computadores que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure a segurança para permitir o acesso.

**Ponto final do serviço de rede virtual:** Um [ponto final de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais do tipo de serviço DoZure. Neste artigo, estamos interessados no nome do tipo de **Microsoft.Sql**, que faz referência ao serviço do Azure chamado banco de dados SQL.

**Regra da rede virtual:** uma regra da rede virtual para o servidor de Banco de dados SQL do Microsoft Azure é uma sub-rede listada na lista de controle de acesso (ACL) do seu servidor de Banco de dados SQL do Microsoft Azure. Para estar na ACL do seu banco de dados SQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql**. Uma regra da rede virtual informa o servidor de Banco de dados SQL do Microsoft Azure para aceitar comunicações de cada nó que está na sub-rede.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Regras de firewall IP vs. Rede Virtual

O firewall do Azure SQL Server permite especificar os intervalos de endereços IP dos quais as comunicações são aceitas no Banco de Dados SQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. No entanto, as máquinas virtuais (VMs) dentro da rede privada Azure são configuradas com endereços IP *dinâmicos.* Os endereços IP dinâmicos podem mudar quando sua VM é reiniciada e, por sua vez, invalidar a regra de firewall baseada em IP. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall, em um ambiente de produção.

Você pode contornar essa limitação obtendo um endereço IP *estático* para sua VM. Para obter mais detalhes, consulte [Configurar endereços IP particulares para uma máquina virtual usando o Portal do Azure](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é cara quando realizada em escala. 

As regras de rede virtual são alternativas mais fáceis de estabelecer e gerenciar o acesso a partir de uma sub-rede específica que contém suas VMs.

> [!NOTE]
> Ainda não é possível ter o Banco de Dados SQL em uma sub-rede. Se seu servidor do banco de dados SQL do Azure for um nó em uma sub-rede em sua rede virtual, todos os nós dentro da rede virtual podem se comunicar com seu banco de dados SQL. Nesse caso, suas VMs podem se comunicar com o Banco de Dados SQL sem a necessidade de nenhuma regra da rede virtual ou regras de IP.

## <a name="private-link"></a>Link Privado 
O Private Link permite que você se conecte ao Azure SQL Server através de um **ponto final privado**. Um ponto final privado é um endereço IP privado dentro de uma [rede virtual](../virtual-network/virtual-networks-overview.md) e sub-rede específica.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma partida rápida na criação de uma regra de firewall IP em nível de servidor, consulte [Criar um banco de dados SQL do Azure](sql-database-single-database-get-started.md).

- Para obter uma partida rápida na criação de uma regra de firewall Vnet em nível de servidor, consulte [pontos finais e regras de serviço da Rede Virtual para o Banco de Dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md).

- Para obter ajuda para se conectar a um banco de dados SQL do Azure a partir de aplicativos de código aberto ou de terceiros, consulte amostras de [código quickstart do Cliente para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Para obter mais informações sobre as portas adicionais que você pode precisar abrir, consulte a seção **Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

- Para obter uma visão geral da conectividade do banco de dados Azure SQL, consulte [a arquitetura de conectividade SQL do Azure](sql-database-connectivity-architecture.md)

- Para obter uma visão geral de segurança do Banco de Dados SQL do Azure, consulte [Protegendo seu banco de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

