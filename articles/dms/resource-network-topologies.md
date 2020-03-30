---
title: Topoologias de rede para migrações de instâncias gerenciadas pelo SQL
titleSuffix: Azure Database Migration Service
description: Aprenda as configurações de origem e destino para migrações de instâncias gerenciadas do Azure SQL Database usando o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254953"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topoologias de rede para migrações de instância gerenciada do Azure SQL DB usando o Serviço de Migração de Banco de Dados do Azure

Este artigo discute várias topologias de rede com as quais o Azure Database Migration Service pode trabalhar para fornecer uma experiência de migração abrangente de servidores SQL locais para instância gerenciada do banco de dados Azure SQL.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instância Gerenciada do Banco de Dados SQL do Azure configurada para cargas de trabalho Híbridas 

Use essa topologia se a Instância Gerenciada do Banco de Dados SQL do Azure está conectada à rede local. Essa abordagem fornece o roteamento de rede mais simplificado e gera a taxa máxima de transferência de dados durante a migração.

![Topologia de rede para cargas de trabalho híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- Nesse cenário, a instância gerenciada do Banco de Dados SQL do Azure e a instância do Serviço de Migração de Banco de Dados do Azure são criadas na mesma Rede Virtual do Microsoft Azure, mas usam sub-redes diferentes.  
- A rede virtual utilizada neste cenário também está conectada à rede local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Instância Gerenciada do Banco de Dados SQL do Azure isolada da rede local

Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:

- A instância gerenciada do Banco de Dados SQL do Azure está isolada da conectividade no local, mas a instância do Serviço de Migração do Banco de Dados do Azure está conectada à rede local.
- Se as políticas rbac (Role Based Access Control, controle de acesso baseado em função) estiverem em vigor e você precisar limitar os usuários a acessar a mesma assinatura que está hospedando a instância gerenciada do Banco de Dados SQL do Azure.
- As redes virtuais usadas para a Instância Gerenciada do Banco de Dados Azure SQL e o Serviço de Migração de Banco de Dados Do Azure estão em diferentes assinaturas.

![Topologia de rede da Instância Gerenciada isolada da rede local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- A rede virtual que o Azure Database Migration Service usa para este cenário tambémhttps://docs.microsoft.com/azure/expressroute/expressroute-introduction) deve ser conectada à rede local usando (ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configure o [peering de rede VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a rede virtual usada para a instância gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrações nuvem-nuvem: rede virtual compartilhada

Use essa topologia se o SQL Server de origem estiver hospedado em uma VM do Azure e compartilhar a mesma rede virtual com a instância gerenciada do Banco de Dados Azure SQL e o Serviço de Migração do Banco de Dados Azure.

![Topologia de rede para migrações cloud-to-cloud com um VNet compartilhado](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- Nenhum requisito adicional.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrações de nuvem para nuvem: rede virtual isolada

Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:

- A instância gerenciada do Banco de Dados SQL do Azure é provisionada em uma rede virtual isolada.
- Se as políticas rbac (Role Based Access Control, controle de acesso baseado em função) estiverem em vigor e você precisar limitar os usuários a acessar a mesma assinatura que está hospedando a instância gerenciada do Banco de Dados SQL do Azure.
- As redes virtuais usadas para a instância gerenciada do banco de dados Azure SQL e para o Serviço de Migração de Banco de Dados Do Azure estão em diferentes assinaturas.

![Topologia de rede para migrações cloud-to-cloud com um VNet isolado](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Configure o [peering de rede VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a rede virtual usada para a instância gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados Azure.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOME**   | **Porta** | **Protocolo** | **Fonte** | **Destino** | **Ação** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | DMS SUBNET | Qualquer             | Allow      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOME**                  | **Porta**                                              | **Protocolo** | **Fonte** | **Destino**           | **Ação** | **Motivo para a regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| gerenciamento                | 443,9354                                              | TCP          | Qualquer        | Qualquer                       | Allow      | Comunicação de plano de gerenciamento através do service bus e armazenamento de blob Azure. <br/>(Se o emparelhamento da Microsoft é habilitado, você talvez não precise essa regra.)                                                             |
| Diagnósticos               | 12000                                                 | TCP          | Qualquer        | Qualquer                       | Allow      | DMS usa a regra para coletar informações de diagnóstico para fins de solução de problemas.                                                                                                                      |
| Servidor de origem SQL         | 1433 (ou porta TCP IP que o SQL Server está escutando) | TCP          | Qualquer        | Espaço de endereço local | Allow      | Conectividade de origem do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não seja necessário usar essa regra.)                                                                                       |
| Instância nomeada do SQL Server | 1434                                                  | UDP          | Qualquer        | Espaço de endereço local | Allow      | Conectividade de origem de instância nomeada do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não seja necessário usar essa regra.)                                                                        |
| Compartilhamento SMB                 | 445                                                   | TCP          | Qualquer        | Espaço de endereço local | Allow      | Compartilhamento de rede SMB para que o DMS armazene arquivos de backup de banco de dados para migrações para o MI do Banco de Dados SQL do Azure e os SQL Servers na VM do Azure <br/>(Se você tiver conectividade site a site, talvez não seja necessária essa regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Confira também

- [Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Visão geral dos pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Crie uma rede virtual usando o portal Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](dms-overview.md)
- Para obter informações atuais sobre a disponibilidade regional do Serviço de Migração de Banco de Dados Do Azure, consulte os Produtos disponíveis pela página [da região.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)
