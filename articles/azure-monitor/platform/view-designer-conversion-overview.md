---
title: Azure Monitor vê designer para guia de transição de livros de trabalho
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658686"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor vê designer para guia de transição de livros de trabalho
[O View designer](view-designer.md) é um recurso do Azure Monitor que permite criar visualizações personalizadas para ajudá-lo a visualizar dados em seu espaço de trabalho do Log Analytics, com gráficos, listas e cronogramas. Eles estão sendo eliminados e substituídos por livros de trabalho que fornecem funcionalidade adicional. Este artigo fornece uma visão geral do processo para converter suas visualizações existentes em livros de trabalho.

## <a name="workbooks-overview"></a>Visão geral dos livros de trabalho
[As pastas](../insights/vminsights-workbooks.md) de trabalho combinam texto, [consultas de log,](../log-query/query-language.md)métricas e parâmetros em relatórios interativos ricos. Os membros da equipe com o mesmo acesso aos recursos do Azure também são capazes de editar livros de trabalho.

As regras de trabalho são úteis para cenários como:

-   Explorando o uso de sua máquina virtual quando você não sabe as métricas de interesse antecipadamente: utilização da CPU, espaço em disco, memória, dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, os livros de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para esse tipo de exploração de forma livre.
-   Explicando à sua equipe como uma VM recentemente provisionada está se saindo, mostrando métricas para contadores-chave e outros eventos de log.
-   Compartilhando os resultados de um experimento de redimensionamento de sua VM com outros membros da sua equipe. Você pode explicar os objetivos do experimento com texto e, em seguida, mostrar cada métrica de uso e consultas de análise usadas para avaliar o experimento, juntamente com chamadas claras para saber se cada métrica estava acima ou abaixo do alvo.
-   Reportando o impacto de uma paralisação no uso de sua VM, combinando dados, explicação de texto e uma discussão sobre os próximos passos para evitar paralisações no futuro.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Por que converter painéis de designer de visualização em livros de trabalho?

O designer de visualização oferece a capacidade de gerar diferentes visualizações e visualizações baseadas em consultas. No entanto, muitas personalizações de alto nível permanecem limitadas, como formatar as grades e layouts de peça ou selecionar gráficos alternativos para representar seus dados. O designer de visualização está restrito a um total de nove telhas distintas para representar seus dados.

As Pastas de Trabalho são uma plataforma que desbloqueia todo o potencial dos seus dados. os livros de trabalho não apenas retêm todos os recursos, mas também suportam funcionalidades adicionais através de texto, métricas, parâmetros e muito mais. Por exemplo, as cadernetas permitem que os usuários consolidem grades densas e adicionem barras de pesquisa para filtrar e analisar facilmente os dados. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vantagens de usar workbooks sobre view designer

* Suporta registros e métricas.
* Permite tanto visualizações pessoais para controle de acesso individual quanto visualizações compartilhadas de livros de trabalho.
* Opções de layout personalizadas com guias, dimensionamento e controles de dimensionamento.
* Suporte para consultas em vários espaços de trabalho do Log Analytics, aplicativos de criteriose e assinaturas.
* Permite parâmetros personalizados que atualizem dinamicamente gráficos e visualizações associadas.
* Suporte a galeria de modelos do GitHub público.

Embora este guia ofereça passos simples para recriar diretamente várias das visualizações de designers de visualizações comumente usadas, os livros de trabalho permitem que os usuários tenham a liberdade de criar e projetar qualquer uma de suas próprias visualizações e métricas personalizadas. A captura de tela a seguir é do modelo de uso do [Workspace](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) e mostra um exemplo do que as livros de trabalho são capazes de criar:


![Exemplo de aplicação de livros de trabalho](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Como começar a usar livros de trabalho
As regras de trabalho abertas das Livros de Trabalho são habilitadas nos espaços de trabalho do Log Analytics como um item na barra de navegação lateral, diretamente a localização do designer de exibição.

![Navegação em livros de trabalho](media/view-designer-conversion-overview/workbooks-nav.png)

Uma vez selecionada, uma galeria será exibida listando todas as regras de trabalho e modelos salvos para o seu espaço de trabalho.

![Galeria de livros de trabalho](media/view-designer-conversion-overview/workbooks-gallery.png)

Para iniciar uma nova carteira de trabalho, você pode selecionar o modelo **Vazio** em **Início rápido**ou o ícone **Novo** na barra de navegação superior. Para visualizar modelos ou retornar às cadernetas salvas, selecione o item na galeria ou pesquise o nome na barra de pesquisa.

Para salvar uma carteira de trabalho, você precisará salvar o relatório com um título específico, assinatura, grupo de recursos e localização.
A carteira de trabalho será preenchida automaticamente nas mesmas configurações do espaço de trabalho de LA, com a mesma assinatura, grupo de recursos, no entanto, os usuários podem alterar essas configurações do relatório. As cadernetas de trabalho são salvas por padrão em *Meus Relatórios,* acessíveis apenas pelo usuário individual. Eles também podem ser salvos diretamente em relatórios compartilhados ou compartilhados posteriormente.

![Livros de trabalho salvam](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Próximas etapas

- [Opções de conversão](view-designer-conversion-options.md)
