---
title: Blocos de anotações do Synapse Studio
description: Neste artigo, você aprenderá a criar e a desenvolver notebooks do Azure Synapse Studio (versão prévia) para fazer a preparação e a visualização dos dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: tracking-python
ms.openlocfilehash: a7dc0fcae9a6fea789d30bac10511007454ecc5f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503950"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Criar, desenvolver e manter blocos de anotações do Synapse Studio (visualização) no Azure Synapse Analytics

Um notebook Synapse Studio (visualização) é uma interface da Web para você criar arquivos que contenham código ao vivo, visualizações e texto de narração. Os notebooks são um bom lugar para validar ideias e fazer experimentos rápidos para obter insights de seus dados. Os notebooks também são amplamente usados na preparação e visualização de dados, no aprendizado de máquina e em outros cenários de Big Data.

Com um notebook do Azure Synapse Studio, você pode:

* Começar a trabalhar com um mínimo de configuração.
* Manter os dados protegidos com recursos internos de segurança corporativa.
* Analisar dados em formatos brutos (CSV, txt, JSON, etc.), formatos de arquivo processados (Parquet, Delta Lake, ORC, etc.) e arquivos de dados tabulares do SQL em Spark e SQL.
* Seja produtivo com recursos de criação aprimorados e visualização de dados interna.

Este artigo descreve como usar notebooks no Azure Synapse Studio.

## <a name="create-a-notebook"></a>Criar um notebook

Há dois modos de criar um notebook. Você pode criar um notebook ou importar um existente para um workspace do Azure Synapse no **Pesquisador de Objetos**. Os notebooks do Azure Synapse Studio podem reconhecer arquivos IPYNB padrão do Jupyter Notebook.

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Desenvolver notebooks

Os notebooks consistem em células, que são blocos individuais de código ou texto que podem ser executados de forma independente ou em grupo.

### <a name="add-a-cell"></a>Adicionar uma célula

Há várias maneiras de adicionar uma nova célula ao notebook.

1. Expanda o botão superior esquerdo **+ Célula** e selecione **Adicionar célula de código** ou **Adicionar célula de texto**.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Passe o mouse sobre o espaço entre duas células e selecione **Adicionar código** ou **Adicionar texto**.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Use [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Pressione **B** para inserir uma célula abaixo da célula atual.

### <a name="set-a-primary-language"></a>Definir uma linguagem principal

Os notebooks do Azure Synapse Studio dão suporte a quatro linguagens do Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET para Apache Spark (C#)

Você pode definir a linguagem principal para novas células adicionadas na lista suspensa na barra de comandos superior.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Usar vários idiomas

Você pode usar várias linguagens em um notebook especificando o comando magic da linguagem correto no início de uma célula. A tabela a seguir lista os comandos magic para alternar entre linguagens de célula.

|Comandos magic |Linguagem | Descrição |  
|---|------|-----|
|%%pyspark| Python | Execute uma consulta **Python** no contexto do Spark.  |
|%%spark| Scala | Execute uma consulta **Scala** no contexto do Spark.  |  
|%%sql| SparkSQL | Execute uma consulta **SparkSQL** no contexto do Spark.  |
|%%csharp | .NET para Spark C# | Execute uma consulta **.NET para Spark C#** no contexto do Spark. |

A imagem a seguir é um exemplo de como você pode escrever uma consulta PySpark com o comando magic **%%pyspark** ou uma consulta SparkSQL com o comando magic **%%sql** em um notebook **Spark (Scala)** . Observe que a linguagem principal do notebook está definida como pySpark.

   ![synapse-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Usar tabelas temporárias para fazer referência a dados entre linguagens

Você não pode fazer referência a dados ou variáveis diretamente em linguagens diferentes em um notebook do Synapse Studio. No Spark, uma tabela temporária pode ser referenciada entre os idiomas. Aqui está um exemplo de como ler um dataframe `Scala` no `PySpark` e `SparkSQL` usando uma tabela temporária do Spark como solução alternativa.

1. Na célula 1, leia um dataframe do conector do pool SQL com o Scala e crie uma tabela temporária.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Na célula 2, consulte os dados com o Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Na célula 3, use os dados no PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense de estilo IDE

Os notebooks do Azure Synapse Studio são integrados ao editor Monaco para levar o IntelliSense de estilo IDE ao editor de célula. O realce de sintaxe, o marcador de erro e as conclusões automáticas de código ajudam você a escrever código e identificar problemas mais rapidamente.

Os recursos do IntelliSense estão em níveis diferentes de maturidade para linguagens diferentes. Use a tabela abaixo para ver o que tem suporte.

|Languages| Realce da sintaxe | Marcador de erro de sintaxe  | Conclusão de código de sintaxe | Conclusão de código de variável| Conclusão de código de função do sistema| Conclusão do código de função do usuário| Recuo Inteligente | Dobramento de código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Spark (Scala)|Sim|Sim|Sim|Sim|-|-|-|Sim|
|SparkSQL|Sim|Sim|-|-|-|-|-|-|
|.NET para Spark (C#)|Sim|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatar célula de texto com botões da barra de ferramentas

Você pode usar os botões de formatação da barra de ferramentas de células de texto para realizar ações de markdown comuns. Eles incluem texto em negrito, texto em itálico, inserção de trechos de código, inserção de lista não ordenada, inserção de lista ordenada e inserção de imagem da URL.

  ![synapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Desfazer operações de célula
Clique no botão **Desfazer** ou pressione **CTRL+Z** para revogar a operação de célula mais recente. Agora você pode desfazer até as 20 últimas ações do histórico da célula. 

   ![synapse-undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Mover uma célula

Selecione as reticências (...) para acessar o menu de ações adicionais de célula na extrema direita. Em seguida, selecione **Mover célula para cima** ou **Mover célula para baixo** para mover a célula atual. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Ctrl+Alt+↑** a fim de mover a célula atual para cima. Pressione **Ctrl+Alt+↓** a fim de mover a célula atual para baixo.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Excluir uma célula

Para excluir uma célula, selecione as reticências (...), acesse o menu de ações de célula adicional na extrema direita e selecione **Excluir uma célula**. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **D,D** para excluir a célula atual.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Recolher uma entrada de célula
Clique no botão de seta na parte inferior da célula atual para recolhê-la. Para expandi-la, clique no botão de seta enquanto a célula estiver recolhida.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Recolher uma saída de célula

Clique no botão **Recolher saída** no canto superior esquerdo da saída da célula atual para recolhê-la. Para expandi-la, clique em **Mostrar saída da célula** enquanto a saída da célula está recolhida.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Executar notebooks

Você pode executar as células de código em seu notebook individualmente ou todas de uma vez. O status e o progresso de cada célula são representados no notebook.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código em uma célula.

1. Passe o mouse sobre a célula que você deseja executar e selecione o botão **Executar Célula** ou pressione **Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Para acessar o menu de ações adicionais de célula na extrema direita, selecione as reticências ( **...** ). Em seguida, selecione **Executar célula**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Use [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Shift+Enter** para executar a célula atual e selecionar a célula abaixo. Pressione **Alt+Enter** para executar a célula atual e inserir uma célula abaixo.


### <a name="run-all-cells"></a>Executar todas as células
Clique no botão **Executar Tudo** para executar todas as células no notebook atual em sequência.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

Para acessar o menu de ações adicionais de célula na extrema direita, selecione as reticências ( **...** ). Em seguida, selecione **Executar células acima** para executar todas as células acima da atual em sequência. Selecione **Executar células abaixo** para executar todas as células abaixo da atual em sequência.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Cancelar todas as células em execução
Clique no botão **cancelar tudo** para cancelar as células ou células em execução aguardando na fila. 
   ![cancelar-todas as células](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Indicador de status de célula

Um status de execução de célula passo a passo é exibido abaixo da célula para ajudá-lo a ver seu progresso atual. Depois que a execução da célula for concluída, um resumo de execução com a duração total e a hora de término serão mostrados e mantidos ali para referência futura.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso do Spark

O notebook do Azure Synapse Studio é baseado exclusivamente no Spark. As células de código são executadas remotamente no pool do Spark. Um indicador de progresso do trabalho do Spark é fornecido com uma barra de progresso em tempo real para ajudá-lo a entender o status de execução do trabalho.
O número de tarefas por cada trabalho ou estágio ajuda a identificar o nível paralelo do seu trabalho do Spark. Você também pode analisar mais detalhadamente a interface do usuário do Spark de um trabalho específico (ou estágio) clicando no link no nome do trabalho (ou estágio).


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuração de sessão do Spark

Você pode especificar a duração do tempo limite, o número e o tamanho dos executores para dar à sessão atual do Spark em **Configurar sessão**. Reinicie a sessão do Spark para que as alterações de configuração entrem em vigor. Todas as variáveis do notebook em cache serão limpas.

[![gerenciamento de sessão](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Um recomendador de sessão do Spark agora está disponível no painel de configuração de sessão do Spark. Você pode selecionar um pool do Spark diretamente no painel de configuração de sessão e ver quantos nós estão usando e quantos executores restantes estão disponíveis. Essas informações podem ajudá-lo a definir o tamanho da sessão adequadamente, em vez de modificá-la de volta e para trás.

![sessão-recomendado](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Trazer dados para um notebook

Você pode carregar dados do Armazenamento de Blobs do Azure, do Azure Data Lake Store Gen 2 e de pool do SQL, como mostram os exemplos de código abaixo.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Ler um CSV do Azure Data Lake Store Gen2 como um dataframe do Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Ler um CSV do Armazenamento de Blobs do Azure como um dataframe do Spark

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Ler dados da conta de armazenamento primária

Você pode acessar os dados na conta de armazenamento principal diretamente. Não é necessário fornecer as chaves secretas. No Data Explorer, clique com o botão direito do mouse em um arquivo e selecione **Novo notebook** para ver um novo notebook com o extrator de dados gerado automaticamente.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizar dados em um notebook

### <a name="produce-rendered-table-view"></a>Produzir exibição de tabela renderizada

Uma exibição de resultados tabulares é fornecida com a opção para criar gráfico de barras, de linhas, de pizza, de dispersão e de área. Você pode visualizar seus dados sem precisar escrever código. Os gráficos podem ser personalizados nas **Opções de Gráfico**. 

A saída dos comandos magic **%%sql** aparecem na exibição de tabela renderizada por padrão. Você pode chamar <code>display(df)</code> o Spark Dataframes ou a função RDD (redistribuída de conjuntos de tabelas resilientes) para produzir a exibição de tabela renderizada.

   [![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Visualizar gráficos internos do conjunto de grandes escala 

Por padrão, a <code>display(df)</code> função usará apenas as primeiras 1000 linhas dos dados para renderizar os gráficos. Verifique a **agregação em todos os resultados** e clique no botão **aplicar** , você aplicará a geração de gráfico do conjunto de um inteiro. Um trabalho do Spark será disparado quando a configuração do gráfico for alterada, demora um pouco para concluir o cálculo e renderizar o gráfico. 
    [![Builtin-gráficos-agregação-todos](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>Visualizar informações de estatísticas de dados
Você pode usar <code>display(df, summary = true)</code> para verificar o resumo de estatísticas de um determinado frame do Spark que inclui o nome da coluna, tipo de coluna, valores exclusivos e valores ausentes para cada coluna. Você também pode selecionar uma coluna específica para ver seu valor mínimo, valor máximo, valor médio e desvio padrão.
    [![BuiltIn-gráficos-resumo ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>Renderizar bibliotecas em HTML ou interativas

Você pode renderizar bibliotecas HTML ou interativas, como a **bokeh**, usando **displayHTML()** .

A imagem a seguir é um exemplo de como plotar glifos em um mapa usando **bokeh**.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Execute o código de exemplo a seguir para desenhar a imagem acima.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Salvar notebooks

Você pode salvar um único notebook ou todos eles em seu espaço de trabalho.

1. Para salvar as alterações feitas em um único notebook, selecione o botão **Publicar** na barra de comandos do notebook.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para salvar todos os notebooks em seu espaço de trabalho, selecione o botão **Publicar tudo** na barra de comandos do espaço de trabalho. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do notebook, você pode configurar se deseja incluir a saída da célula ao salvar.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos magic
Você pode usar seus comandos magic do Jupyter conhecidos nos notebooks do Azure Synapse Studio. Verifique a lista abaixo com os comandos magic disponíveis atuais. Conte-nos seus casos de uso no GitHub para que possamos continuar a criar mais comandos magic e atender às suas necessidades.

Mágicas de linha disponíveis: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magics de célula disponíveis: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>Orquestrar Notebook

### <a name="add-a-notebook-to-a-pipeline"></a>Adicionar um bloco de anotações a um pipeline

Clique no botão **Adicionar ao pipeline** no canto superior direito para adicionar um bloco de anotações a um pipeline existente ou criar um novo pipeline.

![Adicionar ao pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Designar uma célula de parâmetros

Para parametrizar seu notebook, selecione as reticências (...) para acessar o menu de ações de célula adicional na extrema direita. Em seguida, selecione **alternar célula de parâmetro** para designar a célula como a célula de parâmetros.

![alternar parâmetro](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory procura a célula Parameters e trata essa célula como padrão para os parâmetros passados no momento da execução. O mecanismo de execução adicionará uma nova célula abaixo da célula Parameters com parâmetros de entrada para substituir os valores padrão. Quando não há nenhuma célula de parâmetros designada, a célula injetada será inserida na parte superior do bloco de anotações.

### <a name="assign-parameters-values-from-a-pipeline"></a>Atribuir valores de parâmetros de um pipeline

Depois de criar um bloco de anotações com parâmetros, você pode executá-lo de um pipeline com a atividade do Azure Synapse notebook. Depois de adicionar a atividade à tela do pipeline, você poderá definir os valores dos parâmetros na seção **parâmetros de base** na guia **configurações** . 

![atribuir parâmetro](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Ao atribuir valores de parâmetro, você pode usar a [linguagem de expressão de pipeline](../../data-factory/control-flow-expression-language-functions.md) ou variáveis de [sistema](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Teclas de atalho

Semelhantemente aos Jupyter Notebooks, os notebooks do Azure Synapse Studio têm uma interface de usuário modal. O teclado faz coisas diferentes, dependendo de em qual modo a célula do notebook está. Os notebooks do Synapse Studio dão suporte aos seguintes dois modos de uma célula de código: modo de comando e modo de edição.

1. Uma célula está no modo de comando quando não há um cursor de texto solicitando que você digite. Quando uma célula está no modo de comando, você pode editar o notebook como um todo, mas não pode digitar em células individuais. Entre no modo de comando pressionando `ESC` ou usando o mouse para clicar fora da área do editor de uma célula.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. O modo de edição é indicado por um cursor de texto solicitando que você digite na área do editor. Quando uma célula está no modo de edição, você pode digitar na célula. Entre no modo de edição pressionando `Enter` ou usando o mouse para clicar na área do editor de uma célula.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho no modo de comando

Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade nos notebooks do Azure Synapse.

| Ação |Atalhos do notebook do Synapse Studio  |
|--|--|
|Executar a célula atual e selecionar abaixo | Shift+Enter |
|Executar a célula atual e inserir abaixo | Alt+Enter |
|Selecionar célula acima| Up |
|Selecionar célula abaixo| Down |
|Inserir célula acima| Um |
|Inserir célula abaixo| B |
|Estender células selecionadas acima| Shift+Seta para Cima |
|Estender células selecionadas abaixo| Shift+Seta para Baixo|
|Mover a célula para cima| CTRL+ALT+↑ |
|Mover a célula para baixo| CTRL+ALT+↓ |
|Excluir células selecionadas| D, D |
|Mudar para o modo de edição| Digite |

### <a name="shortcut-keys-under-edit-mode"></a>Teclas de atalho no modo de edição

Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade nos notebooks do Azure Synapse quando no modo de edição.

| Ação |Atalhos do notebook do Synapse Studio  |
|--|--|
|Mover cursor para cima | Para cima |
|Mover cursor para baixo|Para baixo|
|Desfazer|CTRL+Z|
|Refaz|Ctrl+Y|
|Inserir/remover comentário|Ctrl+/|
|Excluir palavra anterior|Ctrl+Backspace|
|Excluir palavra seguinte|Ctrl+Delete|
|Ir para o início da célula|Ctrl+Home|
|Ir para o final da célula |Ctrl+End|
|Ir uma palavra para a esquerda|CTRL+Seta para a esquerda|
|Ir uma palavra para a direita|CTRL+Seta para a direita|
|Selecionar tudo|CTRL+A|
|Recuar| Ctrl+]|
|Desfazer recuo|Ctrl+[|
|Alternar para o modo de comando| Esc |

## <a name="next-steps"></a>Próximas etapas
- [Confira os blocos de anotações de exemplo do Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Início Rápido: Criar um Pool do Apache Spark (versão prévia) no Azure Synapse Analytics usando ferramentas da Web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)
- [Use .NET para Apache Spark com Azure Synapse Analytics](spark-dotnet.md)
- [Documentação do .NET para Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
