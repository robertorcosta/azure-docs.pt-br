---
title: Arquitetura de conectividade
description: Este documento explica a arquitetura de conectividade Azure SQL para conexões de banco de dados de dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 2028aac9c01aedc4baa568d370c9f7d21c920647
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419256"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de conectividade do SQL do Azure
> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo faz *não* se aplica à **Instância Gerenciada do Banco de Dados SQL do Azure**. Consulte a [arquitetura conectividade para obter uma instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).

Este artigo explica a arquitetura de vários componentes que direcionam o tráfego de rede para o Banco de Dados SQL do Azure ou para o SQL Data Warehouse. Também explica diferentes políticas de conexão e como ela impacta os clientes que se conectam dentro do Azure e clientes que se conectam de fora do Azure. 

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de conectividade do Banco de Dados SQL do Azure.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

As seguintes etapas descrevem como uma conexão é estabelecida com um banco de dados SQL do Azure:

- Os clientes se conectam ao gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de conexão efetiva, redireciona o tráfego ou usa um proxy para ele para o cluster de banco de dados correto.
- No cluster de banco de dados, o tráfego é encaminhado para o banco de dados SQL do Azure apropriado.

## <a name="connection-policy"></a>Política de Conexão

O Banco de Dados SQL do Azure oferece suporte às três opções a seguir para a configuração de diretiva de conexão de um servidor do Banco de Dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados, levando à redução da latência e melhor produtividade. Para que as conexões usem esse modo, os clientes precisam:
   - Permitir a comunicação de saída do cliente para todos os endereços IP SQL Do Azure SQL na região em portas na faixa de 11000 11999. Use as etiquetas de serviço para SQL para tornar isso mais fácil de gerenciar.  
   - Permitir a comunicação de saída do cliente para endereços IP do gateway de banco de dados Azure SQL na porta 1433.

- **Proxy:** Neste modo, todas as conexões são proxidas através dos gateways do Banco de Dados SQL do Azure, levando a um aumento da latência e redução do throughput. Para que as conexões usem esse modo, os clientes precisam permitir a comunicação de saída do cliente para os endereços IP do gateway gateway do Banco de Dados Azure SQL na porta 1433.

- **Padrão:** Esta é a política de conexão em vigor em todos os `Proxy` servidores `Redirect`após a criação, a menos que você altere explicitamente a política de conexão para qualquer um ou . A política`Redirect` padrão é para todas as conexões de cliente originárias do Azure (por exemplo, de uma Máquina Virtual Do Azure) e `Proxy`para todas as conexões de clienteoriginadas fora (por exemplo, conexões da sua estação de trabalho local).

 Recomendamos altamente `Redirect` a política `Proxy` de conexão sobre a política de conexão para a menor latência e maior throughput. No entanto, você precisará atender aos requisitos adicionais para permitir o tráfego de rede como descrito acima. Se o cliente for uma Máquina Virtual Azure, você pode conseguir isso usando o NSG (Network Security Groups, grupos de segurança de [rede)](../virtual-network/security-overview.md#service-tags)com tags de serviço . Se o cliente estiver se conectando a partir de uma estação de trabalho no local, então você pode precisar trabalhar com o administrador da rede para permitir o tráfego de rede através do firewall corporativo.

## <a name="connectivity-from-within-azure"></a>Conectividade de dentro do Azure

Se você estiver se conectando de dentro do Azure, as conexões terão uma política de conexão de `Redirect` por padrão. Uma política de `Redirect` significa que, após a sessão TCP ser estabelecida com o Banco de Dados SQL do Azure, a sessão do cliente será redirecionada para o cluster de banco de dados correto com uma alteração do IP virtual de destino daquele do gateway do Banco de Dados SQL do Azure para aquele do cluster. Depois disso, todos os pacotes seguintes fluem diretamente para o cluster, ignorando o gateway do Banco de Dados SQL do Azure. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se você estiver se conectando de fora do Azure, as conexões terão uma política de conexão de `Proxy` por padrão. Uma política de `Proxy` significa que a sessão TCP é estabelecida por meio do gateway do Banco de Dados SQL do Azure e todos os pacotes seguintes fluem por meio do gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Além disso, abra as portas 14000-14999 para habilitar [a conexão com dac](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços IP de gateway do Banco de Dados SQL do Azure

A tabela abaixo lista os endereços IP de Gateways por região. Para se conectar a um banco de dados SQL do Azure, você precisa permitir que o tráfego de rede & de **todos os** Gateways para a região.

Detalhes de como o tráfego deve ser migrado para novos Gateways em regiões específicas estão no seguinte artigo: Migração de [tráfego do Banco de Dados Azure SQL para Gateways mais novos](sql-database-gateway-migration.md)


| Nome da Região          | Endereços IP do Gateway |
| --- | --- |
| Austrália Central    | 20.36.105.0 |
| Austrália Central2   | 20.36.113.0 |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251 |
| Sul do Brasil         | 104.41.11.5, 191.233.200.14 |
| Canadá Central       | 40.85.224.249      |
| Leste do Canadá          | 40.86.226.166      |
| Centro dos EUA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Leste da China           | 139.219.130.35     |
| Leste da China 2         | 40.73.82.1         |
| Norte da China          | 139.219.15.17      |
| Norte da China 2        | 40.73.50.0         |
| Leste da Ásia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Leste dos EUA              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Leste dos EUA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| França Central       | 40.79.137.0, 40.79.129.1 |
| Alemanha Central      | 51.4.144.100       |
| Nordeste da Alemanha   | 51.5.144.179       |
| Centro da Índia        | 104.211.96.159     |
| Sul da Índia          | 104.211.224.146    |
| Oeste da Índia           | 104.211.160.80     |
| Leste do Japão           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Oeste do Japão           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Coreia Central        | 52.231.32.42       |
| Sul da Coreia          | 52.231.200.86      |
| Centro-Norte dos EUA     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Norte da Europa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Leste da Noruega          | 51.120.96.0        |
| Noruega Ocidental          | 51.120.216.0       |
| Norte da África do Sul   | 102.133.152.0      |
| África do Sul Ocidental    | 102.133.24.0       |
| Centro-Sul dos Estados Unidos     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Sudeste da Ásia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| EAU Central          | 20.37.72.64        |
| Norte dos EAU            | 65.52.248.0        |
| Sul do Reino Unido             | 51.140.184.11      |
| Oeste do Reino Unido              | 51.141.8.11        |
| Centro-Oeste dos EUA      | 13.78.145.25       |
| Europa Ocidental          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Oeste dos EUA              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Oeste dos EUA 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter mais informações sobre o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente, consulte [Portas depois da 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obter informações sobre a visão geral do desenvolvimento de aplicativos em geral, consulte [Visão Geral do Desenvolvimento de Aplicativos do Banco de Dados SQL](sql-database-develop-overview.md).
