---
title: Migre seu pool SQL para gen2
description: Instruções para migrar um pool SQL existente para gen2 e o cronograma de migração por região.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4714d5908fffb6f5c1440c3ec512fb8173da4b57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346755"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Atualize seu pool SQL para Gen2

A Microsoft está ajudando a reduzir o custo de entrada de executar um pool SQL.  Níveis de computação mais baixos capazes de lidar com consultas exigentes estão agora disponíveis para pool SQL. Leia o anúncio completo [Suporte de nível de computação inferior para Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). A nova oferta está disponível nas regiões anotadas na tabela abaixo. Para regiões suportadas, os pools SQL Gen1 existentes podem ser atualizados para gen2 através de:

- **O processo de atualização automática:** Os upgrades automáticos não começam assim que o serviço estiver disponível em uma região.  Quando os upgrades automáticos começarem em uma região específica, as atualizações individuais do data warehouse ocorrerão durante o cronograma de manutenção selecionado.
- [**Auto-atualização para Gen2:**](#self-upgrade-to-gen2) Você pode controlar quando atualizar fazendo um auto-upgrade para gen2. Se sua região ainda não estiver suportada, você pode restaurar de um ponto de restauração diretamente para uma instância Gen2 em uma região suportada.

## <a name="automated-schedule-and-region-availability-table"></a>Agendamento automatizado e tabela de disponibilidade na região

A tabela a seguir resume por região quando a camada de computação inferior Gen2 estará disponível e quando as atualizações automáticas serão iniciadas. As datas estão sujeitas à alteração. Consulte a tabela com frequência para saber quando a sua região estará disponível.

\* indica que um agendamento específico para a região está indisponível no momento.

| **Região** | **Gen2 inferior disponível** | **Início das atualizações automáticas** |
|:--- |:--- |:--- |
| Leste do Canadá |1 de junho de 2020 |1 de julho de 2020 |
| Leste da China |\* |\* |
| Norte da China |\* |\* |
| Alemanha Central |\* |\* |
| Alemanha Centro-Oeste |Disponível |1 º de maio de 2020 |
| Oeste da Índia |Disponível |1 º de maio de 2020  |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, estaremos agendando upgrades automatizados para suas instâncias Gen1. Para evitar interrupções inesperadas na disponibilidade do pool SQL, as atualizações automatizadas serão agendadas durante o seu cronograma de manutenção. A capacidade de criar uma nova instância Gen1 será desativada em regiões que passam por upgrade automático para o Gen2. O Gen1 será preterido assim que os upgrades automáticos forem concluídos. Para saber mais sobre agendamentos, consulte [Exibir um agendamento de manutenção](maintenance-scheduling.md#view-a-maintenance-schedule)

O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos seu pool SQL.  Uma vez que seu pool SQL tenha sido reiniciado, ele estará totalmente disponível para uso. No entanto, você pode sofrer uma degradação no desempenho enquanto o processo de atualização continua a atualizar os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Você também pode agilizar o processo de atualização do arquivo de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias usando uma classe maior de SLO e recursos após a reinicialização.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

## <a name="self-upgrade-to-gen2"></a>Autoatualização para Gen2

Você pode optar por fazer uma auto-atualização seguindo essas etapas em um pool SQL Gen1 existente. Se você optar por fazer um auto-upgrade, você deve completá-lo antes que o processo de atualização automática comece na sua região. Isso garante que você evite qualquer risco de que os upgrades automáticos causem um conflito.

Há duas opções ao realizar uma autoatualização.  Você pode atualizar seu pool SQL atual no lugar ou restaurar um pool SQL Gen1 em uma instância Gen2.

- [Upgrade no local](upgrade-to-latest-generation.md) - Esta opção atualizará seu pool De SQL Gen1 existente para gen2. O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos seu pool SQL.  Uma vez que seu pool SQL tenha sido reiniciado, ele estará totalmente disponível para uso. Se você tiver problemas durante a atualização, abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "upgrade gen2" como a possível causa.
- [Upgrade do ponto de restauração](sql-data-warehouse-restore-points.md) - Crie um ponto de restauração definido pelo usuário no pool SQL do Gen1 atual e, em seguida, restaure diretamente em uma instância Gen2. A piscina SQL Gen1 existente permanecerá no local. Uma vez que a restauração tenha sido concluída, seu pool Gen2 SQL estará totalmente disponível para uso.  Depois de executar todos os processos de teste e validação na instância Gen2 restaurada, a instância Gen1 original pode ser excluída.

   - Passo 1: A partir do portal Azure, [crie um ponto de restauração definido pelo usuário](sql-data-warehouse-restore-active-paused-dw.md).
   - Passo 2: Ao restaurar a partir de um ponto de restauração definido pelo usuário, defina o "Nível de desempenho" para o nível de Gen2 preferido.

Talvez você perceba um período de degradação no desempenho enquanto o processo continua atualizando os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Para acelerar o processo de migração de dados em segundo plano, você pode forçar imediatamente a movimentação de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas de columnstore primárias que você estaria consultando em uma classe de recursos e SLO maior.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

Se você encontrar quaisquer problemas com seu pool SQL, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "upgrade Gen2" como a possível causa.

Para saber mais, confira [Atualizar para Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Perguntas frequentes sobre migração

**P: Gen2 custa o mesmo que Gen1?**

- R: Sim.

**P: Como os upgrades afetarão meus scripts de automação?**

- R: Qualquer script de automação que faz referência a um Objetivo de Nível de Serviço deve ser alterado para corresponder ao equivalente Gen2.  Veja os detalhes [aqui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: Quanto tempo uma auto-atualização normalmente leva?**

- A: Você pode atualizar no lugar ou atualizar a partir de um ponto de restauração.  
   - A atualização no local fará com que sua piscina SQL faça uma pausa e retomada momentaneamente.  Um processo de fundo continuará enquanto o pool SQL estiver on-line.  
   - Será mais demorado se você atualizar usando um ponto de restauração, pois a atualização passará pelo processo completo de restauração.

**P: Quanto tempo levará a atualização automática?**

- R: O tempo de inatividade real para a atualização é apenas o tempo necessário para pausar e retomar o serviço, que é entre 5 a 10 minutos. Após esse breve tempo de inatividade, um processo em segundo plano executará uma migração de armazenamento. O tempo de duração do processo de fundo depende do tamanho do seu pool SQL.

**P: Quando será realizada essa atualização automática?**

- A: Durante seu cronograma de manutenção. Aproveitar o agendamento de manutenção escolhido minimizará a interrupção nos seus negócios.

**P: O que devo fazer se meu processo de atualização de fundo parece estar preso?**

 - R: Inicie um reíndice de suas tabelas da Columnstore. Observe que a reindexação da tabela será offline durante essa operação.

**P: E se o Gen2 não tiver o Objetivo de Nível de Serviço que tenho no Gen1?**
- R: Se você estiver executando um DW600 ou DW1200 no Gen1, é aconselhável usar DW500c ou DW1000c, respectivamente, já que o Gen2 fornece mais memória, recursos e desempenho superior ao Gen1.

**P: Posso desativar o geobackup?**
- R: Não. O backup geográfico é um recurso corporativo para preservar a disponibilidade do pool SQL caso uma região fique indisponível. Abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) em caso de outras dúvidas.

**P: Existe uma diferença na sintaxe T-SQL entre Gen1 e Gen2?**

- R: Não há alteração na sintaxe da língua T-SQL de Gen1 para Gen2.

**P: O Gen2 suporta o Windows de manutenção?**

- R: Sim.

**P: Serei capaz de criar uma nova instância gen1 depois que minha região tiver sido atualizada?**

- R: Não. Depois que uma região for atualizada, a criação de novas instâncias de Gen1 será desabilitada.

## <a name="next-steps"></a>Próximas etapas

- [Etapas de atualização](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitoramento da integridade de recursos](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Revise o artigo Antes de começar a migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualizar in-loco e atualizar de um ponto de restauração](upgrade-to-latest-generation.md)
- [Crie um ponto restauração definido pelo usuário](sql-data-warehouse-restore-points.md)
- [Saiba como restaurar para Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Abrir uma solicitação de suporte para SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
