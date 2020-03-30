---
title: Criar relatórios interativos do Azure Monitor para VMs com pastas de trabalho
description: Simplifique relatórios complexos com livros de trabalho predefinidos e personalizados parametrizados para OZure Monitor para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480446"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Criar relatórios interativos do Azure Monitor para VMs com pastas de trabalho

As pastas de trabalho combinam texto, [consultas de log,](../log-query/query-language.md)métricas e parâmetros em relatórios interativos ricos. As Pastas de Trabalho são editáveis por qualquer membro da equipe com acesso aos mesmos recursos do Azure.

As regras de trabalho são úteis para cenários como:

* Explorando o uso de sua máquina virtual quando você não sabe as métricas de interesse antecipadamente: utilização da CPU, espaço em disco, memória, dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, os livros de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para esse tipo de exploração de forma livre.
* Explicando à sua equipe como uma VM recentemente provisionada está se saindo, mostrando métricas para contadores-chave e outros eventos de log.
* Compartilhando os resultados de um experimento de redimensionamento de sua VM com outros membros da sua equipe. Você pode explicar os objetivos do experimento com texto e, em seguida, mostrar cada métrica de uso e consultas de análise usadas para avaliar o experimento, juntamente com chamadas claras para saber se cada métrica estava acima ou abaixo do alvo.
* Reportando o impacto de uma paralisação no uso de sua VM, combinando dados, explicação de texto e uma discussão sobre os próximos passos para evitar paralisações no futuro.

A tabela a seguir resume as anotações que o Azure Monitor para VMs inclui para começar.

| Pasta de trabalho | Descrição | Escopo |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável da nossa exibição Top N List and Charts em uma única carteira de trabalho que aproveita todos os contadores de desempenho do Log Analytics que você habilitou.| Em escala |
| Contadores de desempenho | Uma exibição de gráfico Top N em um amplo conjunto de contadores de desempenho. | Em escala |
| conexões | As conexões fornecem uma visão aprofundada das conexões de entrada e saída de suas VMs monitoradas. | Em escala |
| Portas ativas | Fornece uma lista dos processos vinculados às portas nas VMs monitoradas e suas atividades no prazo escolhido. | Em escala |
| Abrir portas | Fornece o número de portas abertas em suas VMs monitoradas e os detalhes sobre essas portas abertas. | Em escala |
| Conexões com falha | Exiba a contagem de conexões com falha em suas VMs monitoradas, a tendência de falha e se a porcentagem de falhas estiver aumentando ao longo do tempo. | Em escala |
| Segurança e Auditoria | Uma análise do tráfego TCP/IP que relata conexões globais, conexões maliciosas, onde os pontos finais de IP residem globalmente.  Para habilitar todos os recursos, você precisará ativar a detecção de segurança. | Em escala |
| Tráfego de TCP | Um relatório ranqueado para suas VMs monitoradas e seu tráfego de rede enviado, recebido e total em uma grade e exibido como uma linha de tendência. | Em escala |
| Comparação de tráfego | Esta cartm permite comparar as tendências de tráfego de rede para uma única máquina ou um grupo de máquinas. | Em escala |
| Desempenho | Fornece uma versão personalizável da nossa exibição performance que aproveita todos os contadores de desempenho do Log Analytics que você habilitou. | VM única | 
| conexões | As conexões fornecem uma visão aprofundada das conexões de entrada e saída da VM. | VM única |
 
## <a name="creating-a-new-workbook"></a>Criando uma nova carteira de trabalho

Uma pasta de trabalho composta por seções consiste em gráficos editáveis de forma independente, tabelas, texto e controles de entrada. Para entender melhor as carteiras de trabalho, vamos começar abrindo um modelo e caminhar através da criação de uma carteira de trabalho personalizada. 

1. Faça login no [portal Azure](https://portal.azure.com).

2. Selecione **Máquinas Virtuais do Microsoft Azure**.

3. Na lista, selecione uma VM.

4. Na página VM, na seção **Monitoramento,** selecione **Insights**.

5. Na página de insights da VM, selecione A guia **Desempenho** ou **Mapas** e selecione Exibir livros de **trabalho** no link da página. Na lista de paradas, selecione **Ir para Galeria**.

    ![Captura de tela da lista de parada da carteira de trabalho](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Isso lança a galeria da cartilha com uma série de livros de trabalho pré-construídos para ajudá-lo a começar.

7. Crie uma nova carteira de trabalho selecionando **Novo**.

    ![Captura de tela da galeria da pasta de trabalho](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Editando seções da pasta de trabalho

Pastas de Trabalho têm dois modos: **modo de edição**, e **modo de leitura**. Quando uma nova carteira de trabalho é lançada pela primeira vez, ela é aberta no **modo de edição**. Ele mostra todo o conteúdo da carteira de trabalho, incluindo quaisquer etapas e parâmetros que estejam escondidos de outra forma. **Modo de leitura** apresenta uma exibição de estilo do relatório simplificado. O modo de leitura permite que você abstraia a complexidade que entrou na criação de um relatório enquanto ainda tem a mecânica subjacente a apenas alguns cliques de distância quando necessário para a modificação.

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando terminar de editar uma seção, clique em **Editar feito** no canto inferior esquerdo da seção.

2. Para criar uma duplicata de uma seção, clique no ícone **Clonar esta seção**. Criar seções duplicadas é uma ótima forma de iterar em uma consulta sem perder as iterações anteriores.

3. Para mover uma seção para cima em uma pasta de trabalho, clique no ícone **Mover para cima** ou **Mover para baixo**.

4. Para remover uma seção permanentemente, clique no ícone **Remover**.

## <a name="adding-text-and-markdown-sections"></a>Adicionando texto e seções de Markdown

Adicionar cabeçalhos, explicações e comentários às pastas de trabalho ajuda a transformar um conjunto de tabelas e gráficos em uma narrativa. As seções de texto nas pastas de trabalho dão suporte à [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) na formatação de texto, como cabeçalhos, negrito, itálico e listas com marcadores.

Para adicionar uma seção de texto à pasta de trabalho, use o botão **Adicionar texto** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionando seções de consulta

![Seção de consulta no Workbooks](media/vminsights-workbooks/005-workbook-query-section.png)

Para adicionar uma seção de consulta à pasta de trabalho, use o botão **Adicionar consulta** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

Seções de consulta são altamente flexíveis e podem ser usadas para responder perguntas como:

* Como foi a utilização da MINHA CPU durante o mesmo período de tempo como um aumento no tráfego de rede?
* Qual foi a tendência no espaço disponível em disco no último mês?
* Quantas falhas de conexão de rede minha vm experimentou nas últimas duas semanas? 

Você também não está limitado apenas a consultar a partir do contexto da máquina virtual da quem você lançou a carteira de trabalho. Você pode consultar várias máquinas virtuais, bem como espaços de trabalho do Log Analytics, desde que você tenha permissão de acesso a esses recursos.

Para incluir dados de outros espaços de trabalho do Log Analytics ou de um aplicativo específico do Application Insights usando o identificador do **espaço de trabalho.** Para saber mais sobre consultas de recursos cruzados, consulte a [orientação oficial](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Configurações avançadas de consulta analítica

Cada seção tem suas próprias configurações avançadas, que são acessíveis através das configurações ![Ícone de controles de edição da seção Workbooks localizado à direita do botão Adicionar](media/vminsights-workbooks/006-settings.png) **parâmetros.**

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Faz de um item um tamanho arbitrário, para que você possa encaixar muitos itens em uma única linha, permitindo que você organize melhor seus gráficos e tabelas em relatórios interativos ricos.  |
| **Condicionalmente visível** | Especifique para ocultar passos com base em um parâmetro quando estiver no modo de leitura. |
| **Exportar um parâmetro**| Permitir que uma linha selecionada na grade ou gráfico faça com que etapas posteriores alterem valores ou se tornem visíveis.  |
| **Mostrar consulta quando não estiver em edição** | Exibe a consulta acima do gráfico ou da tabela mesmo quando no modo de leitura.
| **Mostrar abrir no botão análise ao não editar** | Adiciona o ícone blue Analytics ao canto direito do gráfico para permitir acesso com um clique.|

A maioria dessas configurações é bastante intuitiva, mas para entender **um parâmetro de exportação** é melhor examinar uma pasta de trabalho que usa essa funcionalidade.

Uma das cadernetas pré-construídas - **TCP Traffic**, fornece informações sobre métricas de conexão de uma VM.

A primeira seção da carteira de trabalho é baseada em dados de consulta de log. A segunda seção também é baseada em dados de consulta de log, mas selecionar uma linha na primeira tabela atualizará interativamente o conteúdo dos gráficos:

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível através do uso do **Quando um item é selecionado, exporte um parâmetro** de configurações avançadas, que estão habilitadas na consulta de log da tabela.

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de log utiliza os valores exportados quando uma linha é selecionada para criar um conjunto de valores que são então usados pelo título e gráficos da seção. Se nenhuma linha for selecionada, ela oculta o título da seção e os gráficos. 

Por exemplo, o parâmetro oculto na segunda seção usa a seguinte referência da linha selecionada na grade:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionando seções de métricas

As seções métricas dão acesso completo ao incorporar dados de métricas do Azure Monitor em seus relatórios interativos. No Monitor Azure para VMs, as permotos pré-construídas normalmente contêm dados de consulta analítica em vez de dados métricos.  Você pode optar por criar livros de trabalho com dados métricos, permitindo que você aproveite ao máximo o melhor de ambos os recursos em um só lugar. Você também tem a capacidade de extrair dados de métrica de recursos em qualquer uma das assinaturas que você tem acesso.

Aqui está um exemplo de dados de máquinas virtuais sendo puxados para uma carteira de trabalho para fornecer uma visualização em grade do desempenho da CPU:

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionando seções de parâmetro

Os parâmetros de pasta de trabalho permitem que você altere os valores na pasta de trabalho sem a necessidade de editar manualmente as seções de consulta ou texto. Isso elimina a necessidade de precisar compreender a linguagem de consulta do Analytics subjacente e expandindo imensamente o público-alvo potencial de relatórios baseados na pasta de trabalho.

Os valores dos parâmetros são substituídos na consulta, texto ou outras seções de parâmetro, colocando o nome do parâmetro entre chaves, como ``{parameterName}``. Os nomes dos parâmetros são limitados a regras semelhantes como identificadores JavaScript, caracteres alfabéticos ou sublinhados, seguidos por caracteres alfanuméricos ou sublinhados. Por exemplo, **a1** é permitido, mas **1a** não é permitido.

Os parâmetros são lineares, começando na parte superior de uma pasta de trabalho e que fluem para baixo para etapas posteriores.  Parâmetros declarados posteriormente em uma carteira de trabalho podem substituir parâmetros que foram declarados anteriormente. Isso também permite parâmetros que usam consultas para acessar os valores a partir de parâmetros definidos anteriormente. Dentro de etapa de um parâmetro em si, parâmetros também são lineares, da esquerda para a direita, onde parâmetros à direita podem depender de um parâmetro declarado anteriormente na mesma etapa.
 
Existem quatro tipos diferentes de parâmetros, que atualmente são suportados:

|                  |      |
| ---------------- |:-----|
| **Texto**    | Permite que o usuário edite uma caixa de texto, e você pode opcionalmente fornecer uma consulta para preencher o valor padrão. |
| **Queda** | Permite que o usuário escolha entre um conjunto de valores. |
| **Seletor de intervalo de tempo**| Permite que o usuário escolha entre um conjunto predefinido de valores de intervalo de tempo ou escolha a partir de um intervalo de tempo personalizado.|
| **Catador de recursos** | Permite que o usuário escolha entre os recursos selecionados para a carteira de trabalho.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um usuário digita na caixa de texto é substituído diretamente na consulta, sem escapar ou citar. Se o valor que você precisa é uma cadeia de caracteres, a consulta deve ter aspas ao redor do parâmetro (como **'{parameter}'**).

O parâmetro de texto permite que o valor em uma caixa de texto seja usado em qualquer lugar. Pode ser um nome de tabela, nome da coluna, nome da função, operador, etc.  O tipo de parâmetro de texto tem uma configuração **Obtenha o valor padrão da consulta analytics**, que permite ao autor da carteira de trabalho usar uma consulta para preencher o valor padrão para essa caixa de texto.

Ao usar o valor padrão de uma consulta de log, apenas o primeiro valor da primeira linha (linha 0, coluna 0) é usado como o valor padrão. Portanto, é recomendável limitar sua consulta para retornar apenas uma linha e uma coluna. Quaisquer outros dados retornados pela consulta serão ignorados. 

Qualquer valor que a consulta retorna será substituído diretamente por nenhum escape ou aspas. Se a consulta não retornar nenhuma linha, o resultado do parâmetro é uma cadeia de caracteres vazia (se o parâmetro não for necessário) ou indefinido (se o parâmetro for necessário).

### <a name="using-a-drop-down"></a>Usando um drop-down

O tipo de parâmetro de saque permite criar um controle drop-down, permitindo a seleção de um ou muitos valores.

A lista de paradas é preenchida por uma consulta de log ou JSON. Se a consulta retornar uma coluna, os valores nessa coluna serão o valor e o rótulo no controle de saque. Se a consulta retornar duas colunas, a primeira coluna será o valor e a segunda coluna será a etiqueta mostrada no drop-down. Se a consulta retornar três colunas, a terceira coluna será usada para indicar a seleção padrão nessa lista de paradas. Esta coluna pode ser qualquer tipo, mas o mais simples é usar o bool ou tipos numéricos, onde 0 é falso e 1 é verdadeiro.

Se a coluna for um tipo de cadeia de caracteres, cadeia de caracteres nula ou está vazia, é considerada falsa, e qualquer outro valor será considerado verdadeiro. Para as gotas de seleção única, o primeiro valor com um valor real é usado como seleção padrão.  Para várias quedas de seleção, todos os valores com um valor real são usados como o conjunto selecionado padrão. Os itens na parte suspensa são mostrados em qualquer ordem que a consulta retornou linhas. 

Vejando os parâmetros presentes no relatório Visão Geral de Conexões. Clique no símbolo de edição ao lado **de Direção**.

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isso iniciará o item **editar parâmetro.**

![Monitor do Azure para controles de edição da seção de livros de trabalho de VMs](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite que você gere uma tabela arbitrária preenchida com conteúdo. Por exemplo, o JSON a seguir gera dois valores no drop-down:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável é usar um drop-down para escolher a partir de um conjunto de contadores de desempenho pelo nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta exibirá os resultados da seguinte maneira:

![Evasão de balcão Perf](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

As drop-downs são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora você possa fazer seu próprio parâmetro de intervalo de tempo personalizado por meio do tipo de parâmetro de lista suspensa, você também pode usar o tipo de parâmetro de intervalo de tempo de uso, se você não precisa do mesmo grau de flexibilidade. 

Tipos de parâmetro de intervalo de tempo têm intervalos padrão de 15 que vão de cinco minutos para os últimos 90 dias. Também é uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador de relatório escolha iniciar explicitamente e parar os valores para o intervalo de tempo.

### <a name="resource-picker"></a>Seletor de recursos

O tipo de parâmetro de seletor de recurso fornece a capacidade de definir o escopo de seu relatório para determinados tipos de recursos. Um exemplo de uma carteira de trabalho pré-construída que aproveita o tipo de seletor de recursos é a carteira de trabalho **Performance.**

![Dropdown de espaços de trabalho](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

As abanuais são salvas dentro de um Espaço de Trabalho do Log Analytics ou de um recurso de máquina virtual, dependendo de como você acessa a galeria de livros de trabalho. A carteira de trabalho pode ser salva na seção **Meus relatórios** que é privada para você ou na seção **Relatórios Compartilhados** que é acessível a todos com acesso ao recurso. Para exibir todas as pastas de trabalho no recurso, clique no botão **Abrir** na barra de ações.

Para compartilhar uma pasta de trabalho que atualmente está em **Meus Relatórios**:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja compartilhar
3. Clique em **Mover para Relatórios Compartilhados**.

Para compartilhar uma pasta de trabalho com um link ou por email, clique em **Compartilhar** na barra de ações. Tenha em mente que os destinatários do link precisam ter acesso a esse recurso no portal do Azure para exibir a pasta de trabalho. Para fazer edições, os destinatários precisam de, pelo menos, permissões de Colaborador ao recurso.

Para fixar um link em uma pasta de trabalho em um Painel do Azure:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja fixar
3. Clique em **Fixar no painel**.

## <a name="next-steps"></a>Próximas etapas

- Para identificar limitações e desempenho geral da VM, consulte [Exibir desempenho de VM do Azure](vminsights-performance.md).

- Para saber mais sobre as dependências descobertas dos aplicativos, consulte [Exibir o Monitor do Azure para VMs Map](vminsights-maps.md).
