---
title: Inventário de ativos da central de segurança do Azure
description: Saiba mais sobre a experiência de gerenciamento de ativos da central de segurança do Azure, fornecendo visibilidade total sobre todos os recursos monitorados da central de segurança.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263868"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Explore e gerencie seus recursos com o inventário de ativos e as ferramentas de gerenciamento

A página inventário de ativos da central de segurança do Azure fornece uma única página para exibir a postura de segurança dos recursos que você conectou à central de segurança. 

A central de segurança analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele fornece recomendações sobre como corrigir essas vulnerabilidades.

Quando qualquer recurso tiver recomendações pendentes, eles aparecerão no inventário.

Use esta exibição e seus filtros para resolver tais perguntas como:

- Quais das minhas assinaturas da camada Standard têm recomendações pendentes?
- Quais dos meus computadores com a marca ' produção ' estão faltando o agente de Log Analytics?
- Quantos de meus computadores, marcados com uma marca específica, têm recomendações pendentes?
- Quantos recursos em um grupo de recursos específico têm conclusões de segurança de um serviço de avaliação de vulnerabilidade?

As possibilidades de gerenciamento de ativos para essa ferramenta são substanciais e continuam crescendo. 


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Visualização|
|Refere|Gratuito|
|Funções e permissões necessárias:|todos os usuários|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Quais são os principais recursos do inventário de ativos?

A página inventário fornece as seguintes ferramentas:

- **Resumos** -antes de definir qualquer filtro, uma faixa de valores proeminente na parte superior da exibição de inventário mostra:

    - **Total de recursos**: o número total de recursos conectados à central de segurança.
    - **Recursos não íntegros**: recursos com recomendações de segurança ativas. [Saiba mais sobre as recomendações de segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - **Recursos não monitorados**: recursos com problemas de monitoramento de agente-eles têm o agente de log Analytics implantado, mas o agente não está enviando dados ou tem outros problemas de integridade.

- **Filtros** – os vários filtros na parte superior da página fornecem uma maneira de refinar rapidamente a lista de recursos de acordo com a pergunta que você está tentando responder. Por exemplo, se você quisesse responder à pergunta *quais dos meus computadores com a marca "produção" estão sem o agente de log Analytics?* você pode combinar o filtro de **monitoramento de agente** com o filtro de **marcas** , conforme mostrado no seguinte clipe:

    ![Filtragem para recursos de produção que não são monitorados](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Assim que você aplicou filtros, os valores de resumo são atualizados para se relacionar aos resultados da consulta. 

- **Opções de exportação** – o inventário fornece a opção de exportar os resultados de suas opções de filtro selecionadas para um arquivo CSV. Além disso, você pode exportar a consulta em si para o Gerenciador de grafo de recursos do Azure para refinar, salvar ou modificar a consulta KQL.

    ![Opções de exportação do inventário](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A documentação do KQL fornece um banco de dados com algumas consultas de exemplo junto com algumas simples para obter a "sensação" da linguagem. [Saiba mais neste tutorial de KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Opções de gerenciamento de ativos** – o inventário permite que você execute consultas de descoberta complexas. Quando você encontrar os recursos que correspondem às suas consultas, o inventário fornecerá atalhos para operações como:

    - Atribuir marcas aos recursos filtrados – marque as caixas de seleção ao lado dos recursos que você deseja marcar
    - Carregar novos servidores para a central de segurança-Use o botão de barra de ferramentas **adicionar servidores não Azure**


## <a name="how-does-asset-inventory-work"></a>Como funciona o inventário de ativos?

O inventário de ativos utiliza o [grafo de recursos do Azure (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), um serviço do Azure que fornece a capacidade de consultar dados de postura de segurança da central de segurança em várias assinaturas.

ARG foi projetado para fornecer uma exploração eficiente de recursos com a capacidade de consultar em escala.

Usando a [linguagem de consulta Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/), o inventário de ativos pode produzir rapidamente percepções aprofundadas por meio da referência cruzada de dados ASC com outras propriedades de recurso.


## <a name="how-to-use-asset-inventory"></a>Como usar o inventário de ativos

1. Na barra lateral da central de segurança, selecione **inventário**.

1. Opcionalmente, para exibir um recurso específico, insira o nome na caixa **Filtrar por nome** .

1. Selecione as opções relevantes nos filtros para criar a consulta específica que você deseja executar.

    ![Filtros do inventário](./media/asset-inventory/inventory-filters.png)

    Por padrão, os recursos são classificados pelo número de recomendações de segurança ativas.

    > [!IMPORTANT]
    > As opções em cada filtro são específicas para os recursos nas assinaturas atualmente selecionadas **e** suas seleções nos outros filtros.
    >
    > Por exemplo, se você selecionou apenas uma assinatura e a assinatura não tiver recursos com recomendações de segurança pendentes para corrigir (0 recursos não íntegros), o filtro de **recomendações** não terá opções. 

1. Para usar as **conclusões de segurança, contenha** o filtro, insira o texto livre da ID, verificação de segurança ou nome do CVE de uma vulnerabilidade que está encontrando para filtrar os recursos afetados:

    ![Filtro "conclusões de segurança contêm"](./media/asset-inventory/security-findings-contain-elements.png)

1. Para usar o filtro **tipo de preço** , selecione uma ou mais opções (gratuita, parcial ou padrão):

    - Recursos **gratuitos** que estão no tipo de preço gratuito
    - **Standard** -recursos que estão no tipo de preço Standard
    - **Parcial** -isso se aplica a assinaturas que estão no tipo de preço Standard, mas que têm alguns dos planos de segurança opcionais desabilitados. Por exemplo, a assinatura a seguir está na camada Standard, mas tem cinco elementos da camada Standard desabilitada. 

        ![Assinatura no tipo de preço Standard (parcial)](./media/asset-inventory/pricing-tier-partial.png)

1. Para examinar melhor os resultados da consulta, selecione os recursos que lhe interessam.

1. Opcionalmente, selecione **Exibir no Gerenciador de grafo de recursos** para abrir a consulta no Gerenciador de grafo de recursos.

    ![Consulta de inventário em ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Se você definiu alguns filtros e deixou a página aberta, a central de segurança não atualizará os resultados automaticamente. As alterações nos recursos não afetarão os resultados exibidos, a menos que você recarregue a página manualmente ou selecione **Atualizar**.


## <a name="faq---inventory"></a>Perguntas frequentes-inventário

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Por que todas as minhas assinaturas, máquinas, contas de armazenamento, etc. são mostradas?

A exibição de inventário lista os recursos de uma perspectiva do CSPM (gerenciamento de postura de segurança na nuvem). Os filtros não retornam todos os recursos em seu ambiente; somente aqueles com recomendações pendentes (ou ' ativas '). 

Por exemplo, se você tiver nove assinaturas, mas apenas oito têm recomendações no momento, ao filtrar por **tipo de recurso = assinaturas** , você verá apenas as oito assinaturas com recomendações ativas:

![Nem todas as sub-rotinas retornaram quando não há nenhuma recomendação ativa](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Por que alguns dos meus recursos mostram valores em branco nas colunas de monitoramento de preço ou de agente?

Nem todos os recursos monitorados da central de segurança têm agentes. Por exemplo, contas de armazenamento do Azure ou recursos de PaaS, como discos, aplicativos lógicos, análise de Data Lake e Hub de eventos.

Quando o monitoramento de preços ou agentes não for relevante para um recurso, nada será mostrado nessas colunas de inventário.

![Alguns recursos mostram informações em branco nas colunas de monitoramento ou de preços do agente](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a página inventário de ativos da central de segurança do Azure.

Para obter mais informações sobre as ferramentas relacionadas, consulte as seguintes páginas:

- [Grafo de recursos do Azure (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)