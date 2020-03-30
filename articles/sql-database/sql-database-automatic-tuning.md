---
title: Visão geral do ajuste automático
description: O Banco de Dados SQL do Azure analisa a consulta SQL e automaticamente se adapta à carga de trabalho do usuário.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214111"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático no Banco de Dados SQL do Microsoft Azure

A sintonia automática do Banco de Dados Azure SQL fornece desempenho máximo e cargas de trabalho estáveis por meio de ajuste contínuo de desempenho com base em IA e aprendizado de máquina.

O ajuste automático é um serviço de desempenho inteligente totalmente gerenciado que usa inteligência interna para monitorar continuamente consultas executadas em um banco de dados e aprimora automaticamente o desempenho. Isso é obtido adaptando dinamicamente o banco de dados à mudança das cargas de trabalho e aplicando recomendações de ajuste. O ajuste automático aprende horizontalmente com todos os bancos de dados do Azure por meio de inteligência artificial e aprimora de modo dinâmico suas ações de ajustes. Quanto mais tempo um banco de dados for executado com ajuste automático, melhor será o desempenho.

O ajuste automático do Banco de Dados SQL do Azure pode ser um dos recursos mais importantes que você pode permitir para fornecer cargas de trabalho de banco de dados estáveis e de desempenho máximo.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que a sintonia automática pode fazer por você

- Ajuste de desempenho automatizado de bancos de dados SQL do Azure
- Verificação automatizada de ganhos de desempenho
- Autocorreção e reversão automática
- Histórico de ajuste
- Ajuste dos scripts do T-SQL de ação para implantações manuais
- Monitoramento do desempenho de carga de trabalho proativa
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, confiável e comprovado

As operações de ajuste aplicadas aos bancos de dados no Banco de Dados SQL do Azure são totalmente seguras para o desempenho de suas cargas de trabalho mais intensas. O sistema foi projetado com cuidado para não interferir nas cargas de trabalho do usuário. Recomendações de ajuste automatizadas são aplicadas somente nos horários de pouca utilização. O sistema também pode desabilitar temporariamente as operações de ajuste automático para proteger o desempenho da carga de trabalho. Nesse caso, a mensagem "Desativado pelo sistema" será exibida no portal Azure. Ajuste automático considera cargas de trabalho com a prioridade mais alta de recurso.

Mecanismos de ajuste automático são desenvolvidos e foram aperfeiçoados em milhões de bancos de dados em execução no Azure. As operações de ajuste automatizado aplicadas são verificadas automaticamente para garantir que exista uma melhoria no desempenho da carga de trabalho. Recomendações de desempenho retornadas são detectadas dinamicamente e revertidas no mesmo momento. Por meio do histórico de ajuste registrado, há um rastreamento claro das melhorias de ajuste feitas em cada Banco de Dados SQL do Azure.

![Como funciona o trabalho de ajuste automático](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

O ajuste automático do Banco de Dados SQL do Azure está compartilhando sua lógica central com o mecanismo de ajuste automático do SQL Server. Para obter informações técnicas adicionais sobre o mecanismo interno de inteligência, consulte [Ajuste automático do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

Para uma visão geral de como o ajuste automático funciona em cenários de uso típicos, assista ao vídeo inserido:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Habilitar o ajuste automático

Você pode [habilitar ajuste automático para bancos de dados únicos e agrupados no portal Azure](sql-database-automatic-tuning-enable.md) ou usando a declaração [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL. Você habilita o ajuste automático, por exemplo, de bancos de dados em uma implantação de instância gerenciada usando a declaração [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Opções de ajuste automático

As opções de ajuste automático disponíveis no Banco de Dados SQL do Azure são:

| Opção de ajuste automático | Suporte a banco de dados individual e banco de dados em pool | Suporte de banco de dados de instâncias |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - Identifica índices que podem melhorar o desempenho da sua carga de trabalho, cria índices e verifica automaticamente que o desempenho das consultas melhorou. | Sim | Não |
| **ÍNDICE DE QUEDA** - Identifica índices redundantes e duplicados diariamente, exceto índices únicos, e índices que não foram utilizados por muito tempo (>90 dias). Observe que essa opção não é compatível com aplicativos que usam alternância de partição e dicas de índice. A queda de índices não utilizados não é suportada para os níveis de serviço Premium e Business Critical. | Sim | Não |
| **FORCE LAST GOOD PLAN** (correção automática do plano) - Identifica consultas SQL usando plano de execução que é mais lento do que o plano anterior bom, e consultas usando o último bom plano conhecido em vez do plano regredido. | Sim | Sim |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Ajuste automático para bancos de dados únicos e agrupados

O ajuste automático para bancos de dados únicos e agrupados usa as recomendações de orientador de banco de dados **CREATE INDEX**, **DROP INDEX**e FORCE LAST **GOOD PLAN** para otimizar o desempenho do banco de dados. Para obter mais informações, consulte [as recomendações do consultor de banco de dados no portal Azure,](sql-database-advisor-portal.md)no [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)e na [API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Você pode aplicar manualmente recomendações de ajuste usando o portal Azure ou pode deixar a sintonia automática aplicar de forma autônoma recomendações de ajuste para você. Os benefícios de permitir que o sistema aplique recomendações de ajuste autonomamente para você é que ele valida automaticamente que existe um ganho positivo no desempenho da carga de trabalho e, se não houver nenhuma melhoria de desempenho significativa detectada, ele reverterá automaticamente a recomendação de ajuste. Observe que, no caso de consultas afetadas por recomendações de ajuste que não são executadas com frequência, a fase de validação pode levar até 72 horas por design.

No caso de você aplicar recomendações de ajuste através do T-SQL, a validação automática de desempenho e os mecanismos de reversão não estão disponíveis. As recomendações aplicadas dessa forma permanecerão ativas e apresentadas na lista de recomendações de ajuste para 24-48 horas. antes que o sistema os retire automaticamente. Se você quiser remover uma recomendação mais cedo, você pode descartá-la do portal Azure.

As opções de ajuste automático podem ser habilitadas ou desabilitadas independentemente por banco de dados ou podem ser configuradas em servidores do Banco de Dados SQL e aplicadas em todos os bancos de dados que herdam as configurações do servidor. Os servidores do Banco de Dados SQL podem herdar os padrões do Azure para configurações automáticas de ajuste. Atualmente, os padrões do Azure estão definidos como FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

> [!IMPORTANT]
> A partir de março, as alterações de 2020 para os padrões do Azure para ajuste automático entrarão em vigor da seguinte forma:
>
> - Novos padrões do Azure serão FORCE_LAST_GOOD_PLAN = habilitados, CREATE_INDEX = desativados e DROP_INDEX = desativados.
> - Os servidores existentes sem preferências de ajuste automática configuradas serão configurados automaticamente para HERDar os novos padrões do Azure. Isso se aplica a todos os clientes que atualmente têm configurações de servidor para ajuste automático em um estado indefinido.
> - Novos servidores criados serão configurados automaticamente para HERDar os novos padrões do Azure (ao contrário de antes, quando a configuração de ajuste automático estava em um estado indefinido após a criação do novo servidor).

Configurar opções de ajuste automático em um servidor e herdar configurações para bancos de dados pertencentes ao servidor pai é um método recomendado para configurar ajuste automático, pois simplifica o gerenciamento de opções de ajuste automático para um grande número de bancos de dados.

Para saber mais sobre a criação de notificações de e-mail para recomendações automáticas de ajuste, consulte [notificações de e-mail para ajuste automático](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Ajuste automático para bancos de dados por exemplo

A sintonia automática, por exemplo, de bancos de dados em uma implantação de instância gerenciada só suporta **FORCE LAST GOOD PLAN**. Para obter mais informações sobre a configuração de opções de ajuste automático através do T-SQL, consulte [Ajuste automático introduz correção automática do plano](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [correção automática do plano](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a inteligência incorporada usada na sintonia automática, consulte [os bancos de dados Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)de Inteligência Artificial .
