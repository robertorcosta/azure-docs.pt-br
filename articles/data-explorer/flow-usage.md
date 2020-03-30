---
title: Exemplos de uso do conector de fluxo do Microsoft Azure Data Explorer (Preview)
description: Aprenda alguns exemplos comuns de uso do conector Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 759ea9800a0ed051fed887adfb10b06f7a53b72f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529196"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Exemplos de uso do conector de fluxo microsoft (Preview)

O conector de fluxo do Azure Data Explorer permite que o Azure Data Explorer use os [recursos flow do Microsoft Power Automate](https://flow.microsoft.com/) para executar consultas e comandos do Kusto automaticamente como parte de uma tarefa programada ou acionada. Este documento contém vários exemplos comuns de uso do conector Microsoft Flow.

Para obter mais informações, consulte [o conector Microsoft Flow (Preview)](flow.md).

* [Conector de fluxo da Microsoft e SQL](#microsoft-flow-connector-and-sql)
* [Empurrar dados para o conjunto de dados power BI](#push-data-to-power-bi-dataset)
* [Consultas condicionais](#conditional-queries)
* [Envie vários gráficos de fluxo do Azure Data Explorer](#email-multiple-azure-data-explorer-flow-charts)
* [Envie um e-mail diferente para diferentes contatos](#send-a-different-email-to-different-contacts)
* [Crie uma tabela HTML personalizada](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>Conector de fluxo da Microsoft e SQL

Use o conector Microsoft Flow para consultar seus dados e agregá-los em um banco de dados SQL.

> [!Note]
> A inserção SQL é feita separadamente para cada linha. Use apenas o conector Microsoft Flow para pequenas quantidades de dados de saída. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> No campo Nome do *cluster,* digite a URL do cluster.

## <a name="push-data-to-power-bi-dataset"></a>Empurrar dados para o conjunto de dados power BI

O conector Microsoft Flow pode ser usado juntamente com o conector Power BI para empurrar dados de consultas kusto para conjuntos de dados de streaming do Power BI.

1. Crie uma nova consulta executar e listar a ação de resultados.
1. Selecione **Nova etapa**.
1. Selecione **Adicionar uma ação** e procurar o Power BI.
1. Selecione **Power BI**.
1. Selecione **Adicionar linhas a um conjunto de dados**. 

    ![Conector BI de energia de fluxo](./media/flow-usage/flow-powerbiconnector.png)
1. Digite o **Workspace**, **Dataset**e **Tabela** para os quais os dados serão empurrados.
1. A partir da caixa de diálogo de conteúdo dinâmico, adicione uma carga útil contendo seu esquema de conjunto de dados e os resultados relevantes da consulta kusto.

    ![Campos BI de energia de fluxo](./media/flow-usage/flow-powerbifields.png)

O Fluxo aplica automaticamente a ação Power BI para cada linha da tabela de resultados da consulta Kusto. 

![Ação do BID de poder de fluxo para cada linha](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Consultas condicionais

Os resultados das consultas de Kusto podem ser usados como entrada ou condições para as próximas ações de fluxo.

No exemplo a seguir, consultamos Kusto por incidentes ocorridos durante o último dia. Para cada incidente resolvido, uma mensagem de folga é postada e uma notificação push é criada.
Para cada incidente que ainda está ativo, Kusto é consultado para obter mais informações sobre incidentes semelhantes. Ele envia essas informações como um e-mail e abre uma tarefa TFS relacionada.

Siga estas instruções para criar um fluxo semelhante:

1. Crie uma nova consulta executar e listar a ação de resultados.
1. Selecione **Nova etapa**.
1. Selecione **controle de condição .**
1. Na janela de conteúdo dinâmico, selecione o parâmetro que deseja usar como condição para as próximas ações.
1. Selecione o tipo de *Relacionamento* e *Valor* para definir uma condição específica no parâmetro dado.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    O fluxo aplica essa condição em cada linha da tabela de resultados da consulta.
1. Adicione ações para quando a condição for verdadeira e falsa.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Você pode usar os valores de resultado da consulta Kusto como entrada para as próximas ações. Selecione os valores de resultado na janela de conteúdo dinâmico.
No exemplo abaixo, uma ação Slack - Post Message e Visual Studio - Crie uma nova ação de item de trabalho contendo dados da consulta kusto foram adicionadas.

![Slack - Post Message](./media/flow-usage/flow-slack.png)

![Ação do Visual Studio](./media/flow-usage/flow-visualstudio.png)

Neste exemplo, se um incidente ainda estiver ativo, consulta kusto novamente para obter informações sobre como incidentes da mesma fonte foram resolvidos no passado.

![Consulta de condição de fluxo](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> No campo Nome do *cluster,* digite a URL do cluster.

Visualize essas informações como um gráfico de tortas e envie-as por e-mail para a equipe.

![E-mail de condição de fluxo](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Envie vários gráficos de fluxo do Azure Data Explorer

1. Crie um novo fluxo com o gatilho "Recorrência" e defina o intervalo do Fluxo e da frequência. 
1. Adicione um novo passo, com uma ou mais Kusto - Execute consulta e visualize as ações de resultados. 

    ![Execute várias consultas em um fluxo](./media/flow-usage/flow-severalqueries.png)
1. Para cada Kusto - Execute a consulta e visualize o resultado, defina os seguintes campos:
    * URL de cluster (no campo Nome do *cluster)*
    * Nome do Banco de Dados
    * Consulta e tipo de gráfico (tabela HTML/ gráfico de pie/ gráfico de tempo/ gráfico de barras/ inserir valor personalizado).

    ![Visualize resultados com vários anexos](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > Nos campos Nome de *cluster,* digite a URL do cluster.

1. Adicione uma ação Enviar e-mail. 
    * No campo *Corpo,* insira o corpo necessário para que o resultado visualizado da consulta seja incluído no corpo do e-mail.
    * Para adicionar um anexo ao e-mail, adicione nome de anexo e conteúdo de anexo.
    
    ![E-mail múltiplos anexos](./media/flow-usage/flow-emailmultipleattachments.png)

Resultados:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>Envie um e-mail diferente para diferentes contatos

Você pode aproveitar o Microsoft Flow para enviar diferentes e-mails personalizados para diferentes contatos. Os endereços de e-mail e o conteúdo do e-mail são resultado de uma consulta kusto.

Exemplo:

![E-mail dinâmico usando uma consulta kusto](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> No campo Nome do *cluster,* digite a URL do cluster.

![E-mail dinâmico na ação de fluxo](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>Crie uma tabela HTML personalizada

Você pode aproveitar o Microsoft Flow para criar e usar elementos HTML personalizados, como uma tabela HTML personalizada.

O exemplo a seguir demonstra como criar uma tabela HTML personalizada. A tabela HTML terá suas linhas coloridas pelo nível de log (o mesmo que no Azure Data Explorer).

Siga estas instruções para criar um fluxo semelhante:

1. Crie uma nova ação kusto - Execute consulta e liste resultados.

    ![Listar resultados para uma tabela HTML](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> No campo Nome do *cluster,* digite a URL do cluster.

1. Loop sobre os resultados da consulta e crie o corpo da tabela HTML: 
    1. Para criar uma variável para segurar a seqüência HTML, selecione **Novo passo**
    1. Selecione **Adicionar uma ação** e procurar variáveis. 
    1. Selecionar **Variáveis - Inicializar variável**. 
    1. Inicialize uma variável de string da seguinte forma:

    ![Inicializar uma variável](./media/flow-usage/flow-initializevariable.png)

1. Loop sobre os resultados:
    1. Selecione **Nova etapa**.
    1. Selecione **Adicionar uma ação**.
    1. Procure por Variáveis. 
    1. Selecionar **Variáveis - Anexar à variável string**. 
    1. Selecione o nome de variável que você inicializou antes e crie as linhas de tabela HTML usando os resultados da consulta. 
    Ao selecionar os resultados da consulta, Aplicar a cada um é adicionado automaticamente.

    No exemplo abaixo, `if` a expressão é usada para definir o estilo de cada linha:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. Crie o conteúdo HTML completo: 
    1. Adicione uma nova ação fora Aplicar a cada um. 
    No exemplo a seguir, a ação usada é Enviar um e-mail.
    1. Defina sua tabela HTML usando a variável das etapas anteriores. 
    1. Se você estiver enviando um e-mail, selecione **Mostrar opções avançadas** e, em É HTML, selecione **Sim**.

    ![E-mail de tabela HTML personalizado](./media/flow-usage/flow-customhtmltablemail.png)

Resultado:

![Resultado de e-mail de tabela HTML personalizado](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>Próximas etapas

Conheça o [conector do aplicativo Microsoft Azure Explorer Logic](https://docs.microsoft.com/azure/kusto/tools/logicapps) App, que é outra maneira de executar consultas e comandos kusto automaticamente como parte de uma tarefa programada ou acionada.
