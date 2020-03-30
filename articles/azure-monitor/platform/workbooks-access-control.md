---
title: Controle de acesso do Azure Monitor Workbooks
description: Simplifique relatórios complexos com livros de trabalho pré-construídos e personalizados com controle de acesso baseado em função
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658414"
---
# <a name="access-control"></a>Controle de acesso

O controle de acesso nas cadernetas refere-se a duas coisas:

* Acesso necessário para ler dados em uma carteira de trabalho. Esse acesso é controlado por funções padrão [do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) sobre os recursos utilizados na pasta de trabalho. As abdominais não especificam ou configuram acesso a esses recursos. Os usuários geralmente obtêm esse acesso a esses recursos usando a função [Leitor de Monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) nesses recursos.

* Acesso necessário para salvar livros de trabalho

    - Salvar `("My")` livros de trabalho privados não requer privilégios adicionais. Todos os usuários podem salvar livros de trabalho privados, e só eles podem ver essas livros de trabalho.
    - Salvar livros de trabalho compartilhados requer privilégios de gravação em um grupo de recursos para salvar a carteira de trabalho. Esses privilégios geralmente são especificados pela função [Contribuinte de Monitoramento,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) mas também podem ser definidos através da função *Contribuinte de Livros de Trabalho.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Funções padrão com privilégios relacionados à carteira de trabalho

[O Monitorando o Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) inclui privilégios padrão/leitura que seriam usados por ferramentas de monitoramento (incluindo livros de trabalho) para ler dados de recursos.

[O Contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) de `/write` Monitoramento inclui privilégios gerais usados por `workbooks/write` várias ferramentas de monitoramento para salvar itens (incluindo privilégio de salvar livros de trabalho compartilhados).
"Contribuinte de livros de trabalho" adiciona privilégios de "livros de trabalho/gravação" a um objeto para salvar livros de trabalho compartilhados.
Não são necessários privilégios especiais para que os usuários salvem livros de trabalho privados que só eles podem ver.

Para controle de acesso personalizado baseado em função:

Adicione `microsoft.insights/workbooks/write` para salvar livros de trabalho compartilhados. Para obter mais detalhes, consulte a função [Contribuinte da Carteira de Trabalho.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
