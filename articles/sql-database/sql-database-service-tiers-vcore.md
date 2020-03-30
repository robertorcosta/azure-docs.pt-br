---
title: Visão geral do modelo vCore
description: O modelo de compra vCore permite que você dimensione independentemente os recursos de computação e armazenamento, combine o desempenho no local e otimize o preço.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481075"
---
# <a name="vcore-model-overview"></a>Visão geral do modelo vCore

O modelo de núcleo virtual (vCore) oferece vários benefícios:

- Maior computação, memória, IO e limites de armazenamento.
- Controle sobre a geração de hardware para melhor corresponder aos requisitos de computação e memória da carga de trabalho.
- Descontos de preços para [o Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) e [a Instância Reservada (RI)](sql-database-reserved-capacity.md).
- Maior transparência nos detalhes de hardware que alimentam o cálculo; facilita o planejamento de migrações de implantações locais.

## <a name="service-tiers"></a>Camadas de serviço

As opções de nível de serviço no modelo vCore incluem Propósito Geral, Business Critical e Hyperscale. O nível de serviço geralmente define a arquitetura de armazenamento, os limites de espaço e IO e as opções de continuidade de negócios relacionadas à disponibilidade e recuperação de desastres.

||**Propósito geral**|**Negócios críticos**|**Hiperescala**|
|---|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de computação e armazenamento orientadas ao orçamento, equilibradas e escaláveis. |Oferece aos aplicativos de negócios a maior resiliência às falhas usando várias réplicas isoladas e fornece o maior desempenho de I/O por réplica de banco de dados.|A maioria das cargas de trabalho de negócios com requisitos de armazenamento e escala de leitura altamente escaláveis.  Oferece maior resiliência às falhas, permitindo a configuração de mais de uma réplica isolada do banco de dados. |
|Armazenamento|Usa armazenamento remoto.<br/>**Bancos de dados únicos e piscinas elásticas provisionadas computação:**<br/>5 GB – 4 TB<br/>**Cálculo sem servidor:**<br/>5 GB - 3 TB<br/>**Instância gerenciada**: 32 GB - 8 TB |Usa armazenamento SSD local.<br/>**Bancos de dados únicos e piscinas elásticas provisionadas computação:**<br/>5 GB – 4 TB<br/>**Instância gerenciada:**<br/>32 GB - 4 TB |Crescimento automático flexível de armazenamento conforme necessário. Suporta até 100 TB de armazenamento. Usa armazenamento SSD local para cache local de pool de buffer e armazenamento de dados local. Usa o armazenamento remoto do Azure como armazenamento final de dados a longo prazo. |
|IOPS e throughput (aproximado)|**Bancos de dados únicos e piscinas elásticas**: Consulte os limites de recursos para [bancos de dados únicos](../sql-database/sql-database-vcore-resource-limits-single-databases.md) e [piscinas elásticas](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Instância gerenciada**: Consulte [A visão geral Do banco de dados Azure SQL gerenciado limites de recurso](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)de instância .|Consulte os limites de recursos para [bancos de dados únicos](../sql-database/sql-database-vcore-resource-limits-single-databases.md) e [piscinas elásticas](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Hyperscale é uma arquitetura de várias camadas com cache em vários níveis. IOPS eficaz e throughput dependerá da carga de trabalho.|
|Disponibilidade|1 réplica, sem réplicas em escala de leitura|3 réplicas, 1 [réplica em escala de leitura](sql-database-read-scale-out.md),<br/>zona redundante de alta disponibilidade (HA)|1 réplica de leitura-gravação, mais 0-4 [réplicas em escala de leitura](sql-database-read-scale-out.md)|
|Backups|[Armazenamento geo-redundante de acesso de leitura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|Backups baseados em snapshot no armazenamento remoto Azure. As restaurações usam esses instantâneos para recuperação rápida. Os backups são instantâneos e não afetam o desempenho de I/O computacional. As restaurações são rápidas e não são uma operação de tamanho de dados (levando minutos em vez de horas ou dias).|
|Na memória|Sem suporte|Com suporte|Sem suporte|
|||


### <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

Para obter informações sobre a seleção de um nível de serviço para sua carga de trabalho específica, consulte os seguintes artigos:

- [Quando escolher o nível de serviço de propósito geral](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando escolher o nível de serviço Business Critical](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando escolher o nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Níveis de computação

As opções de camada de cálculo no modelo vCore incluem os níveis de computação provisionados e sem servidor.


### <a name="provisioned-compute"></a>Computação provisionada

O nível de computação provisionado fornece uma quantidade específica de recursos computacionais que são continuamente provisionados independentemente da atividade de carga de trabalho, e contas para a quantidade de cálculo provisionado a um preço fixo por hora.


### <a name="serverless-compute"></a>Computação sem servidor

O [nível de computação sem servidor](sql-database-serverless.md) escala automaticamente os recursos de computação com base na atividade de carga de trabalho e as contas da quantidade de cálculo usada por segundo.



## <a name="hardware-generations"></a>Gerações de hardware

As opções de geração de hardware no modelo vCore incluem Gen 4/5, Série M (pré-visualização) e série Fsv2 (pré-visualização). A geração de hardware geralmente define os limites de computação e memória e outras características que afetam o desempenho da carga de trabalho.

### <a name="gen4gen5"></a>Gen4/Gen5

- O hardware Gen4/Gen5 fornece recursos balanceados de computação e memória, e é adequado para a maioria das cargas de trabalho de banco de dados que não têm memória mais alta, vCore mais alto ou requisitos de vCore único mais rápido, conforme fornecido pela série Fsv2 ou série M.

Para regiões onde o Gen4/Gen5 está disponível, consulte [a disponibilidade de Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Série Fsv2 (pré-visualização)

- A série Fsv2 é uma opção de hardware otimizada para computação que oferece baixa latência da CPU e alta velocidade de clock para as cargas de trabalho mais exigentes da CPU.
- Dependendo da carga de trabalho, a série Fsv2 pode oferecer mais desempenho de CPU por vCore do que o Gen5, e o tamanho de 72 vCore pode fornecer mais desempenho de CPU por menos custo do que 80 vCores no Gen5. 
- Fsv2 fornece menos memória e tempdb por vCore do que outros hardwares, de modo que cargas de trabalho sensíveis a esses limites podem querer considerar a série Gen5 ou M em vez disso.  

Série Fsv2 em apenas suportado no nível De Propósito Geral.  Para as regiões onde a série Fsv2 está disponível, consulte [a disponibilidade da série Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Série M (pré-visualização)

- A série M é uma opção de hardware otimizada de memória para cargas de trabalho que exigem mais memória e limites de computação mais altos do que os fornecidos pelo Gen5.
- A série M fornece 29 GB por vCore e 128 vCores, o que aumenta o limite de memória em relação ao Gen5 em 8x para quase 4 TB.

A série M é suportada apenas no nível Business Critical e não suporta redundância de zona.

Para habilitar o hardware da série M para uma assinatura e região, uma solicitação de suporte deve ser aberta. A assinatura deve ser um tipo de oferta paga, incluindo Pay-As-You-Go ou Enterprise Agreement (EA).  Se a solicitação de suporte for aprovada, a experiência de seleção e provisionamento da série M seguirá o mesmo padrão de outras gerações de hardware. Para regiões onde a série M está disponível, consulte [a disponibilidade da série M](#m-series).


### <a name="compute-and-memory-specifications"></a>Especificações de computação e memória


|Geração de hardware  |Computação  |Memória  |
|:---------|:---------|:---------|
|Gen4     |- Processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz<br>- Provisão até 24 vCores (1 vCore = 1 núcleo físico)  |- 7 GB por vCore<br>- Provisão até 168 GB|
|Gen5     |**Computação provisionada**<br>- Processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake)*<br>- Provisão até 80 vCores (1 vCore = 1 hiper-thread)<br><br>**Computação sem servidor**<br>- Processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake)*<br>- Escala automática até 16 vCores (1 vCore = 1 hiper-thread)|**Computação provisionada**<br>- 5,1 GB por vCore<br>- Provisão até 408 GB<br><br>**Computação sem servidor**<br>- Escala automática de até 24 GB por vCore<br>- Escala automática até 48 GB máx|
|Série Fsv2     |- Processadores Intel Xeon Platinum 8168 (SkyLake)<br>- Apresentando uma velocidade sustentada de relógio turbo de 3,4 GHz e uma velocidade máxima de clock turbo de núcleo único de 3,7 GHz.<br>- Provisão 72 vCores (1 vCore = 1 hiper-thread)|- 1,9 GB por vCore<br>- Provisão 136 GB|
|Série M     |- Processadores Intel Xeon E7-8890 v3 de 2,5 GHz<br>- Provisão 128 vCores (1 vCore = 1 hiper-thread)|- 29 GB por vCore<br>- Provisão 3,7 TB|

\*Na visão de gerenciamento dinâmico [sys.dm_user_db_resource_governance,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) a geração de hardware para bancos de dados Gen5 usando processadores Intel SP-8160 (Skylake) aparece como Gen6. Os limites de recursos para todos os bancos de dados Gen5 são os mesmos, independentemente do tipo de processador (Broadwell ou Skylake).

Para obter mais informações sobre os limites de recursos, consulte [limites de recursos para bancos de dados únicos (vCore)](sql-database-vcore-resource-limits-single-databases.md)ou limites de recursos para [pools elásticos (vCore)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selecionando uma geração de hardware

No portal Azure, você pode selecionar a geração de hardware para um banco de dados SQL ou pool no momento da criação, ou pode alterar a geração de hardware de um banco de dados ou pool SQL existente.

**Para selecionar uma geração de hardware ao criar um banco de dados ou pool SQL**

Para obter informações detalhadas, consulte [Criar um banco de dados SQL](sql-database-single-database-get-started.md).

Na guia **Noções** básicas, selecione o link **Configurar banco de dados** na seção **Computação + armazenamento** e, em seguida, selecione o link **Configuração Alterar:**

  ![configurar o banco de dados](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Selecione a geração de hardware desejada:

  ![selecionar hardware](media/sql-database-service-tiers-vcore/select-hardware.png)


**Para alterar a geração de hardware de um banco de dados ou pool SQL existente**

Para um banco de dados, na página Visão geral, selecione o link **de nível de preços:**

  ![alterar hardware](media/sql-database-service-tiers-vcore/change-hardware.png)

Para um pool, na página Visão geral, **selecione Configurar**.

Siga as etapas para alterar a configuração e selecione a geração de hardware conforme descrito nas etapas anteriores.

**Para selecionar uma geração de hardware ao criar uma instância gerenciada**

Para obter informações [detalhadas,](sql-database-managed-instance-get-started.md)consulte Criar uma instância gerenciada .

Na guia **Basics,** selecione o link **Configurar banco de dados** na seção **Computação + armazenamento** e, em seguida, selecione a geração de hardware desejada:

  ![configurar instância gerenciada](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Para alterar a geração de hardware de uma instância gerenciada existente**

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na página de instância gerenciada, selecione o link **de nível de preços** colocado na seção Configurações

![alterar hardware instância gerenciada](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Na página **de nível de preços,** você poderá alterar a geração de hardware conforme descrito nas etapas anteriores.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Use o seguinte script do PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Para obter mais detalhes, verifique o comando [Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Use o seguinte comando CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Para obter mais detalhes, verifique o comando [az sql mi update.](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

---

### <a name="hardware-availability"></a>Disponibilidade de hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

O hardware [Gen4](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) está sendo eliminado e não está mais disponível para as novas implantações. Todos os novos bancos de dados devem ser implantados no hardware Gen5.

Gen5 está disponível na maioria das regiões do mundo.

#### <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 está disponível nas seguintes regiões: Austrália Central, Austrália Central 2, Austrália Oriental, Austrália Sudeste, Brasil Sul, Canadá Central, Leste da Ásia, Leste dos Estados Unidos, França Central, Índia Central, Índia Ocidental, Coréia Central, Coréia do Sul, Norte Europa, África do Sul Norte, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Oeste dos Estados Unidos 2.


#### <a name="m-series"></a>Série M

A série M está disponível nas seguintes regiões: Leste dos EUA, Europa Do Norte, Europa Ocidental, OESTE DOS EUA 2.
As séries M também podem ter disponibilidade limitada em regiões adicionais. Você pode solicitar uma região diferente da listada aqui, mas o cumprimento em uma região diferente pode não ser possível.

Para habilitar a disponibilidade da série M em uma assinatura, o acesso deve ser solicitado [mediante a apresentação de uma nova solicitação de suporte](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Crie uma solicitação de suporte para habilitar sérieS M: 

1. Selecione **Ajuda + suporte** no portal.
2. Selecione **Nova solicitação de suporte**.

Na página **Básico,** forneça o seguinte:

1. Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)**.
2. Para **assinatura** = selecione a assinatura para ativar a série M.
3. Para **o tipo Quota,** selecione **banco de dados SQL**.
4. Selecione **Ao lado** para ir à página **Detalhes.**

Na página **Detalhes,** forneça o seguinte:

1. Na seção **DETALHES DO PROBLEMA,** selecione o link **Fornecer detalhes.** 
2. Para **o tipo de cota sql database** selecione **séries M**.
3. Para **Região,** selecione a região para habilitar sérieS M.
    Para regiões onde a série M está disponível, consulte [a disponibilidade da série M](#m-series).

As solicitações de suporte aprovadas são normalmente atendidas dentro de 5 dias úteis.


## <a name="next-steps"></a>Próximas etapas

- Para criar um banco de dados SQL, consulte [Criando um banco de dados SQL usando o portal Azure](sql-database-single-database-get-started.md).
- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados únicos, consulte [os limites de recursos baseados no SQL Database vCore para bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md).
- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados no SQL Database vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).
- Para obter detalhes sobre preços, consulte a página de preços do [Banco de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).
