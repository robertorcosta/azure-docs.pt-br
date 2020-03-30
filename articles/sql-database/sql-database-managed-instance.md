---
title: Visão geral da instância gerenciada pelo SQL
description: Este artigo descreve a instância gerenciada do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 01/21/2020
ms.openlocfilehash: b9fdd1b25e53e1cdc8aa76564304a61adaa8d804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268776"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>O que é a instância gerenciada do Azure SQL Database?

A instância gerenciada é uma nova opção de implantação do Banco de Dados SQL do Azure, fornecendo quase 100% de compatibilidade com o mais recente Mecanismo de Banco de Dados do SQL Server (Enterprise Edition) local, fornecendo uma implementação de [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) nativa que aborda questões de segurança comuns e um [modelo corporativo](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes do SQL Server local. O modelo de implantação de instância gerenciada permite que os clientes do SQL Server existentes façam lift-and-shift dos aplicativos locais para a nuvem com alterações mínimas do banco de dados e aplicativo. Ao mesmo tempo, a opção de implantação da instância gerenciada preserva todas as funcionalidades de PaaS (aplicação automática de patches e atualizações de versão, [backups automatizados](sql-database-automated-backups.md), [alta disponibilidade](sql-database-high-availability.md) ), reduzindo drasticamente a sobrecarga de gerenciamento e TCO.

> [!IMPORTANT]
> Para obter uma lista de regiões em que a opção de implantação de instância gerenciada está disponível no momento, veja [regiões com suporte](sql-database-managed-instance-resource-limits.md#supported-regions).

O diagrama a seguir apresenta os principais recursos da instância gerenciada:

![principais recursos](./media/sql-database-managed-instance/key-features.png)

O modelo de implantação da instância gerenciada foi desenvolvida para clientes que desejam migrar um grande número de aplicativos do ambiente local ou IaaS, autocompilados, ou fornecido pelo ISV para o ambiente de nuvem de PaaS totalmente gerenciado, com o menor esforço de migração possível. Usando o [DMS (Data Migration Service, serviço de migração de dados)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) totalmente automatizado no Azure, os clientes podem levantar e deslocar seu SQL Server no local para uma instância gerenciada que ofereça compatibilidade com o SQL Server no local e isolamento completo das instâncias do cliente com suporte nativo ao VNet.  Com o Software Assurance, você pode trocar suas licenças existentes por taxas com desconto em uma instância gerenciada usando o [Azure Hybrid Benefit para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  A instância gerenciada é o melhor destino da migração na nuvem para instâncias do SQL Server que exigem alta segurança e uma superfície de programação avançada.

A opção de implantação da instância gerenciada tem como objetivo entregar aproximadamente 100% de compatibilidade de área de superfície com a última versão do SQL Server local por meio de um plano de lançamento em etapas.

Para decidir entre as opções de implantação do Banco de Dados SQL do Azure: banco de dados individual, banco de dados em pool e instância gerenciada e SQL Server hospedado em máquina virtual. Veja [Como escolher a versão correta do SQL Server na nuvem do Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Principais recursos e capacidades

A instância gerenciada combina os melhores recursos disponíveis no Banco de Dados SQL do Azure e no Mecanismo de Banco de Dados do SQL Server.

> [!IMPORTANT]
> Uma instância gerenciada executa com todos os recursos da versão mais recente do SQL Server, incluindo operações online, correções de plano automático e outros aprimoramentos de desempenho do enterprise. Uma comparação dos recursos disponíveis é explicada em [Comparação de recursos: Banco de Dados SQL do Azure versus SQL Server](sql-database-features.md).

| **Benefícios de PaaS** | **Continuidade de negócios** |
| --- | --- |
|Sem gerenciamento e compra de hardware <br>Sem sobrecarga de gerenciamento para gerenciar infraestrutura subjacente <br>Rápido provisionamento e dimensionamento de serviço <br>Aplicação de patch automatizado e atualização da versão <br>Integração com outros serviços de dados PaaS |99,99% do SLA de tempo de atividade  <br>Compilado em [alta disponibilidade](sql-database-high-availability.md) <br>Dados protegidos com [backups automatizados](sql-database-automated-backups.md) <br>Período de retenção de backup configurável pelo cliente <br>[Backups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) iniciados pelo usuário <br>Capacidade de [restauração pontual do banco de dados](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gerenciamento**|
|Ambiente isolado[(integração VNet,](sql-database-managed-instance-connectivity-architecture.md)serviço de inquilino único, computação dedicada e armazenamento) <br>[Criptografia de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação do Azure AD](sql-database-aad-authentication.md), suporte de logon único <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Principais do servidor Azure AD (logins)</a>  <br>Cumpre os padrões de conformidade assim como o Banco de Dados SQL do Azure <br>[Auditoria do SQL](sql-database-managed-instance-auditing.md) <br>[Proteção Avançada contra Ameaças](sql-database-managed-instance-threat-detection.md) |API do Azure Resource Manager para automatizar o dimensionamento e provisionamento do serviço <br>Funcionalidade do Portal do Azure para dimensionamento e provisionamento manual do serviço <br>Serviço de Migração de Dados

> [!IMPORTANT]
> O Banco de Dados SQL do Azure (todas as opções de implantação) foi certificado com uma série de padrões de conformidade. Para obter mais informações, consulte o [Microsoft Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do Banco de Dados SQL.

Os principais recursos de instâncias gerenciadas são mostrados na tabela a seguir:

|Recurso | Descrição|
|---|---|
| Compilação/versão do SQL Server | Mecanismo de BD do SQL Server (estável mais recente) |
| Backups automatizados gerenciados | Sim |
| Métricas e monitoramento de banco de dados e instância interna | Sim |
| Aplicação automática de patches de software | Sim |
| Os recursos mais recentes do mecanismo de banco de dados | Sim |
| Número de arquivos de dados (LINHAS) por banco de dados | Vários |
| Número de arquivos de log (LOG) por banco de dados | 1 |
| VNet - Implantação do Azure Resource Manager | Sim |
| VNet - Modelo de implantação clássico | Não |
| Suporte do Portal | Sim|
| Serviço de Integração Integrado (SSIS) | Não - o SSIS faz parte da [PaaS do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Serviço de análise interna (SSAS) | Não - SSAS é separado [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Serviço interno de relatório (SSRS) | Não - usar o Power BI ou no IaaS do SSRS |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) para instâncias gerenciadas oferece flexibilidade, controle, transparência e uma maneira direta de traduzir os requisitos de carga de trabalho local para a nuvem. Este modelo permite que você altere computação, memória e armazenamento com base em suas necessidades de carga de trabalho. O modelo vCore também é elegível para economias de até 55% com o [Azure Hybrid Benefit para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

No modelo vCore, você pode escolher entre gerações de hardware.

- **Gen4** As CPUs lógicas são baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, SSD conectado, núcleos físicos, MEMÓRIA RAM de 7 GB por núcleo e tamanhos de cálculo entre 8 e 24 vCores.
- **Gen5** As CPUs lógicas são baseadas nos processadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz e Intel SP-8160 (Skylake), SSD NVMe rápido, núcleo lógico hiper-threaded e tamanhos de computação entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre as gerações de hardware nos [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Camadas de serviço de instância gerenciada

A instância gerenciada está disponível em duas camadas de serviço:

- **Propósito geral**: Projetado para aplicações com requisitos típicos de desempenho e latência de IO.
- **Crítica de negócios**: Projetado para aplicações com baixos requisitos de latência de IO e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambas as camadas de serviço garantem 99,99% de disponibilidade e permitem que você selecione de maneira independente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade do Banco de Dados SQL do Azure, veja [Alta disponibilidade e Banco de Dados SQL do Azure](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Camada de serviço de uso geral

A lista a seguir apresenta a principal característica da camada de serviço de Uso Geral:

- Design para a maioria dos aplicativos de negócios com requisitos de desempenho típicos
- Armazenamento de Blobs do Azure de alto desempenho (8 TB)
- [Alta disponibilidade](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) integrada, baseada no confiável Armazenamento de Blobs do Azure e no [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Para obter mais informações, veja [Camada de armazenamento em Camada de usos gerais](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e [práticas recomendadas de desempenho de armazenamento e considerações para instâncias gerenciadas (uso geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recurso de instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Camada de serviço comercialmente crítica

Camada de serviço Comercialmente Crítico desenvolvida para aplicativos com altos requisitos de E/S. Ele oferece maior resiliência a falhas usando várias réplicas isoladas.

A lista a seguir apresenta as características principais da camada de serviço Comercialmente Crítico:

- Projetada para aplicativos de negócios com requisitos de alta disponibilidade e desempenho mais alto
- Vem com armazenamento SSD local super rápido (até 1 TB no Gen 4 e até 4 TB no Gen 5)
- A [alta disponibilidade](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) incorporada com base em grupos de disponibilidade [always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e malha de serviço [do Azure](../service-fabric/service-fabric-overview.md).
- Réplica de [banco de dados](sql-database-read-scale-out.md) adicional somente de leitura incorporada que pode ser usada para emissão de relatórios e outras cargas de trabalho somente leitura
- [OLTP In-Memory](sql-database-in-memory.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recurso de instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operações gerenciadas de gerenciamento de instâncias

O banco de dados SQL do Azure fornece operações de gerenciamento que você pode usar para implantar automaticamente novas instâncias gerenciadas, atualizar propriedades de instância e excluir instâncias quando não forem mais necessárias. Esta seção fornece informações sobre operações de gerenciamento e suas durações típicas.

Para oferecer suporte [às implantações dentro das Redes Virtuais (VNets) do Azure](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) e fornecer isolamento e segurança para os clientes, a instância gerenciada conta com [clusters virtuais,](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)que representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede virtual do cliente. Essencialmente, cada implantação de instância gerenciada em uma sub-rede vazia resulta em uma nova compilação de cluster virtual.

Operações subseqüentes em instâncias gerenciadas implantadas também podem ter efeitos em seu cluster virtual subjacente. Isso afeta a duração das operações de gerenciamento, uma vez que a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa ser considerada quando você planeja novas implantações ou atualizações para instâncias gerenciadas existentes.

Todas as operações de gerenciamento podem ser categorizadas da seguinte maneira:

- Implantação de instância (criação de novas instâncias). 
- Atualização de instância (alterar propriedades de instância, como vCores ou armazenamento reservado.
- Exclusão de instâncias.

Normalmente, as operações em clusters virtuais demoram mais tempo. A duração das operações em clusters virtuais varia – abaixo estão os valores que você normalmente pode esperar, com base nos dados de telemetria de serviço existentes:

- Criação de clustervirtual. Este é um passo síncrono nas operações de gerenciamento de instâncias. **90% das operações terminam em 4 horas.**
- Redimensionamento de clustervirtual (expansão ou encolhimento). A expansão é um passo síncrono, enquanto o encolhimento é realizado de forma assíncrona (sem impacto na duração das operações de gerenciamento de instâncias). **90% das expansões de cluster terminam em menos de 2,5 horas**.
- Exclusão de cluster virtual. A exclusão é um passo assíncrono, mas também pode ser [iniciada manualmente](sql-database-managed-instance-delete-virtual-cluster.md) em um cluster virtual vazio, nesse caso ele é executado de forma sincronizada. **90% das exclusões de clusters virtuais terminam em 1,5 horas**.

Além disso, o gerenciamento de instâncias também pode incluir uma das operações em bancos de dados hospedados, o que resulta em durações mais longas:

- Anexando arquivos de banco de dados do Azure Storage. Este é um passo síncrono, como computação (vCore) ou dimensionamento de armazenamento para cima ou para baixo no nível de serviço do Propósito Geral. **90% dessas operações terminam em 5 minutos.**
- Sempre em semeação de grupo de disponibilidade. Este é um passo síncrono, como computação (vCore) ou dimensionamento de armazenamento no nível de serviço Business Critical, bem como na alteração do nível de serviço de Uso Geral para Business Critical (ou vice-versa). A duração desta operação é proporcional ao tamanho total do banco de dados, bem como à atividade atual do banco de dados (número de transações ativas). A atividade do banco de dados ao atualizar uma instância pode introduzir uma variância significativa na duração total. **90% dessas operações são executadas a 220 GB/hora ou mais.**

A tabela a seguir resume as operações e as durações gerais típicas:

|Categoria  |Operação  |Segmento de longo prazo  |Duração estimada  |
|---------|---------|---------|---------|
|**Implantação** |Primeira instância em uma sub-rede vazia|Criação de clustervirtual|90% das operações terminam em 4 horas|
|Implantação |Primeira instância de outra geração de hardware em uma sub-rede não vazia (por exemplo, primeira instância gen 5 em uma sub-rede com instâncias Gen 4)|Criação de cluster virtual*|90% das operações terminam em 4 horas|
|Implantação |Criação em primeira instância de 4 vCores, em uma sub-rede vazia ou não|Criação de cluster virtual**|90% das operações terminam em 4 horas|
|Implantação |Criação de instâncias subseqüentes dentro da sub-rede não vazia (2ª, 3ª, etc. instância)|Redimensionamento de clustervirtual|90% das operações terminam em 2,5 horas|
|**Atualização** |Alteração de propriedade de instância (senha de administrador, login AAD, bandeira azure Hybrid Benefit)|N/D|Até 1 minuto|
|Atualizar |Dimensionamento de armazenamento de instâncias para cima/para baixo (nível de serviço de propósito geral)|Anexando arquivos de banco de dados|90% das operações terminam em 5 minutos|
|Atualizar |Dimensionamento de armazenamento por instância supércal (nível de serviço crítico do business)|- Redimensionamento de clustervirtual<br>- Sempre em semeação de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todos os bancos de dados (220 GB/hora)|
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Propósito Geral)|- Redimensionamento de clustervirtual<br>- Anexando arquivos de banco de dados|90% das operações terminam em 2,5 horas|
|Atualizar |Cálculo de instância (vCores) de dimensionamento para cima e para baixo (Business Critical)|- Redimensionamento de clustervirtual<br>- Sempre em semeação de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todos os bancos de dados (220 GB/hora)|
|Atualizar |A escala de instância sumiu para 4 vCores (Propósito Geral)|- Redimensionamento de cluster virtual (se feito pela primeira vez, pode exigir criação de cluster virtual**)<br>- Anexando arquivos de banco de dados|90% das operações terminam em 4 h 5 min**|
|Atualizar |A escala de instância sumiu para 4 vCores (Business Critical)|- Redimensionamento de cluster virtual (se feito pela primeira vez, pode exigir criação de cluster virtual**)<br>- Sempre em semeação de grupo de disponibilidade|90% das operações terminam em 4 horas + tempo para semear todos os bancos de dados (220 GB/ hora)|
|Atualizar |Alteração do nível de serviço de instância (Propósito Geral para O Business Critical e vice-versa)|- Redimensionamento de clustervirtual<br>- Sempre em semeação de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todos os bancos de dados (220 GB/hora)|
|**Exclusão**|Exclusão da instância|Log tail backup para todos os bancos de dados|90% das operações terminam em até 1 minuto.<br>Nota: se a última instância da sub-rede for excluída, essa operação agendará a exclusão do cluster virtual após 12 horas***|
|Exclusão|Exclusão de cluster virtual (como operação iniciada pelo usuário)|Exclusão de cluster virtual|90% das operações terminam em até 1,5 horas|

\*O cluster virtual é construído por geração de hardware.

\*\*A opção de implantação de 4 vCores foi lançada em junho de 2019 e requer uma nova versão de cluster virtual. Se você tiver instâncias na sub-rede de destino que foram todas criadas antes de 12 de junho, um novo cluster virtual será implantado automaticamente para hospedar 4 instâncias vCore.

\*\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, então não se dependência disso. Se você precisar excluir um cluster virtual mais cedo (para liberar a sub-rede, por exemplo), consulte [Excluir uma sub-rede depois de excluir uma instância gerenciada do Azure SQL Database](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Disponibilidade de instâncias durante o gerenciamento

As instâncias gerenciadas não estão disponíveis para os aplicativos clientes durante as operações de implantação e exclusão.

As instâncias gerenciadas estão disponíveis durante as operações de atualização, mas há um curto tempo de inatividade causado pelo failover que acontece no final das atualizações que normalmente duram até 10 segundos. A exceção a isso é a atualização do espaço de armazenamento reservado no nível de serviço General Purpose, que não incorre em failover nem afeta a disponibilidade de instâncias.

> [!IMPORTANT]
> A duração de um failover pode variar significativamente no caso de transações de longa duração que ocorrem nos bancos de dados devido ao [tempo de recuperação prolongado](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Portanto, não é recomendável dimensionar o cálculo ou o armazenamento da instância gerenciada do Azure SQL Database ou alterar o nível de serviço ao mesmo tempo com as transações de longo prazo (importação de dados, trabalhos de processamento de dados, reconstrução de índices, etc.). O failover do banco de dados que será realizado no final da operação cancelará transações em andamento e resultará em tempo de recuperação prolongado.

> [!TIP]
> A atualização do espaço de armazenamento reservado no nível de serviço General Purpose não incorre em failover nem afeta a disponibilidade da ocorrência.

[A recuperação acelerada do banco de dados](sql-database-accelerated-database-recovery.md) não está disponível no momento para instâncias gerenciadas pelo Azure SQL Database. Uma vez ativado, esse recurso reduzirá significativamente a variabilidade do tempo de failover, mesmo em caso de transações de longo prazo.

### <a name="canceling-management-operations"></a>Cancelando operações de gerenciamento

A tabela a seguir resume a capacidade de cancelar operações de gerenciamento específicas e durações gerais típicas:

Categoria  |Operação  |Cancelável  |Duração estimada de cancelamento  |
|---------|---------|---------|---------|
|Implantação |Criação de instâncias |Não |  |
|Atualizar |Dimensionamento de armazenamento de instâncias para cima/para baixo (Propósito Geral) |Não |  |
|Atualizar |Dimensionamento de armazenamento de instâncias para cima/para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos |
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Propósito Geral) |Sim |90% das operações terminam em 5 minutos |
|Atualizar |Cálculo de instância (vCores) de dimensionamento para cima e para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos |
|Atualizar |Alteração do nível de serviço de instância (Propósito Geral para O Business Critical e vice-versa) |Sim |90% das operações terminam em 5 minutos |
|Excluir |Exclusão da instância |Não |  |
|Excluir |Exclusão de cluster virtual (como operação iniciada pelo usuário) |Não |  |

Para cancelar a operação de gerenciamento, vá até a lâmina de visão geral e clique na caixa de notificação da operação em andamento. Do lado direito, uma tela com operação contínua aparecerá e haverá botão para cancelar a operação. Após o primeiro clique, você será solicitado a clicar novamente e confirmar que deseja cancelar a operação.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Depois que a solicitação de cancelamento for submetida e processada, você receberá uma notificação se o envio de cancelamento tiver sido bem sucedido ou não. 

Em caso de sucesso de cancelamento, a operação de gerenciamento será cancelada em alguns minutos, resultando em uma falha.

![cancelamento resultado da operação](./media/sql-database-managed-instance/canceling-operation-result.png)

Se cancelar solicitação falhar ou cancelar botão não estiver ativo, isso significa que a operação de gerenciamento entrou em estado não cancelável e que terminará em alguns minutos. A operação de gestão continuará sua execução até que seja concluída.

> [!IMPORTANT]
> Aoperação de cancelamento é atualmente suportada apenas no Portal.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A opção de implantação da instância gerenciada combina os recursos de segurança avançados fornecidos pela nuvem do Azure e pelo Mecanismo de Banco de Dados do SQL Server.

### <a name="managed-instance-security-isolation"></a>Isolamento de segurança da instância gerenciada

A instância gerenciada fornece isolamento de segurança adicional de outros locatários na nuvem do Azure. O isolamento de segurança inclui:

- [Implementação de rede virtual nativa](sql-database-managed-instance-connectivity-architecture.md) e conectividade ao seu ambiente local usando o Azure Express Route ou o VPN Gateway.
- Em uma implantação padrão, o ponto final SQL é exposto apenas através de um endereço IP privado, permitindo conectividade segura de redes privadas do Azure ou híbridas.
- Locatário único com infraestrutura subjacente dedicada (computação, armazenamento).

O diagrama a seguir descreve várias opções de conectividade para seus aplicativos:

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Para obter mais detalhes sobre a integração VNet e a imposição de políticas de rede no nível da sub-rede, confira [Arquitetura de VNet para instâncias gerenciadas](sql-database-managed-instance-connectivity-architecture.md) e [Conectar seu aplicativo à instância gerenciada](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Coloque várias instâncias gerenciadas na mesma sub-rede, onde quer que o permitido pela seus requisitos de segurança, como o que trará benefícios adicionais. Disposição das instâncias na mesma sub-rede será significativamente simplificar a manutenção de infraestrutura de rede e reduzir o tempo, o provisionamento, pois o provisionamento de longa duração está associado ao custo de implantação de primeira instância gerenciada em uma sub-rede de instância.

### <a name="azure-sql-database-security-features"></a>Recursos de segurança do Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure fornece um conjunto de recursos de segurança avançados que podem ser usados para proteger os dados.

- [A auditoria de instância gerenciada](sql-database-managed-instance-auditing.md) rastreia eventos do banco de dados e os grava em um arquivo de registro de auditoria colocado em sua conta de armazenamento Do Zure. A auditoria pode ajudar a manter conformidade com as normas, entender a atividade do banco de dados e ter ideia das discrepâncias e anomalias que podem gerar preocupações comerciais ou violações suspeitas de seguranças.
- Criptografia de dados em movimento – a instância gerenciada protege os dados, fornecendo criptografia para dados em movimento usando a segurança de camada de transporte. Além do protocolo TLS, a opção de implantação da instância gerenciada oferece proteção de dados confidenciais em trânsito, em repouso e durante processamento de consulta com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é pioneiro na indústria, oferecendo segurança de dados incomparável contra violações envolvendo o roubo de dados críticos. Por exemplo, com o Always Encrypted, números de cartão de crédito são armazenados sempre criptografados no banco de dados, mesmo durante a consulta de processamento, permitindo a descriptografia no ponto de uso por pessoal autorizado ou aplicativos que precisam processar os dados.
- [O Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) complementa [a auditoria fornecendo](sql-database-managed-instance-auditing.md) uma camada adicional de inteligência de segurança incorporada ao serviço que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL, bem como padrões de acesso do banco de dados anormais. Os alertas avançados de proteção contra ameaças podem ser vistos no [Azure Security Center](https://azure.microsoft.com/services/security-center/) e fornecer detalhes de atividades suspeitas e recomendar ações sobre como investigar e mitigar a ameaça.  
- [Dynamic data masking](/sql/relational-databases/security/dynamic-data-masking) limits sensitive data exposure by masking it to non-privileged users. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designados, sendo que os dados no banco de dados não são alterados.
- A [segurança em nível de linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução). A RLS (Segurança em Nível de Linha) simplifica o design e codificação de segurança em seu aplicativo. Ela permite implementar restrições de acesso à linha de dados. Por exemplo, garantindo que os funcionários possam acessar apenas as linhas de dados pertinentes ao seu departamento ou restringindo o acesso a dados apenas para dados relevantes.
- A [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) criptografa arquivos de dados de instância gerenciada, conhecido como dados de criptografia em repouso. A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos arquivos de log e de dados. A criptografia usa uma chave de criptografia de banco de dados (DEK), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação. Você pode proteger todos os seus bancos de dados na instância gerenciada com a transparent data encryption. A TDE é a tecnologia de criptografia em repouso comprovada do SQL Server exigida por vários padrões de conformidade para proteger contra roubo de mídia de armazenamento.

Há suporte para a migração de um banco de dados criptografado para a instância gerenciada por meio do DMS (Serviço de Migração de Banco de Dados) do Azure ou restauração nativa. Se você planeja migrar um banco de dados criptografado usando restauração nativa, a migração do certificado TDE existente do SQL Server no local ou do SQL Server em uma máquina virtual para uma instância gerenciada é uma etapa necessária. Para obter mais informações sobre as opções de migração consulte [migração da instância do SQL Server para a instância gerenciada](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integração do Active Directory do Azure

A opção de implantação de instância gerenciada dá suporte a logons e logons do mecanismo de Banco de Dados do SQL Server e logons integrados ao AAD (Azure Active Directory). As entidades de servidor do Azure AD (logons, **versão prévia pública**) são uma versão de nuvem do Azure de logons de banco de dados locais do Windows que você está usando no ambiente local. Os recursos do servidor Azure AD (logins) permitem que você especifique usuários e grupos do seu inquilino do Azure Active Directory como princípios com escopo de instância real, capazes de executar qualquer operação em nível de instância, incluindo consultas de banco de dados cruzados dentro do mesmo gerenciado Instância.

Uma nova sintaxe é introduzida para criar os principais (logins) do servidor Azure AD, **do PROVEDOR EXTERNO**. Para obter mais informações sobre a sintaxe, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CRIAR LOGIN</a>e revise a [Provisão de um administrador do Diretório Ativo do Azure para o artigo da instância gerenciada.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

A opção de implantação de instância gerenciada permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory é compatível com [MFA](sql-database-ssms-mfa-authentication-configure.md) (autenticação multifator) para aumentar a segurança de aplicativos e dados e dá suporte a um processo de logon único.

### <a name="authentication"></a>Autenticação

A autenticação de instância gerenciada refere-se a como os usuários comprovam a identidade ao conectarem-se ao banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:  

- **Autenticação SQL:**

  Este método de autenticação usa um nome de usuário e senha.
- **Autenticação do diretório ativo do Azure:**

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Use autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

Autorização refere-se ao que um usuário pode fazer em um Banco de Dados SQL do Azure e é controlado pelas associações a uma função de banco de dados e pelas permissões no nível de objeto da conta de usuário. Uma instância gerenciada tem os mesmos recursos de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de banco de dados

A opção de implantação de instância gerenciada destina-se a cenários de usuários com migração de banco de dados em massa de implementações de bancos de dados locais ou IaaS. A instância gerenciada oferece suporte a várias opções de migração de banco de dados:

### <a name="back-up-and-restore"></a>Fazer backup e restaurar  

A abordagem de migração aproveita backups do SQL para o Armazenamento de Blobs do Azure. Os backups armazenados no blob de armazenamento do Azure podem ser restaurados diretamente na instância gerenciada usando o [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para um início rápido mostrando como restaurar o arquivo de backup do banco de dados Wide World Importers - Standard, consulte [Restaurar um arquivo de backup em uma instância gerenciada](sql-database-managed-instance-get-started-restore.md). Este quickstart mostra que você tem que carregar um arquivo de backup para o armazenamento blob do Azure e protegê-lo usando uma chave SAS (Assinatura de acesso compartilhado).
- Para obter informações sobre restauração de URL, consulte [Restauração nativa de URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Os backups de uma instância gerenciada só podem ser restaurados para outra instância gerenciada. Eles não podem ser restaurados para um SQL Server local ou para um pool elástico/banco de dados individual.

### <a name="data-migration-service"></a>Serviço de Migração de Dados

O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados do SQL Server e de terceiros existentes para o Banco de Dados SQL do Azure (bancos de dados individuais, bancos de dados agrupados em pools elásticos e bancos de dados de instância em uma instância gerenciada) e o SQL Server na VM do Azure. Veja [Como migrar o banco de dados local para a instância gerenciada usando DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

A opção de implantação de instância gerenciada tem como objetivo entregar aproximadamente 100% de compatibilidade de área de superfície com o SQL Server local em etapas, até a disponibilidade geral do serviço. Para uma lista de recursos e comparação, consulte [Comparação de recursos do Banco de Dados SQL](sql-database-features.md) e para uma lista de diferenças de T-SQL em instâncias gerenciadas em comparação com o SQL Server, consulte [Diferenças do T-SQL da instância gerenciada do SQL Server](sql-database-managed-instance-transact-sql-information.md).

A opção de implantação de instância gerenciada dá suporte à compatibilidade com versões anteriores para bancos de dados SQL 2008. A migração direta dos servidores do Banco de Dados do SQL 2005 tem suporte, o nível de compatibilidade para Bancos de Dados do SQL 2005 migrados é atualizado para o SQL 2008.
  
O diagrama a seguir apresenta a compatibilidade da área de superfície na instância gerenciada:  

![migração](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Principais diferenças entre SQL Server local e instância gerenciada

A opção de implantação de instância gerenciada se beneficia de estar sempre atualizada na nuvem, o que significa que alguns recursos no SQL Server local podem estar obsoletos, desativados ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que um determinado recurso funciona de uma maneira ligeiramente diferente ou que o serviço está sendo executado em um ambiente que você não controla totalmente:

- Alta disponibilidade baseia-se e pré-configurado usando a tecnologia semelhante [grupos de disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Backups automatizados e restauração pontual. O cliente pode iniciar backups `copy-only` que não interferem na cadeia de backup automático.
- A instância gerenciada não permite especificar caminhos físicos completos, de modo que todos os cenários correspondentes devem ser suportados de forma diferente: RESTORE DB não suporta COM MOVE, CREATE DB não permite caminhos físicos, BULK INSERT funciona apenas com Azure Blobs, etc.
- A instância gerenciada suporta [a autenticação do Azure AD](sql-database-aad-authentication.md) como alternativa na nuvem à autenticação do Windows.
- A instância gerenciada gerencia automaticamente o grupo de arquivos XTP e os arquivos para bancos de dados que contêm objetos OLTP in-memory
- A instância gerenciada suporta o SSIS Server Integration Services (SSIS) e pode hospedar o catálogo SSIS (SSISDB) que armazena pacotes SSIS, mas eles são executados em um Tempo de Execução de Integração (IR) gerenciado do Azure-SSIS na Azure Data Factory (ADF), consulte [Criar O IR do Azure-SSIS no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Para comparar os recursos do SSIS no Banco de Dados SQL, consulte [Compare um banco de dados Único do Azure SQL, pool elástico e instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Recursos de administração de instância gerenciada

A opção de implantação de instância gerenciada permite que o administrador do sistema gaste menos tempo em tarefas administrativas porque o serviço de Banco de Dados SQL as executa para você ou simplifica bastante essas tarefas. Por exemplo, [instalação e patches do OS/RDBMS,](sql-database-high-availability.md) [redimensionamento e configuração de instância dinâmica,](sql-database-single-database-scale.md) [backups,](sql-database-automated-backups.md) [replicação de banco](replication-with-sql-database-managed-instance.md) de dados (incluindo bancos de dados do sistema), [configuração](sql-database-high-availability.md)de alta disponibilidade e configuração de fluxos de dados de monitoramento de saúde e [desempenho.](../azure-monitor/insights/azure-sql.md)

> [!IMPORTANT]
> Para obter uma lista de recursos com suporte, suporte parcial e sem suporte, consulte [Recursos do Banco de Dados SQL](sql-database-features.md). Para obter uma lista de diferenças T-SQL em instâncias gerenciadas versus SQL Server, consulte [as diferenças T-SQL de instância gerenciada do SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Como identificar programaticamente uma instância gerenciada

A tabela a seguir mostra várias propriedades, acessíveis por meio do Transact-SQL que podem ser usadas para detectar se o aplicativo está trabalhando com a instância gerenciada e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 07/03/2018 Copyright (C) 2018 Microsoft Corporation.|Esse valor é o mesmo que no Banco de Dados SQL. Isso **não** indica a versão 12 do motor SQL (SQL Server 2014). A instância gerenciada sempre executa a versão mais recente do motor SQL estável, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Esse valor identifica exclusivamente a instância gerenciada.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS da instância completo no seguinte formato:`<instanceName>`.`<dnsPrefix>`.database.windows.net, em que `<instanceName>` é o nome fornecido pelo cliente, enquanto `<dnsPrefix>` é a parte gerada automaticamente do nome, garantindo exclusividade de nome DNS global ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira instância gerenciada, consulte [o guia Quickstart](sql-database-managed-instance-get-started.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração do VNet, consulte [a configuração vnet de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Para uma partida rápida que cria uma instância gerenciada e restaura um banco de dados a partir de um arquivo de backup, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o DMS (Azure Database Migration Service, serviço de migração de banco de dados do Azure) para migração, consulte [a migração de instâncias gerenciadas usando o DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para obter um monitoramento avançado do desempenho do banco de dados de instância sacada gerenciada com inteligência incorporada para solução de [problemas, consulte Monitor Azure SQL Database usando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte o preço de [instância gerenciada do SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
