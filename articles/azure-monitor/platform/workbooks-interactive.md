---
title: Livros de trabalho do Azure Monitor com parâmetros personalizados
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658261"
---
# <a name="interactive-workbooks"></a>Pastas de trabalho interativas

Os livros de trabalho permitem que os autores criem relatórios e experiências interativas para seus consumidores. A interatividade é suportada de várias maneiras.

## <a name="parameter-changes"></a>Mudanças de parâmetros
Quando um usuário de uma carteira de trabalho atualiza um parâmetro, qualquer controle que use o parâmetro atualiza e redesenha automaticamente para refletir o novo estado. É assim que a maioria dos relatórios do portal Azure suportam a interatividade. Os livros de trabalho fornecem isso de uma maneira muito direta, com o mínimo de esforço do usuário.

Saiba mais sobre [parâmetros em livros de trabalho](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Cliques na linha de grade
Os livros de trabalho permitem que os autores construam cenários onde clicar em uma linha em uma grade atualiza gráficos subseqüentes com base no conteúdo da linha. 

Por exemplo, um usuário pode ter uma grade que mostra uma lista de solicitações e algumas estatísticas como contagem de falhas. Eles poderiam configurá-lo de tal forma que clicar em uma linha correspondente a uma solicitação, resultará em gráficos detalhados abaixo atualizando para filtrar até apenas essa solicitação.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Configurando a interatividade no clique da linha de grade
1. Alterne a carteira de trabalho para editar o modo clicando no item _Editar_ barra de ferramentas.
2. Use o _link Adicionar consulta_ para adicionar um controle de consulta de log à carteira de trabalho. 
3. Selecione o tipo de consulta como _Log_, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o KQL para sua análise
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`para ver os resultados
6. Clique no ícone _Configurações Avançadas_ no rodapé de consulta (o ícone parece uma engrenagem). Isso abre o painel de configurações avançadas 
7. Verifique a configuração:`When an item is selected, export a parameter`
    1. Campo para exportação:`Request`
    2. Nome do parâmetro:`SelectedRequest`
    3. Valor padrão: `All requests`
    
    ![Imagem mostrando o editor avançado com configurações para campos de exportação como parâmetros](./media/workbooks-interactive/advanced-settings.png)

8. Clique em `Done Editing`.
9. Adicione outro controle de consulta usando as etapas 2 e 3.
10. Use o editor de consulta para inserir o KQL para sua análise
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`para ver os resultados.
12. Alterar _visualização_ para`Area chart`
12. Clique em uma linha na primeira grade. Observe como o gráfico de área abaixo filtra para a solicitação selecionada.

O relatório resultante se parece com isso no modo de edição:

![Imagem mostrando a criação uma experiência interativa usando cliques de linha de grade](./media/workbooks-interactive//grid-click-create.png)

A imagem abaixo mostra um relatório interativo mais elaborado no modo de leitura com base nos mesmos princípios. O relatório usa cliques de grade para exportar parâmetros - que, por sua vez, é usado em dois gráficos e um bloco de texto.

![Imagem mostrando a criação uma experiência interativa usando cliques de linha de grade](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportando o conteúdo de uma linha inteira
Às vezes, é desejável exportar todo o conteúdo da linha selecionada em vez de apenas uma coluna específica. Nesses casos, deixe `Field to export` a propriedade desfixada na etapa 7.1 acima. As abanuais exportarão todo o conteúdo da linha como um json para o parâmetro. 

No controle KQL de referência, use a `todynamic` função para analisar o json e acessar as colunas individuais.

 ## <a name="grid-cell-clicks"></a>Cliques de célula de grade
Os livros de trabalho permitem que os autores adicionem `link renderer`interatividade através de um tipo especial de renderizador de coluna de grade chamado . Um renderizador de link converte uma célula de grade em um hiperlink com base no conteúdo da célula. As obras de trabalho suportam muitos tipos de renderizadores de links - incluindo aqueles que permitem abrir lâminas de visão geral de recursos, visualizadores de sacos de propriedade, pesquisa do App Insights, uso, rastreamento de transações, etc.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Configurando a interatividade usando cliques de célula satisfazendo
1. Alterne a carteira de trabalho para editar o modo clicando no item _Editar_ barra de ferramentas.
2. Use o _link Adicionar consulta_ para adicionar um controle de consulta de log à carteira de trabalho. 
3. Selecione o tipo de consulta como _Log_, tipo de recurso (por exemplo, Crições de aplicativo) e os recursos a serem direcionados.
4. Use o editor de consulta para inserir o KQL para sua análise
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`para ver os resultados
6. Clique _em Configurações de coluna_ para abrir o painel de configurações.
7. Na seção _Colunas,_ definido:
    1. _Amostra_ - Renderizador `Link`de coluna: `Cell Details`, Exibir para abrir: , Etiqueta de link:`Sample`
    2. _Contagem_ - Renderizador de `Bar` `Blue`coluna: , Paleta de cores: , Valor mínimo:`0`
    3. _Solicitação_ - Renderizador de Coluna:`Automatic`
    4. Clique _em Salvar e Fechar_ para aplicar alterações
8. Clique em um `Sample` dos links na grade. Isso abre um painel de propriedade com os detalhes de uma solicitação amostrada.

    ![Imagem mostrando a criação uma experiência interativa usando cliques de célula de grade](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Ações de renderização de link
| Ação de link | Ação no clique |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores da linha em uma lâmina de contexto de grade de propriedade |
| `Cell Details` | Mostra o valor celular em uma lâmina de contexto de grade de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de solicitação como localização, instância de função, etc.). |
| `Cell Details` | Mostra o valor celular em uma lâmina de contexto de grade de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de solicitação como localização, instância de função, etc.). |
| `Custom Event Details` | Abre os detalhes de pesquisa do Application Insights com o ID de evento personalizado (itemId) na célula |
| `* Details` | Semelhante aos Detalhes de Eventos Personalizados, exceto para dependências, exceções, exibições de página, solicitações e rastreamentos. |
| `Custom Event User Flows` | Abre a experiência De fluxos de usuário do aplicativo Insights pivotado no nome do evento personalizado na célula |
| `* User Flows` | Semelhante aos Fluxos de Usuário de Eventos Personalizados, exceto para exceções, visualizações de página e solicitações |
| `User Timeline` | Abre a linha do tempo do usuário com o ID do usuário (user_Id) na célula |
| `Session Timeline` | Abre a experiência de pesquisa do Application Insights para o valor na célula (por exemplo, procure por texto 'abc' onde o abc é o valor na célula) |
| `Resource overview` | Abra a visão geral do recurso no portal com base no valor de ID de recurso na célula |

## <a name="conditional-visibility"></a>Visibilidade Condicional
A carteira de trabalho permite que os usuários façam com que certos controles apareçam ou desapareçam com base nos valores dos parâmetros. Isso permite que os autores tenham relatórios diferentes com base na entrada do usuário ou no estado de telemetria. Um exemplo é mostrar aos consumidores apenas um resumo quando as coisas estão boas, mas mostrar detalhes completos quando algo está errado.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Configuração da interatividade usando visibilidade condicional
1. Siga as etapas da `Setting up interactivity on grid row click` seção para configurar dois controles interativos.
2. Adicione um novo parâmetro na parte superior:
    1. Nome: `ShowDetails`
    2. Tipo de parâmetro:`Drop down`
    3. Necessário:`checked`
    4. Obter dados de:`JSON`
    5. Entrada JSON:`["Yes", "No"]`
    6. Economize para cometer alterações.
3. Definir o valor do parâmetro para`Yes`
4. No controle de consulta com o gráfico de área, clique no ícone _Configurações Avançadas_ (ícone de engrenagem)
5. Verifique a configuração`Make this item conditionally visible`
    1. Este item é `ShowDetails` visível `equals` se o valor do parâmetro`Yes`
6. Clique _em Editar feito_ para cometer alterações.
7. Clique em _Editar feito_ na barra de ferramentas da agenda para entrar no modo de leitura.
8. Mude o valor `ShowDetails` do `No`parâmetro para . Observe que o gráfico abaixo desaparece.

A imagem abaixo mostra `ShowDetails` o caso visível onde está`Yes`

![Imagem mostrando a visibilidade condicional onde o gráfico é visível](./media/workbooks-interactive/conditional-visibility.png)

A imagem abaixo mostra `ShowDetails` o caso oculto onde está`No`

![Imagem mostrando a visibilidade condicional onde o gráfico está escondido](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Próximas etapas


* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
