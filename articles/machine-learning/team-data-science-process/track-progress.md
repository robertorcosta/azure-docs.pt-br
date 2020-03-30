---
title: Acompanhe o progresso dos projetos do Processo de Ciência de Dados da Equipe
description: Como gerentes de grupos de ciência de dados, líderes de equipe e líderes de projetos podem acompanhar o progresso de um projeto de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864189"
---
# <a name="track-the-progress-of-data-science-projects"></a>Acompanhe o andamento de projetos de ciência de dados

Os gerentes de grupos de ciência de dados, líderes de equipe e líderes de projetos podem acompanhar o andamento de seus projetos.  Os gestores querem saber que trabalho foi feito, quem fez o trabalho e o que resta do trabalho.   Gerenciar expectativas é um elemento importante de sucesso.

## <a name="azure-devops-dashboards"></a>Dashboards do Azure DevOps

Se você estiver usando o Azure DevOps, você pode construir dashboards para rastrear as atividades e itens de trabalho associados a um determinado projeto Ágil. Para obter mais informações sobre dashboards, consulte [Dashboards, relatórios e widgets](/azure/devops/report/dashboards/).

Para obter instruções sobre como criar e personalizar dashboards e widgets no Azure DevOps, consulte as seguintes partidas rápidas:

- [Adicionar e gerenciar painéis](/azure/devops/report/dashboards/dashboards)
- [Adicionar widgets a um painel](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Painel Exemplo

Aqui está um painel de exemplo simples que rastreia as atividades de sprint de um projeto ágil de ciência de dados, incluindo o número de compromissos com repositórios associados. 

- O azulejo **de contagem regressiva** mostra o número de dias que permanecem no sprint atual. 

- As duas **telhas de código** mostram o número de compromissos nos dois repositórios do projeto nos últimos sete dias. 

- **Itens de trabalho para o TDSP Customer Project** mostram os resultados de uma consulta para todos os itens de trabalho e seu status. 

- Um **diagrama de fluxo cumulativo** (CFD) mostra o número de itens de trabalho fechados e ativos.

- O **gráfico de burndown** mostra trabalho ainda para completar contra o tempo restante no sprint.

- O **gráfico de burnup** mostra o trabalho concluído em comparação com a quantidade total de trabalho no sprint.

![Painel](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

[Passo a passo executando o Processo de Ciência de Dados da Equipe](walkthroughs.md) lista passo a passo que demonstram todas as etapas do processo. Os cenários vinculados ilustram como gerenciar os recursos em nuvem e on-premise em aplicativos inteligentes. 
