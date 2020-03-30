---
title: Alta disponibilidade
description: Saiba mais sobre os recursos e capacidades de alta disponibilidade do serviço de Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269647"
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade

O objetivo da arquitetura de alta disponibilidade no Banco de Dados SQL do Azure é garantir que seu banco de dados esteja funcionando no mínimo 99,99% do tempo (Para obter mais informações sobre SLA específico para diferentes níveis, consulte o SLA para o Banco de [Dados SQL do Azure),](https://azure.microsoft.com/support/legal/sla/sql-database/)sem se preocupar com o impacto das operações de manutenção e paralisações. O Azure lida automaticamente com tarefas críticas de manutenção, como patches, backups, atualizações do Windows e SQL, bem como eventos não planejados, como falhas subjacentes de hardware, software ou rede.  Quando a instância SQL subjacente é corrigida ou falha, o tempo de inatividade não é perceptível se você [empregar uma lógica de repetição](sql-database-develop-overview.md#resiliency) em seu aplicativo. O Banco de Dados SQL do Azure pode se recuperar rapidamente, até nas circunstâncias mais críticas, garantindo que seus dados estejam sempre disponíveis.

A solução de alta disponibilidade foi projetada para garantir que os dados comprometidos nunca sejam perdidos devido a falhas, que as operações de manutenção não afetem sua carga de trabalho e que o banco de dados não será um único ponto de falha em sua arquitetura de software. Não há nenhuma janela de manutenção ou tempo de inatividade que deva exigir que você pare a carga de trabalho enquanto o banco de dados é atualizado ou está em manutenção. 

Existem dois modelos arquitetônicos de alta disponibilidade que são usados no Banco de Dados SQL do Azure:

- Modelo de disponibilidade padrão que é baseado em uma separação de computação e armazenamento.  Ele conta com alta disponibilidade e confiabilidade do nível de armazenamento remoto. Essa arquitetura tem como alvo aplicativos de negócios orientados ao orçamento que podem tolerar alguma degradação de desempenho durante as atividades de manutenção.
- Modelo de disponibilidade premium que é baseado em um cluster de processos de mecanismo de banco de dados. Ele se baseia no fato de que há sempre um quórum de nós de mecanismo de banco de dados disponíveis. Essa arquitetura tem como alvo aplicativos essenciais com alto desempenho de IO, alta taxa de transação e garante um impacto mínimo de desempenho na sua carga de trabalho durante as atividades de manutenção.

O Azure SQL Database é executado na versão estável mais recente do SQL Server Database Engine e do Windows OS, e a maioria dos usuários não notaria que as atualizações são realizadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade de camada de serviço básico, padrão e de uso geral

Esses níveis de serviço aproveitam a arquitetura de disponibilidade padrão. A figura a seguir mostra quatro nós diferentes com as camadas de computação e armazenamento separadas.

![Separação de computação e armazenamento](media/sql-database-high-availability/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação `sqlservr.exe` apátrida que executa o processo e contém apenas dados transitórios e armazenados em cache, como tempDB, bancos de dados de modelo no SSD anexado e cache de plano, pool de buffer e pool de columnstore na memória. Este nó apátrida é operado pela Azure `sqlservr.exe`Service Fabric que inicializa, controla a saúde do nó e realiza failover para outro nó, se necessário.
- Uma camada de dados imponente com os arquivos de banco de dados (.mdf/.ldf) armazenados no armazenamento Azure Blob. O armazenamento de blob do Azure tem disponibilidade de dados incorporada e recurso de redundância. Ele garante que todos os registros no arquivo de log ou página no arquivo de dados serão preservados mesmo se o processo do SQL Server falhar.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado ou uma falha for detectada, o Azure Service Fabric mudará o processo sem estado do SQL Server para outro nó de computação apátrida com capacidade livre suficiente. Os dados no armazenamento Azure Blob não são afetados pela mudança, e os arquivos de dados/log são anexados ao processo sql server recém-inicializado. Esse processo garante 99,99% de disponibilidade, mas uma carga de trabalho pesada pode sofrer alguma degradação de desempenho durante a transição, já que a nova instância do SQL Server começa com cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade de camada de serviço Premium e Business Critical

Os níveis de serviço Premium e Business Critical aproveitam o modelo de disponibilidade Premium, que integra recursos de computação (processo sql server database engine engine) e armazenamento (SSD anexado localmente) em um único nó. A alta disponibilidade é alcançada replicando a computação e o armazenamento em nós adicionais criando um cluster de três a quatro nós. 

![Cluster de nós de mecanismo de banco de dados](media/sql-database-high-availability/business-critical-service-tier.png)

Os arquivos de banco de dados subjacentes (.mdf/.ldf) são colocados no armazenamento SSD anexado para fornecer IO de latência muito baixa à sua carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante ao SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária (processo SQL Server) acessível para cargas de trabalho de clientes de gravação de leitura e até três réplicas secundárias (computação e armazenamento) contendo cópias de dados. O nó principal constantemente empurra alterações para os nós secundários em ordem e garante que os dados sejam sincronizados com pelo menos uma réplica secundária antes de cometer cada transação. Este processo garante que, se o nó primário falhar por qualquer motivo, há sempre um nó totalmente sincronizado para falhar. O failover é iniciado pelo Azure Service Fabric. Uma vez que a réplica secundária se torne o novo nó primário, outra réplica secundária é criada para garantir que o cluster tenha nódulos suficientes (conjunto de quórum). Uma vez que o failover esteja concluído, as conexões SQL são automaticamente redirecionadas para o novo nó principal.

Como um benefício extra, o modelo de disponibilidade premium inclui a capacidade de redirecionar conexões SQL somente de leitura para uma das réplicas secundárias. Este recurso é chamado [Read Scale-Out](sql-database-read-scale-out.md). Ele fornece capacidade computacional adicional de 100% sem custo adicional para descarregar operações somente leitura, como cargas de trabalho analíticas, a partir da réplica primária.

## <a name="hyperscale-service-tier-availability"></a>Disponibilidade de nível de serviço de hiperescala

A arquitetura de nível de serviço Hyperscale é descrita na [arquitetura de funções distribuídas](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Arquitetura funcional de hiperescala](./media/sql-database-hyperscale/hyperscale-architecture.png)

O modelo de disponibilidade em Hyperscale inclui quatro camadas:

- Uma camada de computação `sqlservr.exe` apátrida que executa os processos e contém apenas dados transitórios e armazenados em cache, como cache RBPEX não coberto, TempDB, banco de dados de modelo, etc. no SSD anexado e cache de plano, pool de buffer e pool de columnstore na memória. Esta camada apátrida inclui a réplica de computação primária e, opcionalmente, uma série de réplicas de computação secundárias que podem servir como alvos de failover.
- Uma camada de armazenamento apátrida formada por servidores de página. Esta camada é o mecanismo `sqlservr.exe` de armazenamento distribuído para os processos executados nas réplicas de computação. Cada servidor de página contém apenas dados transitórios e armazenados em cache, como cobrir o cache RBPEX no SSD anexado e páginas de dados armazenadas em cache na memória. Cada servidor de página tem um servidor de página emparelhado em uma configuração ativa para fornecer balanceamento de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de log de transação de transação imponente formada pelo nó de computação executando o processo de serviço Log, a zona de aterrissagem do registro de transações e o armazenamento de registro de transações a longo prazo. A zona de pouso e o armazenamento a longo prazo usam o Azure Storage, que fornece disponibilidade e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para log de transações, garantindo a durabilidade dos dados para transações comprometidas.
- Uma camada de armazenamento de dados imponente com os arquivos de banco de dados (.mdf/.ndf) que são armazenados no Azure Storage e são atualizados por servidores de página. Essa camada usa recursos de disponibilidade e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) de dados do Azure Storage. Ele garante que cada página em um arquivo de dados será preservada mesmo se os processos em outras camadas de falha de arquitetura Hyperscale ou se os nós de computação falharem.

Nós computados em todas as camadas hyperscale executados no Azure Service Fabric, que controla a saúde de cada nó e executa failovers aos nós saudáveis disponíveis, conforme necessário.

Para obter mais informações sobre alta disponibilidade em Hyperscale, consulte [Database High Availability in Hyperscale](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por padrão, o cluster de nós para o modelo de disponibilidade premium é criado no mesmo datacenter. Com a introdução do [Azure Availability Zones,](../availability-zones/az-overview.md)o SQL Database pode colocar diferentes réplicas do banco de dados Business Critical para diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../traffic-manager/traffic-manager-overview.md). Como a configuração redundante de região nos níveis de serviço Premium ou Business Critical não cria redundância adicional de banco de dados, você pode habilitá-la sem custo adicional. Ao selecionar uma configuração redundante de zona, você pode tornar seus bancos de dados Premium ou Business Critical resilientes a um conjunto muito maior de falhas, incluindo paralisações catastróficas de data centers, sem alterações na lógica do aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico existentes para a configuração com redundância de zona.

Como os bancos de dados redundantes da região possuem réplicas em diferentes data centers com alguma distância entre eles, o aumento da latência da rede pode aumentar o tempo de confirmação e, assim, impactar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Este processo é uma operação on-line semelhante à atualização regular do nível de serviço. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Bancos de dados redundantes de zona e pools elásticos são atualmente suportados apenas nos níveis de serviço Premium e Business Critical em regiões selecionadas. Ao usar o nível Business Critical, a configuração redundante da zona só está disponível quando o hardware de computação Gen5 é selecionado. Para obter informações atualizadas sobre as regiões que suportam bancos de dados redundantes da zona, consulte [Serviços de suporte por região](../availability-zones/az-overview.md#services-support-by-region).  
> Esse recurso não está disponível na instância Gerenciada.

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:

![Arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação Acelerada de Banco de Dados (ADR)

[O Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) é um novo recurso do mecanismo de banco de dados SQL que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longo prazo. ADR está atualmente disponível para o Azure SQL Data Warehouse, pools Elásticos e bancos de dados individuais.

## <a name="testing-application-fault-resiliency"></a>Testando a resiliência da falha do aplicativo

A alta disponibilidade é uma parte fundamental da plataforma Azure SQL Database que funciona de forma transparente para o seu aplicativo de banco de dados. No entanto, reconhecemos que você pode querer testar como as operações automáticas de failover iniciadas durante eventos planejados ou não afetariam o aplicativo antes de implantá-lo na produção. Você pode chamar uma API especial para reiniciar um banco de dados ou um pool elástico, que por sua vez acionará um failover. No caso de um banco de dados redundante de zona ou de um pool elástico, a chamada de API resultaria em redirecionar as conexões do cliente para a nova principal em uma Zona de Disponibilidade diferente da Zona de Disponibilidade do primário antigo. Assim, além de testar como o failover impacta as sessões de banco de dados existentes, você também pode verificar se ele altera o desempenho de ponta a ponta devido a alterações na latência da rede. Como a operação de reinicialização é intrusiva e um grande número deles pode enfatizar a plataforma, apenas uma chamada de failover é permitida a cada 30 minutos para cada banco de dados ou piscina elástica. 

Um failover pode ser iniciado usando a API REST ou powerShell. Para a API REST, consulte [failover de banco de dados](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) e [failover de pool elástico](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). Para O PowerShell, consulte [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) e [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). As chamadas da API REST também podem ser feitas a partir do Azure CLI usando o comando [az rest.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> O comando Failover não está disponível no nível de serviço Hyperscale e em Instância Gerenciada.

## <a name="conclusion"></a>Conclusão

O Banco de Dados Azure SQL possui uma solução integrada de alta disponibilidade, profundamente integrada à plataforma Azure. Depende do Service Fabric para detecção e recuperação de falhas, no armazenamento Azure Blob para proteção de dados e em Zonas de Disponibilidade para maior tolerância a falhas. Além disso, o banco de dados Azure SQL aproveita a tecnologia Always On Availability Group do SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos realizem plenamente os benefícios de um modelo de armazenamento misto e suportem os SLAs mais exigentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre [o Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de negócios](sql-database-business-continuity.md)
