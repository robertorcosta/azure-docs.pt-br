---
title: FAQ - Serviço de Migração de Banco de Dados Azure
description: Perguntas frequentes sobre o uso do Azure Database Migration Service para executar migrações de banco de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650974"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Perguntas frequentes sobre o uso do Serviço de Migração de Banco de Dados do Azure

Este artigo lista perguntas comumente feitas sobre o uso do Azure Database Migration Service juntamente com respostas relacionadas.

## <a name="overview"></a>Visão geral

**P. O que é o Serviço de Migração de Banco de Dados do Azure?**
O Azure Database Migration Service é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados Do Azure com tempo mínimo de inatividade. O serviço está atualmente em Disponibilidade Geral, com esforços contínuos de desenvolvimento focados em:

* Confiabilidade e desempenho.
* Adição iterativa de pares de fonte-destino.
* Investimento contínuo em migrações sem conflitos.

**P. Quais pares de origem/destino o Azure Database Migration Service atualmente suporta?**
Atualmente, o serviço suporta uma variedade de pares de origem/destino ou cenários de migração. Para obter uma listagem completa do status de cada cenário de migração disponível, consulte o artigo [Status dos cenários de migração com suporte pelo Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Outros cenários de migração estão em pré-visualização e exigem o envio de uma nomeação através do site de visualização do DMS. Para obter uma lista completa dos cenários na pré-visualização e para se inscrever para participar de uma dessas ofertas, consulte o [site DMS Preview](https://aka.ms/dms-preview/).

**P. Quais versões do SQL Server o Azure Database Migration Service faz como fonte?**
Ao migrar do SQL Server, as fontes suportadas para o Azure Database Migration Service são o SQL Server 2005 até o SQL Server 2019.

**P: Ao usar o Azure Database Migration Service, qual é a diferença entre um offline e uma migração on-line?**
Você pode usar o Azure Database Migration Service para executar migrações offline e on-line. Com uma migração *offline,* o tempo de inatividade do aplicativo começa quando a migração é iniciada. Com uma migração *on-line,* o tempo de inatividade é limitado ao tempo de corte no final da migração. Sugerimos que você teste uma migração offline para determinar se o tempo de inatividade é aceitável; caso contrário, faça uma migração online.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure.

**P. Como o Azure Database Migration Service se compara a outras ferramentas de migração de banco de dados da Microsoft, como o DMA (Database Migration Assistant, assistente de migração de banco de dados) ou o SSMA (SSMA)?**
O Azure Database Migration Service é o método preferido para migração de banco de dados para o Microsoft Azure em escala. Para obter mais detalhes sobre como o Azure Database Migration Service se compara a outras ferramentas de migração de banco de dados da Microsoft e para recomendações sobre o uso do serviço para vários cenários, consulte o blog [postando Diferenciando as Ferramentas e Serviços de Migração de Banco de Dados da Microsoft.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)

**P. Como o Serviço de Migração de Banco de Dados do Azure se compara à oferta do Azure Migrate?**
O Azure Migrate auxilia na migração de máquinas virtuais no local para o Azure IaaS. O serviço avalia a adequação da migração e o dimensionamento com base no desempenho, e fornece estimativas de custo para a execução das máquinas virtuais locais no Azure. As Migrações para Azure são úteis para migrações de lift-and-shift de cargas de trabalho baseadas em VM local para VMs de IaaS do Azure. No entanto, ao contrário do Azure Database Migration Service, o Azure Migrate não é uma oferta especializada de serviçode migração de banco de dados para plataformas de banco de dados relacionais Azure PaaS, como o Azure SQL Database ou o Azure SQL Database Managed Instance.

## <a name="setup"></a>Instalação

**P. Quais são os pré-requisitos para usar o Serviço de Migração de Banco de Dados Do Azure?**
Existem vários pré-requisitos necessários para garantir que o Azure Database Migration Service seja executado sem problemas ao executar migrações de banco de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de origem e destino) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração compatíveis incluem a necessidade de:

* Crie uma rede virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade local a local aos seus servidores de origem no local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Certifique-se de que as regras do Grupo de Segurança de Rede de Rede de rede virtual não bloqueiem as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego DE Rede Virtual NSG, consulte o artigo [Filtrar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)o tráfego da rede com grupos de segurança da rede .
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.

Para obter uma lista de todos os pré-requisitos necessários para competir com cenários de migração específicos usando o Azure Database Migration Service, consulte os tutoriais relacionados na [documentação](https://docs.microsoft.com/azure/dms/dms-overview) do Azure Database Migration Service sobre docs.microsoft.com.

**P. Como encontrar o endereço IP para o Azure Database Migration Service para que eu possa criar uma lista de opções para as regras de firewall usadas para acessar meu banco de dados de origem para migração?**
Você pode precisar adicionar regras de firewall permitindo que o Azure Database Migration Service acesse seu banco de dados de origem para migração. O endereço IP do serviço é dinâmico, mas se você estiver usando o ExpressRoute, este endereço é atribuído privadamente pela sua rede corporativa. A maneira mais fácil de identificar o endereço IP apropriado é procurar no mesmo grupo de recursos que o recurso provisionado Azure Database Migration Service para encontrar a Interface de Rede associada. Normalmente, o nome do recurso de Adaptador de Rede começa com o prefixo de NIC e é seguido por um caractere único e sequência numérica, por exemplo NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar esse recurso de interface de rede, você pode ver o endereço IP que deve ser incluído na lista de permissões na página do portal do Azure para visão de geral de recursos.

Talvez você precise incluir a origem da porta que o SQL Server está escutando na lista de permissões. Por padrão, é a porta 1433, mas o SQL Server de origem também pode estar configurado para escutar outras portas. Nesse caso, você também precisa incluir as portas na lista de permissões. Você pode determinar a porta que o SQL Server está escutando usando uma consulta de modo de exibição de Gerenciamento Dinâmico:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Você também pode determinar a porta que o SQL Server está escutando consultando o log de erros do SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**P. Como configuro uma Rede Virtual Microsoft Azure?**
Enquanto vários tutoriais da Microsoft que podem levá-lo através do processo de configuração de uma rede virtual, a documentação oficial aparece no artigo [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Uso

**P. Qual é o resumo das etapas necessárias para usar o Azure Database Migration Service para realizar uma migração de banco de dados?**
Durante uma migração de banco de dados típica e simples, você:

1. Cria um banco de dados de destino.
2. Avalie seu banco de dados de origem.
    * Para migrações homogêneas, avalie seu banco de dados existente usando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Para migrações heterogêneas (a partir de fontes de concorrência), avalie seu banco de dados existente com [o SSMA](https://aka.ms/get-ssma). Você também usa o SSMA para converter objetos de banco de dados e migrar o esquema para sua plataforma de destino.
3. Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
4. Crie um projeto de migração especificando o banco de dados de origem, o banco de dados de destino e as tabelas a migrar.
5. Comece a carga completa.
6. Escolhe a validação subsequente.
7. Execute uma mudança manual do seu ambiente de produção para o novo banco de dados baseado em nuvem.

## <a name="troubleshooting-and-optimization"></a>Solução e otimização de problemas

**P. Estou criando um projeto de migração no DMS, e estou tendo dificuldade em me conectar ao meu banco de dados de origem. O que eu devo fazer?**
Se você tiver problemas para se conectar ao seu sistema de banco de dados de origem enquanto trabalha na migração, crie uma máquina virtual na rede virtual com a qual você configura sua instância de DMS. Na máquina virtual, você deve ser capaz de executar um teste de conexão, como usar um arquivo UDL para testar uma conexão com o SQL Server ou baixar robo 3T para testar conexões MongoDB. Se o teste de conexão for bem-sucedido, você não terá problemas em se conectar ao seu banco de dados de origem. Se o teste de conexão não for bem-sucedido, entre em contato com o administrador da rede.

**P. Por que meu Serviço de Migração de Banco de Dados Do Azure não está disponível ou parado?**
Se o usuário interromper explicitamente o Azure Database Migration Service (DMS) ou se o serviço estiver inativo por um período de 24 horas, o serviço estará em estado de parada ou pausa automática. Em cada caso, o serviço estará indisponível e em status parado.  Para retomar as migrações ativas, reinicie o serviço.

**P. Existem recomendações para otimizar o desempenho do Serviço de Migração de Banco de Dados do Azure?**
Você pode fazer algumas coisas para acelerar a sua migração de banco de dados usando o serviço:

* Use os vários tipos de preços de uso geral da CPU ao criar sua instância de serviço para permitir que o serviço aproveite as várias vCPUs para paralelização e transferência de dados mais rápida.
* Temporariamente, escale verticalmente a sua instância de destino de banco de dados SQL do Azure para o SKU da camada Premium durante a operação de migração de dados para minimizar a limitação do banco de dados SQL do Azure que pode afetar as atividades de transferência de dados ao usar SKUs de nível inferior.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional, consulte o artigo [ O que é o Serviço de Migração de Banco de Dados do Azure ](dms-overview.md).
