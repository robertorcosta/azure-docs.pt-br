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
ms.date: 03/06/2019
ms.openlocfilehash: 179bb5c9d718a556b829af8f860cb284597835aa
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821885"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático no Banco de Dados SQL do Microsoft Azure

O ajuste automático do Banco de Dados SQL do Azure oferece o desempenho ideal e cargas de trabalho estáveis por meio do ajuste contínuo do desempenho baseado em inteligência artificial e aprendizado de máquina.

O ajuste automático é um serviço de desempenho inteligente totalmente gerenciado que usa inteligência interna para monitorar continuamente consultas executadas em um banco de dados e aprimora automaticamente o desempenho. Isso é obtido adaptando dinamicamente o banco de dados à mudança das cargas de trabalho e aplicando recomendações de ajuste. O ajuste automático aprende horizontalmente com todos os bancos de dados do Azure por meio de inteligência artificial e aprimora de modo dinâmico suas ações de ajustes. Quanto mais longa é a execução do Banco de Dados SQL do Azure com o ajuste automático ligado, melhor se torna seu desempenho.

O ajuste automático de Banco de Dados SQL do Azure pode ser um dos recursos mais importantes que você pode habilitar para fornecer cargas de trabalho de banco de dados com desempenho ideal e estável.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que o Ajuste Automático pode fazer por você?

- Ajuste de desempenho automatizado de bancos de dados SQL do Azure
- Verificação automatizada de ganhos de desempenho
- Autocorreção e reversão automática
- Histórico de ajuste
- Ajuste dos scripts do T-SQL de ação para implantações manuais
- Monitoramento do desempenho de carga de trabalho proativa
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, confiável e comprovado

As operações de ajuste aplicadas aos bancos de dados SQL do Azure são totalmente seguras para o desempenho das cargas de trabalho mais intensas. O sistema foi projetado com cuidado para não interferir nas cargas de trabalho do usuário. Recomendações de ajuste automatizadas são aplicadas somente nos horários de pouca utilização. O sistema também pode desabilitar temporariamente as operações de ajuste automático para proteger o desempenho da carga de trabalho. Nesse caso, a mensagem "Desabilitado pelo sistema" será mostrada no portal do Azure. Ajuste automático considera cargas de trabalho com a prioridade mais alta de recurso.

Mecanismos de ajuste automático são desenvolvidos e foram aperfeiçoados em milhões de bancos de dados em execução no Azure. As operações de ajuste automatizado aplicadas são verificadas automaticamente para garantir que exista uma melhoria no desempenho da carga de trabalho. Recomendações de desempenho retornadas são detectadas dinamicamente e revertidas no mesmo momento. Por meio do histórico de ajuste registrado, há um rastreamento claro das melhorias de ajuste feitas em cada Banco de Dados SQL do Azure. 

![Como funciona o trabalho de ajuste automático](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

O ajuste Automático do Banco de Dados SQL do Azure está compartilhando sua lógica principal com o mecanismo de ajuste automático do SQL Server. Para obter informações técnicas adicionais sobre o mecanismo interno de inteligência, consulte [Ajuste automático do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Usar ajuste automático

O ajuste automático precisa ser habilitado em sua assinatura. Para habilitar o ajuste automático usando o portal do Azure, consulte [Habilitar ajuste automático](sql-database-automatic-tuning-enable.md).

O ajuste automático pode funcionar de maneira autônoma aplicando automaticamente as recomendações de ajuste, incluindo a verificação automatizada de ganhos de desempenho. 

Para obter mais controle, a aplicação automática de recomendações de ajuste pode ser desativada e recomendações de ajuste podem ser aplicadas manualmente por meio do portal do Azure. Também é possível usar a solução para exibir somente recomendações de ajuste automatizadas e aplicá-las manualmente por meio de scripts e ferramentas de sua escolha. 

Para uma visão geral de como o ajuste automático funciona em cenários de uso típicos, assista ao vídeo inserido:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opções de ajuste automático

As opções de ajuste automático disponíveis no Banco de Dados SQL do Azure são:

| Opção de ajuste automático | Suporte a banco de dados individual e banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Criar índice** – identifica índices que podem melhorar o desempenho de sua carga de trabalho, cria índices e verifica automaticamente se o desempenho das consultas foi melhorado. | Sim | Não | 
| **Drop index** -identifica índices redundantes e duplicados diariamente, exceto índices exclusivos, e índices que não foram usados por um longo tempo (> 90 dias). Observe que essa opção não é compatível com aplicativos que usam alternância de partição e dicas de índice. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico. | Sim | Não |
| **Forçar último plano bom** (correção de plano automática) – identifica consultas SQL usando o plano de execução que é mais lento do que o bom plano anterior e consultas usando o último plano bom conhecido em vez do plano regressivo. | Sim | Sim |

O ajuste automático identifica recomendações de **CREATE INDEX**, **DROP INDEX** e **FORCE LAST GOOD PLAN** que podem otimizar o desempenho de seu banco de dados, as mostra no [Portal do Azure](sql-database-advisor-portal.md) e as expõe por meio de [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) e da [API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Para saber mais sobre o último bom plano e a configuração das opções de ajuste automático por meio do T-SQL, consulte o [ajuste automático introduz a correção automática do plano](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Você pode aplicar manualmente as recomendações de ajuste usando o portal ou pode permitir que o ajuste automático aplique de forma autônoma as recomendações de ajuste para você. Os benefícios de permitir que o sistema aplique recomendações de ajuste autonomamente para você é que ele valida automaticamente que existe um ganho positivo no desempenho da carga de trabalho e, se não houver nenhuma melhoria de desempenho significativa detectada, ele reverterá automaticamente a recomendação de ajuste. Observe que, no caso de consultas afetadas por recomendações de ajuste que não são executadas com frequência, a fase de validação pode levar até 72 horas por design.

Caso você esteja aplicando recomendações de ajuste por meio do T-SQL, os mecanismos validação de desempenho automático e reversão não estão disponíveis. As recomendações aplicadas de forma que permanecerão ativas e mostradas na lista de recomendações de ajuste para 24-48 horas. antes que o sistema as retire automaticamente. Se você quiser remover uma recomendação mais cedo, poderá descartá-la de portal do Azure.

As opções de ajuste automático podem ser habilitadas ou desabilitadas independentemente por banco de dados ou podem ser configuradas em servidores do Banco de Dados SQL e aplicadas em todos os bancos de dados que herdam as configurações do servidor. Os servidores do Banco de Dados SQL podem herdar os padrões do Azure para as configurações de Ajuste automático. Atualmente, os padrões do Azure estão definidos como FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

Configurar as opções de ajuste Automático em um servidor e herdar as configurações dos bancos de dados pertencentes ao servidor pai é um método recomendado para configurar o ajuste automático, pois simplifica o gerenciamento de opções de ajuste automático para um grande número de bancos de dados.

## <a name="next-steps"></a>Próximas etapas

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure para gerenciar a carga de trabalho, consulte [Habilitar ajuste automático](sql-database-automatic-tuning-enable.md).
- Para examinar e aplicar recomendações de Ajuste automático manualmente, consulte [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
- Para saber como usar T-SQL para aplicar e exibir recomendações de ajuste automático, consulte [Gerenciar o ajuste automático por meio de T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Para saber mais sobre a criação de notificações por email para as recomendações de Ajuste automático, consulte [Notificações por email para ajuste automático](sql-database-automatic-tuning-email-notifications.md).
- Para saber mais sobre a inteligência interna usada no Ajuste automático, confira [A Inteligência Artificial ajusta os bancos de dados SQL do Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Para obter informações sobre como o Ajuste automático funciona no Banco de Dados SQL do Azure e no SQL Server de 2017, consulte [Ajuste automático do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
