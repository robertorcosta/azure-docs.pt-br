---
title: Arquitetura de conectividade - Banco de dados Azure para MariaDB
description: Descreve a arquitetura de conectividade do seu banco de dados Azure para servidor MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4fd6cc2133c6910bace6c36d153085956419b22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532359"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Arquitetura de conectividade no Banco de Dados Azure para MariaDB
Este artigo explica o Banco de Dados Do Azure para arquitetura de conectividade MariaDB, bem como como o tráfego é direcionado para o seu Banco de Dados Azure para a instância MariaDB de clientes dentro e fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

A conexão com o seu Banco de Dados Azure para MariaDB é estabelecida através de um gateway que é responsável por direcionar conexões recebidas para a localização física do seu servidor em nossos clusters. O diagrama a seguir ilustra o fluxo de tráfego.

![Visão geral da arquitetura de conectividade](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

À medida que o cliente se conecta ao banco de dados, ele recebe uma seqüência de conexão que se conecta ao gateway. Este gateway tem um endereço IP público que ouve a porta 3306. Dentro do cluster de banco de dados, o tráfego é encaminhado para o Banco de Dados Azure apropriado para O MariaDB. Portanto, para se conectar ao seu servidor, como a partir de redes corporativas, é necessário abrir o firewall lateral do cliente para permitir que o tráfego de saída seja capaz de alcançar nossos gateways. Abaixo você pode encontrar uma lista completa dos endereços IP usados pelos nossos gateways por região.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Banco de dados Azure para endereços IP de gateway Do VaDb

A tabela a seguir lista os IPs primários e secundários do Banco de Dados Azure para o gateway MariaDB para todas as regiões de dados. O endereço IP principal é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP failover em caso de falha do principal. Como mencionado, os clientes devem permitir a saída para ambos os endereços IP. O segundo endereço IP não escuta nenhum serviço até que seja ativado pelo Banco de Dados Do Azure para que o MariaDB aceite conexões.

| **Nome da região** | **Endereços IP do Gateway** |
|:----------------|:-------------|
| Austrália Central| 20.36.105.0     |
| Austrália Central2     | 20.36.113.0     |
| Leste da Austrália | 13.75.149.87, 40.79.161.1     |
| Sudeste da Austrália |191.239.192.109, 13.73.109.251     |
| Sul do Brasil | 104.41.11.5, 191.233.201.8, 191.233.200.16     |
| Canadá Central |40.85.224.249     |
| Leste do Canadá | 40.86.226.166     |
| Centro dos EUA | 23.99.160.139, 13.67.215.62     |
| Leste da China 2 | 40.73.82.1     |
| Norte da China 2 | 40.73.50.0     |
| Leste da Ásia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| Leste dos EUA | 40.121.158.30, 191.238.6.43     |
| Leste dos EUA 2 |40.79.84.180, 191.239.224.107, 52.177.185.181     |
| França Central | 40.79.137.0, 40.79.129.1     |
| Alemanha Central | 51.4.144.100     |
| Nordeste da Alemanha | 51.5.144.179     |
| Centro da Índia | 104.211.96.159     |
| Sul da Índia | 104.211.224.146     |
| Oeste da Índia | 104.211.160.80     |
| Leste do Japão | 13.78.61.196, 191.237.240.43     |
| Oeste do Japão | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7     |
| Coreia Central | 52.231.32.42     |
| Sul da Coreia | 52.231.200.86     |
| Centro-Norte dos EUA | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36     |
| Norte da Europa | 40.113.93.91, 191.235.193.75     |
| Norte da África do Sul  | 102.133.152.0     |
| África do Sul Ocidental    | 102.133.24.0     |
| Centro-Sul dos Estados Unidos |13.66.62.124, 23.98.162.75, 20.45.120.0, 104.214.16.39     |
| Sudeste da Ásia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| EAU Central | 20.37.72.64     |
| Norte dos EAU | 65.52.248.0     |
| Sul do Reino Unido | 51.140.184.11     |
| Oeste do Reino Unido | 51.141.8.11     |
| Centro-Oeste dos EUA | 13.78.145.25     |
| Europa Ocidental | 40.68.37.158, 191.237.232.75     |
| Oeste dos EUA | 104.42.238.205, 23.99.34.75     |
| Oeste dos EUA 2 | 13.66.226.202     |
||||

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure](./howto-manage-firewall-portal.md)
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando a CLI do Azure](./howto-manage-firewall-cli.md)
