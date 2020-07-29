---
title: Guia de transição do designer de Azure Monitor exibição para pastas de trabalho
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 977dcc71fd25b19e09cfa9126bf01d380f581aca
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289108"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Guia de transição do designer de Azure Monitor exibição para pastas de trabalho
O [Designer de exibição](view-designer.md) é um recurso do Azure monitor que permite que você crie modos de exibição personalizados para ajudá-lo a Visualizar dados em seu espaço de trabalho do log Analytics, com gráficos, listas e linhas do tempo. Eles estão sendo desativados e substituídos por pastas de trabalho que fornecem funcionalidade adicional. Este artigo fornece uma visão geral do processo de conversão de suas exibições existentes em pastas de trabalho do.

## <a name="retirement-schedule"></a>Cronograma de desativação

| Alterar | O que isso significa | Data esperada |
|:---|:---|:---|
| Desabilite a criação de novas exibições criadas por meio do designer de exibição. | Você não poderá mais criar e salvar novas exibições personalizadas no portal do Azure.| Novembro de 2020 |
| Desabilite a função editar para exibições existentes com o designer de exibição. | Você não poderá mais modificar e salvar as alterações em seus modos de exibição personalizados existentes. | Novembro de 2020 |
| Desabilitar a implantação de modos de exibição em Log Analytics espaços de trabalho | Você não poderá mais implantar exibições personalizadas usando o ARM para Log Analytics espaços de trabalho. | Março de 2021 |
| O designer de exibição não está mais disponível no portal do Azure | A experiência do portal não dará mais suporte ao designer de exibição. | Junho de 2021 |
| Exibições personalizadas removidas do resumo do espaço de trabalho | Você não poderá mais acessar seus dados de exibição personalizados. | Dezembro de 2021 |

## <a name="workbooks-overview"></a>Visão geral das pastas de trabalho
As [pastas de trabalho](../insights/vminsights-workbooks.md) combinam texto, consultas de [log](../log-query/query-language.md), métricas e parâmetros em relatórios interativos sofisticados. Os membros da equipe com o mesmo acesso aos recursos do Azure também podem editar pastas de trabalho.

As pastas de trabalho são úteis para cenários como:

-   Explorando o uso de sua máquina virtual quando você não conhece as métricas de interesse com antecedência: utilização da CPU, espaço em disco, memória, dependências de rede etc. Ao contrário de outras ferramentas de análise de uso, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
-   Explicando à sua equipe como uma VM provisionada recentemente está sendo executada, mostrando métricas para contadores-chave e outros eventos de log.
-   Compartilhando os resultados de um teste de redimensionamento de sua VM com outros membros de sua equipe. Você pode explicar as metas para o experimento com texto e, em seguida, mostrar cada métrica de uso e as consultas de análise usadas para avaliar o experimento, juntamente com as chamadas claras para se cada métrica estava acima ou abaixo do destino.
-   Relatar o impacto de uma interrupção no uso de sua VM, combinar dados, explicação de texto e uma discussão das próximas etapas para evitar interrupções no futuro.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Por que converter painéis do designer de exibição em pastas de trabalho?

O designer de exibição oferece a capacidade de gerar diferentes exibições e visualizações baseadas em consulta. No entanto, muitas personalizações de alto nível permanecem limitadas, como formatar as grades e layouts de peça ou selecionar gráficos alternativos para representar seus dados. O designer de exibição é restrito a um total de nove blocos distintos para representar seus dados.

As Pastas de Trabalho são uma plataforma que desbloqueia todo o potencial dos seus dados. as pastas de trabalho não só retêm todos os recursos, mas também oferecem suporte a funcionalidades adicionais por meio de texto, métricas, parâmetros e muito mais. Por exemplo, as pastas de trabalho permitem que os usuários consolidem grades densas e adicionem barras de pesquisa para filtrar e analisar os dados com facilidade. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vantagens de usar pastas de trabalho no designer de exibição

* Dá suporte a logs e métricas.
* Permite exibições pessoais para as exibições de controle de acesso individual e pastas de trabalho compartilhadas.
* Opções de layout personalizadas com guias, dimensionamento e controles de dimensionamento.
* Suporte para consulta em vários espaços de trabalho Log Analytics, Application Insights aplicativos e assinaturas.
* Habilita parâmetros personalizados que atualizam dinamicamente gráficos e visualizações associados.
* Suporte da Galeria de modelos do GitHub público.

Embora este guia ofereça etapas simples para recriar diretamente várias das exibições do designer de exibição comumente usadas, as pastas de trabalho permitem que os usuários tenham a liberdade de criar e projetar qualquer uma de suas próprias visualizações e métricas personalizadas. A captura de tela a seguir é do [modelo de uso do espaço de trabalho](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) e mostra um exemplo de quais pastas de trabalho são capazes de criar:


![Exemplo de aplicativo de pastas de trabalho](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Como começar a usar pastas de trabalho
As pastas de trabalho abertas das pastas de trabalho são habilitadas nos espaços de trabalho do Log Analytics como um item na barra de navegação lateral, diretamente sob o local do designer de exibição.

![Navegação de pastas de trabalho](media/view-designer-conversion-overview/workbooks-nav.png)

Depois de selecionado, uma galeria será exibida listando todas as pastas de trabalho e modelos salvos para seu espaço de trabalho.

![Galeria de pastas de trabalho](media/view-designer-conversion-overview/workbooks-gallery.png)

Para iniciar uma nova pasta de trabalho, você pode selecionar o modelo **vazio** em **início rápido**ou o ícone **novo** na barra de navegação superior. Para exibir modelos ou retornar a pastas de trabalho salvas, selecione o item da galeria ou procure o nome na barra de pesquisa.

Para salvar uma pasta de trabalho, será necessário salvar o relatório com um título, uma assinatura, um grupo de recursos e um local específicos.
A pasta de trabalho fará preenchimento automático das mesmas configurações que o espaço de trabalho LA, com a mesma assinatura, grupo de recursos, no entanto, os usuários poderão alterar essas configurações de relatório. As pastas de trabalho são salvas por padrão em *meus relatórios*, acessíveis somente pelo usuário individual. Eles também podem ser salvos diretamente em relatórios compartilhados ou compartilhados posteriormente.

![Salvar pastas de trabalho](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Próximas etapas

- [Opções de conversão](view-designer-conversion-options.md)
