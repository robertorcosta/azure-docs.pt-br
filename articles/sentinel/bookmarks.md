---
title: Use marcadores de caça para investigações de dados no Azure Sentinel
description: Este artigo descreve como usar os marcadores de caça do Azure Sentinel para acompanhar os dados.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588681"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Acompanhe os dados durante a caça com o Azure Sentinel

A caça de ameaças normalmente requer a revisão de montanhas de dados de registro à procura de evidências de comportamento malicioso. Durante esse processo, os investigadores encontram eventos que querem lembrar, revisitam e analisam como parte da validação de hipóteses potenciais e compreensão da história completa de um compromisso.

Os marcadores de caça no Azure Sentinel ajudam você a fazer isso, preservando as consultas que você executou no **Azure Sentinel - Logs**, juntamente com os resultados de consulta que você julgar relevantes. Você também pode registrar suas observações contextuais e referenciar suas descobertas adicionando notas e tags. Os dados marcados são visíveis para você e seus colegas de equipe para fácil colaboração.

Você pode revisitar seus dados marcados a qualquer momento na guia **Marcadores** do painel **Caça-** Cabeças. Você pode usar opções de filtragem e pesquisa para encontrar rapidamente dados específicos para sua investigação atual. Alternativamente, você pode visualizar seus dados marcados diretamente na tabela **HuntingBookmark** em seu espaço de trabalho Log Analytics. Por exemplo: 

> [!div class="mx-imgBorder"]
> ![exibir tabela HuntingBookmark](./media/bookmarks/bookmark-table.png)

A visualização de marcadores da tabela permite filtrar, resumir e juntar dados marcados com outras fontes de dados, facilitando a busca de evidências corroboradoras.

Atualmente em visualização, se você encontrar algo que precisa ser abordado com urgência durante a caça em seus registros, em alguns cliques, você pode criar um marcador e promovê-lo a um incidente, ou adicionar o marcador a um incidente existente. Para obter mais informações sobre incidentes, consulte [Tutorial: Investigue incidentes com o Azure Sentinel](tutorial-investigate-cases.md). 

Também na visualização, você pode visualizar seus dados marcados, clicando **em Investigar** a partir dos detalhes do marcador. Isso inicia a experiência de investigação na qual você pode visualizar, investigar e comunicar visualmente suas descobertas usando um diagrama e cronograma interativos de gráficos de entidade.

## <a name="add-a-bookmark"></a>Adicionar um indicador

1. No portal Azure, navegue até **o Sentinel** > **Threat Management** > **Hunting** para executar consultas por comportamento suspeito e anômalo.

2. Selecione uma das consultas de caça e à direita, nos detalhes da consulta de caça, **selecione Run Query**. 

3. Selecione **Exibir resultados da consulta**. Por exemplo: 
    
    > [!div class="mx-imgBorder"]
    > ![ver resultados de consulta da caça Azure Sentinel](./media/bookmarks/new-processes-observed-example.png)
    
    Esta ação abre os resultados da consulta no painel **Logs.**

4. Na lista de resultados da consulta de log, use as caixas de seleção para selecionar uma ou mais linhas que contenham as informações que você achar interessantes.

5. Selecione **Adicionar marcador:**
    
    > [!div class="mx-imgBorder"]
    > ![Adicionar marcador de caça à consulta](./media/bookmarks/add-hunting-bookmark.png)

6. À direita, no painel **Adicionar marcador,** opcionalmente, atualize o nome do marcador, adicione tags e notas para ajudá-lo a identificar o que era interessante sobre o item.

7. Na seção **Informações de consulta,** use as caixas de saque para extrair informações dos resultados da consulta para os tipos de entidade de endereço **de conta,** **host**e **IP.** Esta ação mapeia o tipo de entidade selecionada para uma coluna específica a partir do resultado da consulta. Por exemplo: 
    
    > [!div class="mx-imgBorder"]
    > ![Mapear tipos de entidades para marcar de caça](./media/bookmarks/map-entity-types-bookmark.png)
    
    Para visualizar o marcador no gráfico de investigação (atualmente em visualização), você deve mapear pelo menos um tipo de entidade que seja **de Conta,** **Host**ou **endereço IP**. 

5. Clique **em Salvar** para cometer suas alterações e adicionar o marcador. Todos os dados marcados são compartilhados com outros investigadores, e é um primeiro passo para uma experiência de investigação colaborativa.

 
> [!NOTE]
> Os resultados da consulta de log suportam marcadores sempre que este painel é aberto do Azure Sentinel. Por exemplo, você seleciona**Registros** **Gerais** > na barra de navegação, seleciona links de eventos no gráfico de investigações ou seleciona um ID de alerta nos detalhes completos de um incidente (atualmente em visualização). Você não pode criar marcadores quando o painel **Logs** é aberto de outros locais, como diretamente do Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Exibir e atualizar marcadores 

1. No portal Azure, navegue até a Caça**ao Gerenciamento de** > Ameaças **Sentinelas** > .**Hunting** 

2. Selecione a guia **Marcadores** para exibir a lista de marcadores.

3. Para ajudá-lo a encontrar um marcador específico, use a caixa de pesquisa ou as opções de filtro.

4. Selecione marcadores individuais e visualize os detalhes do marcador no painel de detalhes à direita.

5. Faça suas alterações conforme necessário, que são automaticamente salvas.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Explorando marcadores no gráfico de investigação

> [!IMPORTANT]
> A exploração de marcadores no gráfico de investigação e no próprio gráfico de investigação estão atualmente em visualização pública.
> Esses recursos são fornecidos sem um contrato de nível de serviço e não recomendados para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. No portal Azure, navegue até a guia **Sentinel** > **Threat Management** > **Hunting** > **Bookmarks** e selecione os marcadores ou marcadores que deseja investigar.

2. Nos detalhes do marcador, certifique-se de que pelo menos uma entidade seja mapeada. Por exemplo, para **ENTIDADES,** você vê entradas para **IP,** **Máquina**ou **Conta.**

3. Clique **em Investigar** para ver o marcador no gráfico de investigação.

Para obter instruções para usar o gráfico de investigação, [consulte Use o gráfico de investigação para mergulhar profundamente](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Adicionar marcadores a um incidente novo ou existente

> [!IMPORTANT]
> A adição de marcadores a um incidente novo ou existente está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. No portal Azure, navegue até a guia **Sentinel** > **Threat Management** > **Hunting** > **Bookmarks** e selecione os marcadores ou marcadores que deseja adicionar a um incidente.

2. Selecione **''Visualização'** de ações de incidente (Visualização) na barra de comando:
    
    > [!div class="mx-imgBorder"]
    > ![Adicionar marcadores ao incidente](./media/bookmarks/incident-actions.png)

3. Selecione **Criar novo incidente** ou Adicionar ao incidente **existente,** conforme necessário. Em seguida:
    
    - Para um novo incidente: Atualize opcionalmente os detalhes do incidente e selecione **Criar**.
    - Para adicionar um marcador a um incidente existente: selecione um incidente e, em seguida, **selecione Adicionar**. 

Para visualizar o marcador dentro do incidente: Navegue até **o Sentinel** > **Threat Management** > **Incidents** e selecione o incidente com seu marcador. Selecione **Exibir detalhes completos**e selecione a guia **Marcadores.**

> [!TIP]
> Como alternativa à opção **'''Visualização' de ações incidentes (Visualização)** na barra de comando, você pode usar o menu de contexto (**...**) para um ou mais marcadores para selecionar opções para **Criar novo incidente,** **Adicionar ao incidente existente**e remover de **incidente**. 

## <a name="view-bookmarked-data-in-logs"></a>Exibir dados marcados em logs

Para exibir consultas, resultados ou histórico marcados, selecione o marcador na guia**Marcadores** de **caça** > e use os links fornecidos no painel de detalhes: 

- **Exibir consulta de origem** para exibir a consulta de origem no painel **Logs.**

- **Veja os registros de marcadores** para ver todos os metadados do marcador, o que inclui quem fez a atualização, os valores atualizados e a hora em que a atualização ocorreu.

Você também pode visualizar os dados de marcadores **brutos** para todos os marcadores selecionando Logs de marcadores na barra de comando na guia**Marcadores de** **caça:** > 

> [!div class="mx-imgBorder"]
> ![Logs de marcadores](./media/bookmarks/bookmark-logs.png)

Esta exibição mostra todos os seus marcadores com metadados associados. Você pode usar consultas [kql (Keyword Query Language)](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) para filtrar até a versão mais recente do marcador específico que você está procurando.

> [!NOTE]
> Pode haver um atraso significativo (medido em minutos) entre o momento em que você cria um marcador e quando ele é exibido na guia **Marcadores.**

## <a name="delete-a-bookmark"></a>Exclua um marcador
 
1.  No portal Azure, navegue até a guia **Sentinel** > **Threat management** > **Hunting** > **Bookmarks** e selecione os marcadores ou marcadores que deseja excluir. 

2. Clique com o botão direito do mouse em suas seleções e selecione a opção de excluir o marcador ou marcadores. Por exemplo, **Exclua marcador** se você selecionou apenas um marcador e **exclua 2 marcadores** se você selecionou dois marcadores.
    
A exclusão do marcador remove o marcador da lista na guia **Marcador.** A tabela **HuntingBookmark** para o espaço de trabalho do Log Analytics continuará a conter entradas de marcadores anteriores, mas a entrada mais recente mudará o valor **do SoftDelete** para true, facilitando a filtragem de marcadores antigos. A exclusão de um marcador não remove nenhuma entidade da experiência de investigação que esteja associada a outros marcadores ou alertas. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a executar uma investigação de caça usando marcadores no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Caça proativamente por ameaças](hunting.md)
- [Use notebooks para executar campanhas de caça automatizadas](notebooks.md)
