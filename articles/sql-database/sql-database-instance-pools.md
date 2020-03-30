---
title: Pools de instâncias (visualização)
description: Este artigo descreve os pools de instâncias do Banco de Dados Azure SQL (visualização).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256205"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>O que são pools de instâncias do Banco de Dados SQL (visualização)?

Os pools de instâncias são um novo recurso no Azure SQL Database que fornece uma maneira conveniente e econômica de migrar instâncias SQL menores para a nuvem em escala.

Os pools de instância permitem pré-provisionar os recursos de computação de acordo com os requisitos de migração totais. Em seguida, você pode implantar várias instâncias gerenciadas individuais até seu nível de computação pré-provisionado. Por exemplo, se você pré-provisionar 8 vCores, você pode implantar duas instâncias de 2 vCore e uma 4 vCore e, em seguida, migrar bancos de dados para essas instâncias. Antes da disponibilidade de pools de instâncias, cargas de trabalho menores e menos intensivas em computação muitas vezes teriam que ser consolidadas em uma instância gerenciada maior ao migrar para a nuvem. A necessidade de migrar grupos de bancos de dados para uma instância grande normalmente exigia planejamento cuidadoso de capacidade e governança de recursos, considerações adicionais de segurança e algum trabalho extra de consolidação de dados no nível da instância.

Além disso, os pools de instâncias suportam a integração vnet nativa para que você possa implantar vários pools de instâncias e várias instâncias únicas na mesma sub-rede.


## <a name="key-capabilities-of-instance-pools"></a>Principais recursos de pools de instâncias

Os pools de instâncias fornecem os seguintes benefícios:

1. Capacidade de hospedar 2 instâncias vCore. *Apenas para exemplos em pools de instâncias . \**
2. Tempo de implantação de instâncias previsíveis e rápidas (até 5 minutos).
3. Alocação mínima de endereço IP.

O diagrama a seguir ilustra um pool de instâncias com várias instâncias implantadas em uma sub-rede virtual.

![pool de instâncias com várias instâncias](./media/sql-database-instance-pools/instance-pools1.png)

Os pools de instâncias permitem a implantação de várias instâncias na mesma máquina virtual onde o tamanho da computação da máquina virtual é baseado no número total de vCores alocados para o pool. Essa arquitetura permite a *particionamento* da máquina virtual em várias instâncias, o que pode ser qualquer tamanho suportado, incluindo 2 vCores (2 instâncias vCore estão disponíveis apenas para instâncias em pools).

As operações de gerenciamento em instâncias em um pool são muito mais rápidas quando o pool é implantado inicialmente. Essas operações são mais rápidas porque a implantação ou extensão de um [cluster virtual](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (conjunto dedicado de máquinas virtuais) não faz parte do provisionamento da instância gerenciada.

Como todas as instâncias em um pool compartilham a mesma máquina virtual, a alocação total de IP não depende do número de instâncias implantadas, o que é conveniente para implantação em sub-redes com um intervalo IP estreito.

Cada pool tem uma alocação de IP fixa de apenas nove endereços IP (sem incluir os cinco endereços IP na sub-rede que são reservados para suas próprias necessidades). Para obter detalhes, consulte os requisitos de [tamanho da sub-rede para instâncias individuais](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Cenários de aplicativos para pools por exemplo

A lista a seguir fornece os principais casos de uso em que os pools de instâncias devem ser considerados:

- Migração de *um grupo de instâncias SQL* ao mesmo tempo, onde a maioria é de um tamanho menor (por exemplo, 2 ou 4 vCores).
- Cenários em *que a criação ou o dimensionamento de instâncias previsíveis e curtas* são importantes. Por exemplo, a implantação de um novo inquilino em um ambiente de aplicativo SaaS de vários locatários que requer recursos de nível de instância.
- Cenários em que ter um *custo fixo* ou limite *de gastos* é importante. Por exemplo, executando ambientes compartilhados de teste de demonstração ou demonstração de um tamanho fixo (ou raramente mudando), onde você implanta periodicamente instâncias gerenciadas quando necessário.
- Cenários em que *a alocação mínima de endereçoIP* em uma sub-rede VNet é importante. Todas as instâncias em um pool estão compartilhando uma máquina virtual, de modo que o número de endereços IP alocados é menor do que no caso de instâncias únicas.


## <a name="architecture-of-instance-pools"></a>Arquitetura de pools de instâncias

Os pools de instâncias têm arquitetura semelhante às instâncias gerenciadas regulares *(instâncias únicas).* Para oferecer suporte [a implantações dentro de Redes Virtuais (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) do Azure e para fornecer isolamento e segurança para os clientes, os pools de instâncias também contam com [clusters virtuais](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Os clusters virtuais representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede virtual do cliente.

A principal diferença entre os dois modelos de implantação é que os pools de instâncias permitem várias implantações de processos do SQL Server no mesmo nó de máquina virtual, que são regidos por recursos usando [o Windows Job Objects,](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)enquanto as instâncias únicas estão sempre sozinhas em um nó de máquina virtual.

O diagrama a seguir mostra um pool de instâncias e duas instâncias individuais implantadas na mesma sub-rede e ilustra os principais detalhes arquitetônicos para ambos os modelos de implantação:

![pool de instâncias e duas instâncias individuais](./media/sql-database-instance-pools/instance-pools2.png)

Cada pool de instâncias cria um cluster virtual separado por baixo. As instâncias dentro de um pool e instâncias únicas implantadas na mesma sub-rede não compartilham recursos de computação alocados em processos do SQL Server e componentes de gateway, o que garante a previsibilidade de desempenho.

## <a name="instance-pools-resource-limitations"></a>As limitações de recursos de pools de instâncias

Há várias limitações de recursos em relação a pools de instância e instâncias dentro de pools:

- Os pools de instâncias estão disponíveis apenas no hardware Gen5.
- As instâncias dentro de um pool têm CPU e RAM dedicadas, de modo que o número agregado de vCores em todas as instâncias deve ser menor ou igual ao número de vCores alocados no pool.
- Todos os [limites de nível de instância](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) se aplicam a instâncias criadas dentro de um pool.
- Além dos limites de nível de instância, há também dois limites impostos *no nível de pool de instâncias:*
  - Tamanho total de armazenamento por pool (8 TB).
  - Número total de bancos de dados por pool (100).

A alocação total de armazenamento e o número de bancos de dados em todas as instâncias devem ser menores ou iguais aos limites expostos pelos pools de instâncias.

- Os pools de instâncias suportam 8, 16, 24, 32, 40, 64 e 80 vCores.
- As instâncias gerenciadas dentro dos pools suportam 2, 4, 8, 16, 24, 32, 40, 64 e 80 vCores.
- As instâncias gerenciadas dentro dos pools suportam tamanhos de armazenamento entre 32 GB e 8 TB, exceto:
  - 2 instâncias vCore suportam tamanhos entre 32 GB e 640 GB
  - 4 instâncias vCore suportam tamanhos entre 32 GB e 2 TB

A [propriedade do nível de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) está associada ao recurso pool de instâncias, de modo que todas as instâncias em um pool devem ser a mesma camada de serviço que o nível de serviço do pool. Neste momento, apenas o nível de serviço de Propósito Geral está disponível (veja a seção a seguir sobre limitações na visualização atual).

### <a name="public-preview-limitations"></a>Limitações da visualização pública

A visualização pública tem as seguintes limitações:

- Atualmente, apenas a camada de serviço sustal está disponível.
- Os pools de instâncias não podem ser dimensionados durante a visualização pública, por isso o planejamento cuidadoso da capacidade antes da implantação é importante.
- O suporte ao portal DoZure, por exemplo, para criação e configuração de pool ainda não está disponível. Todas as operações em pools de instâncias são suportadas apenas através do PowerShell. A implantação de instância inicial em um pool pré-criado também é suportada apenas através do PowerShell. Uma vez implantados em um pool, as instâncias gerenciadas podem ser atualizadas usando o portal Azure.
- As instâncias gerenciadas criadas fora do pool não podem ser movidas para um pool existente e as instâncias criadas dentro de um pool não podem ser movidas para fora como uma única instância ou para outra piscina.
- O preço de instância reservado (licença incluída ou com o Azure Hybrid Benefit) não está disponível.

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

As instâncias criadas em pools suportam os mesmos [níveis de compatibilidade e recursos suportados em instâncias gerenciadas únicas](sql-database-managed-instance.md#sql-features-supported).

Cada instância gerenciada implantada em um pool tem uma instância separada do SQL Agent.

Os recursos ou recursos opcionais que exigem que você escolha valores específicos (como colagem em nível de instância, fuso horário, ponto final público para tráfego de dados, grupos de failover) são configurados no nível de instância e podem ser diferentes para cada instância em um pool.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Embora as instâncias gerenciadas dentro dos pools tenham vCore e RAM dedicados, eles compartilham recursos de disco local (para uso temporário) e rede. Não é provável, mas é possível experimentar o efeito *vizinho barulhento* se várias instâncias no pool tiverem alto consumo de recursos ao mesmo tempo. Se você observar esse comportamento, considere implantar essas instâncias em um pool maior ou em instâncias únicas.

## <a name="security-considerations"></a>Considerações sobre segurança

Como as instâncias implantadas em um pool compartilham a mesma máquina virtual, você pode considerar desativar recursos que introduzem riscos de segurança mais altos ou controlar firmemente as permissões de acesso a esses recursos. Por exemplo, integração CLR, backup e restauração nativas, e-mail de banco de dados, etc.

## <a name="instance-pool-support-requests"></a>Solicitações de suporte ao pool de instâncias

Crie e gerencie solicitações de suporte para pools de exemplo no [portal Azure](https://portal.azure.com).

Se você estiver enfrentando problemas relacionados à implantação do pool de instâncias (criação ou exclusão), certifique-se de especificar **Pools de instâncias** no campo **do subtipo Problema.**

![solicitação de suporte a pools de instâncias](./media/sql-database-instance-pools/support-request.png)

Se você estiver enfrentando problemas relacionados a instâncias únicas ou bancos de dados dentro de um pool, você deve criar um ticket de suporte regular para instâncias gerenciadas do Azure SQL Database.

Para criar implantações de instânciagerenciada maiores (com ou sem pools de instâncias), você pode precisar obter uma cota regional maior. Para obter mais informações, consulte [Solicitar aumentos de cotas para o Banco de Dados SQL do Azure](quota-increase-request.md). Observe que se você estiver usando pools de instâncias, a lógica de implantação compara o consumo total de vCore *no nível de pool* em comparação com a sua cota para determinar se você está autorizado a criar novos recursos sem aumentar ainda mais sua cota.

## <a name="instance-pool-billing"></a>Faturamento de pool de instâncias

Os pools de instâncias permitem dimensionar a computação e o armazenamento de forma independente. Os clientes pagam pelo cálculo associado ao recurso de pool medido em vCores, e o armazenamento associado a todas as instâncias medidas em gigabytes (os primeiros 32 GB são gratuitos para todas as instâncias).

O preço do vCore para um pool é cobrado independentemente de quantas instâncias são implantadas nesse pool.

Para o preço de Computação (medido em vCores), duas opções de preços estão disponíveis:

  1. *Licença incluída*: O preço das licenças SQL está incluído. Isso é para os clientes que optarem por não aplicar as licenças existentes do SQL Server com o Software Assurance.
  2. *Azure Hybrid Benefit*: Um preço reduzido que inclui o Azure Hybrid Benefit para o SQL Server. Os clientes podem optar por esse preço usando suas licenças sql server existentes com garantia de software. Para obter elegibilidade e outros detalhes, consulte [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Definir diferentes opções de preços não é possível para instâncias individuais em um pool. Todas as instâncias no pool pai devem estar no preço incluído da licença ou no preço do Benefício Híbrido Azure. O modelo de licença para o pool pode ser alterado após a criação da piscina.

> [!IMPORTANT]
> Se você especificar um modelo de licença para o caso diferente do pool, o preço do pool será usado e o valor do nível de ocorrência será ignorado.

Se você criar pools de instâncias em [assinaturas elegíveis para o benefício do teste de dev,](https://azure.microsoft.com/pricing/dev-test/)você receberá automaticamente taxas com desconto de até 55% na instância gerenciada do Azure SQL.

Para obter detalhes completos sobre os preços do pool de instâncias, consulte a seção *pools de instâncias* na [página de preços de instância gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Próximas etapas

- Para começar com pools de instâncias, consulte [a instância sql database pools de como orientar](sql-database-instance-pools-how-to.md).
- Para saber como criar sua primeira instância gerenciada, consulte [o guia Quickstart](sql-database-managed-instance-get-started.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração do VNet, consulte [a configuração vnet de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Para uma partida rápida que cria uma instância gerenciada e restaura um banco de dados a partir de um arquivo de backup, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o DMS (Azure Database Migration Service, serviço de migração de banco de dados do Azure) para migração, consulte [a migração de instâncias gerenciadas usando o DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para obter um monitoramento avançado do desempenho do banco de dados de instância sacada gerenciada com inteligência incorporada para solução de [problemas, consulte Monitor Azure SQL Database usando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte o preço de [instância gerenciada do SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
