---
title: Criar, desenvolver e manter blocos de anotações do Azure Synapse Studio (visualização)
description: Neste artigo, você aprenderá a criar e desenvolver blocos de anotações do Azure Synapse Studio (visualização) para fazer a preparação e a visualização dos dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430221"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Criar, desenvolver e manter blocos de anotações do Azure Synapse Studio (visualização)

Um notebook do Azure Synapse Studio (versão prévia) é uma interface da Web para que você crie arquivos que contenham código ao vivo, visualizações e texto de narração. Os notebooks são um bom lugar para validar ideias e usar experimentos rápidos para obter informações de seus dados. Os notebooks também são amplamente usados na preparação de dados, na visualização de dados, no aprendizado de máquina e em outros cenários de Big Data.

Com um notebook do Azure Synapse Studio, você pode:

* Introdução ao esforço de configuração zero.
* Mantenha os dados protegidos com recursos internos de segurança corporativa.
* Analise dados em formatos brutos (CSV, txt, JSON, etc.), formatos de arquivo processados (parquet, Delta Lake, ORC, etc.) e arquivos de dados tabulares do SQL em Spark e SQL.
* Seja produtivo com recursos de criação aprimorados e visualização de dados interna.

Este artigo descreve como usar blocos de anotações no Azure Synapse Studio.

## <a name="create-a-notebook"></a>Criar um notebook

Há duas maneiras de criar um bloco de anotações. Você pode criar um novo bloco de anotações ou importar um bloco de anotações existente para um espaço de trabalho do Azure Synapse do pesquisador de **objetos**. Os blocos de anotações do Azure Synapse Studio podem reconhecer arquivos IPYNB padrão de Jupyter Notebook.

![Synapse-criar-importar-Notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Desenvolver notebooks

Os blocos de anotações consistem em células, que são blocos individuais de código ou texto que podem ser executados de forma independente ou como um grupo.

### <a name="add-a-cell"></a>Adicionar uma célula

Há várias maneiras de adicionar uma nova célula ao seu bloco de anotações.

1. Expanda o botão superior esquerdo **+ célula** e selecione **Adicionar célula de código** ou **Adicionar célula de texto**.

    ![Adicionar célula com botão de célula](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Focalize o espaço entre duas células e selecione **Adicionar código** ou **Adicionar texto**.

    ![Adicionar célula-entre-espaço](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Use [as teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **um** para inserir uma célula acima da célula atual. Pressione **B** para inserir uma célula abaixo da célula atual.

### <a name="set-a-primary-language"></a>Definir um idioma principal

Os notebooks do Azure Synapse Studio dão suporte a quatro idiomas do Spark:

* pyspark (Python)
* Spark (escala)
* sparkSQL
* Spark.NET (C#)

Você pode definir o idioma principal para novas células adicionadas na lista suspensa na barra de comandos superior.

   ![padrão-Synapse-idioma](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Usar vários idiomas

Você pode usar vários idiomas em um notebook especificando o comando mágico de idioma correto no início de uma célula. A tabela a seguir lista os comandos mágicos para alternar idiomas de célula.

|Comando mágico |Linguagem | Descrição |  
|---|------|-----|
|%% pyspark| Python | Execute uma consulta **Python** no contexto do Spark.  |
|%% Spark| Scala | Execute uma consulta **escalar** no contexto do Spark.  |  
|%% SQL| SparkSQL | Execute uma consulta **SparkSQL** no contexto do Spark.  |
|%% Csharp | Spark.NET C # | Execute uma consulta **C# Spark.net** no contexto do Spark. |

A imagem a seguir é um exemplo de como você pode escrever uma consulta PySpark usando o comando **%% PySpark** mágica ou uma consulta SparkSQL com o comando **%% SQL** mágico em um notebook **Spark (escala)** . Observe que o idioma principal do bloco de anotações é definido como escala.

   ![Synapse-Spark-Magic](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Usar tabelas temporárias para fazer referência a dados entre linguagens

Você não pode fazer referência a dados ou variáveis diretamente em idiomas diferentes em um notebook do Synapse Studio. No Spark, uma tabela temporária pode ser referenciada entre os idiomas. Aqui está um exemplo de como ler um `Scala` dataframe no e `PySpark` `SparkSQL` usar uma tabela temporária do Spark como uma solução alternativa.

1. Na célula 1, leia um dataframe do conector do pool SQL usando escalabilidade e crie uma tabela temporária.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Na célula 2, consulte os dados usando o Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Na célula 3, use os dados em PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense estilo IDE

Os blocos de anotações do Azure Synapse Studio são integrados ao editor Mônaco para colocar o IntelliSense estilo IDE no editor de célula. O realce de sintaxe, o criador de erros e as conclusões de código automáticas ajudam você a escrever código e identificar problemas mais rapidamente.

Os recursos do IntelliSense estão em níveis diferentes de maturidade para diferentes idiomas. Use a tabela abaixo para ver o que tem suporte.

|Languages| Realce da sintaxe | Marcador de erro de sintaxe  | Conclusão do código de sintaxe | Conclusão de código de variável| Conclusão de código de função do sistema| Conclusão do código da função de usuário| Recuo Inteligente | Dobramento de código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Spark (escala)|Sim|Sim|Sim|Sim|-|-|-|Sim|
|SparkSQL|Sim|Sim|-|-|-|-|-|-|
|Spark.NET (C#)|Sim|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatar célula de texto com botões da barra de ferramentas

Você pode usar os botões de formatação na barra de ferramentas de células de texto para realizar ações de redução comuns. Ele inclui texto em negrito, itálico em texto, inserção de trechos de código, inserção de lista não ordenada, inserção de lista ordenada e inserção de imagem da URL.

  ![Synapse-texto-célula-barra de ferramentas](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Desfazer operações de célula
Clique no botão **desfazer** ou pressione **Ctrl + Z** para revogar a operação de célula mais recente. Agora você pode desfazer até as últimas 20 ações de célula históricas. 

   ![Synapse-desfazer células](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Mover uma célula

Selecione as reticências (...) para acessar o menu ações adicionais de célula na extrema direita. Em seguida, selecione **mover célula para cima** ou **mover célula para baixo** para mover a célula atual. 

Você também pode usar [as teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Ctrl + Alt + ↑** para mover para cima a célula atual. Pressione **Ctrl + Alt + ↓** para mover a célula atual para baixo.

   ![mover uma célula](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Excluir uma célula

Para excluir uma célula, selecione as reticências (...) para acessar o menu de ações de célula adicional na extrema direita e selecione **excluir célula**. 

Você também pode usar [as teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **d, d** para excluir a célula atual.
  
   ![excluir-a-célula](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Recolher uma entrada de célula
Clique no botão de seta na parte inferior da célula atual para recolhê-la. Para expandi-lo, clique no botão de seta enquanto a célula está recolhida.

   ![recolher-célula-entrada](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Recolher uma saída de célula

Clique no botão **recolher saída** no canto superior esquerdo da saída da célula atual para recolhê-la. Para expandi-lo, clique na **saída mostrar célula** enquanto a saída da célula estiver recolhida.

   ![recolher-célula-saída](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Executar notebooks

Você pode executar as células de código em seu bloco de anotações individualmente ou todas de uma vez. O status e o progresso de cada célula são representados no bloco de anotações.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código em uma célula.

1. Focalize a célula que você deseja executar e selecione o botão **executar célula** ou pressione **Ctrl + Enter**.

   ![Run-Cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Para acessar o menu ações adicionais de célula na extrema direita, selecione as reticências (**...**). Em seguida, selecione **executar célula**.

   ![executar-célula-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Use [as teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Shift + Enter** para executar a célula atual e selecione a célula abaixo. Pressione **ALT + Enter** para executar a célula atual e insira uma nova célula abaixo.


### <a name="run-all-cells"></a>Executar todas as células
Clique no botão **executar tudo** para executar todas as células no bloco de anotações atual em sequência.

   ![executar-todas as células](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

Para acessar o menu ações adicionais de célula na extrema direita, selecione as reticências (**...**). Em seguida, selecione **executar células acima** para executar todas as células acima da atual em sequência. Selecione **executar células abaixo** para executar todas as células abaixo da atual em sequência.

   ![Run-Cells-Above-ou-abaixo](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indicador de status de célula

Um status de execução de célula passo a passo é exibido abaixo da célula para ajudá-lo a ver seu progresso atual. Depois que a execução da célula for concluída, um resumo de execução com a duração total e a hora de término serão mostrados e mantidos ali para referência futura.

![status da célula](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso do Spark

O notebook do Azure Synapse Studio é baseado exclusivamente em Spark. As células de código são executadas remotamente no pool do Spark. Um indicador de progresso do trabalho do Spark é fornecido com uma barra de progresso em tempo real aparece para ajudá-lo a entender o status de execução do trabalho.


![Spark-Progress-indicador](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuração de sessão do Spark

Você pode especificar a duração do tempo limite, o número e o tamanho dos executores para dar à sessão atual do Spark na **sessão de configuração**. Reinicie a sessão do Spark para que as alterações de configuração entrem em vigor. Todas as variáveis de bloco de anotações em cache são limpas.

![gerenciamento de sessão](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Trazer dados para um bloco de anotações

Você pode carregar dados do armazenamento de BLOBs do Azure, Azure Data Lake Store Gen 2 e pool do SQL, conforme mostrado nos exemplos de código abaixo.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Ler um CSV de Azure Data Lake Store Gen2 como um dataframe do Spark

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Ler um CSV do armazenamento de BLOBs do Azure como um dataframe do Spark

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

Você pode acessar os dados na conta de armazenamento principal diretamente. Não é necessário fornecer as chaves secretas. Em Data Explorer, clique com o botão direito do mouse em um arquivo e selecione **novo bloco** de anotações para ver um novo bloco de anotações com o extrator de dados gerado automaticamente.

![dados para célula](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizar dados em um bloco de anotações

### <a name="display"></a>Exibir ()

Uma exibição de resultados tabulares é fornecida com a opção para criar um gráfico de barras, gráfico de linhas, gráfico de pizza, gráfico de dispersão e gráfico de área. Você pode visualizar seus dados sem precisar escrever código. Os gráficos podem ser personalizados nas **Opções de gráfico**. 

A saída dos comandos da mágica **%% SQL** é exibida por padrão na exibição de tabela renderizada. Você pode chamar **Display (`<DataFrame name>`)** no Spark dataframes ou na função RDD (resiliented Distributed DataSets) para produzir a exibição de tabela renderizada.

   ![gráficos internos](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Você pode renderizar bibliotecas HTML ou interativas, como **bokeh**, usando o **displayHTML ()**.

A imagem a seguir é um exemplo de plotar glifos em um mapa usando **bokeh**.

   ![bokeh-exemplo](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Execute o seguinte código de exemplo para desenhar a imagem acima.

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

## <a name="save-notebooks"></a>Salvar blocos de anotações

Você pode salvar um único bloco de anotações ou todos os blocos de anotações em seu espaço de trabalho.

1. Para salvar as alterações feitas em um único bloco de anotações, selecione o botão **publicar** na barra de comandos do bloco de anotações.

   ![publicar-Notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para salvar todos os blocos de anotações em seu espaço de trabalho, selecione o botão **publicar tudo** na barra de comandos do espaço de trabalho. 

   ![publicar tudo](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do bloco de anotações, você pode configurar se deseja incluir a saída da célula ao salvar.

   ![bloco de notas-Propriedades](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos mágicos
Você pode usar seus comandos mágicos Jupyter familiares nos notebooks do Azure Synapse Studio. Verifique a lista abaixo como os comandos mágicos disponíveis atuais. Conte-nos seus casos de uso no GitHub para que possamos continuar a criar mais comandos mágicos para atender às suas necessidades.

Mágicas de linha disponíveis: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Mágicas de célula disponíveis: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% Csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Teclas de atalho

Semelhante aos notebooks Jupyter, os notebooks do Azure Synapse Studio têm uma interface de usuário modal. O teclado faz coisas diferentes, dependendo de em qual modo a célula do notebook está. Os notebooks do Synapse Studio dão suporte aos dois modos a seguir para uma determinada célula de código: modo de comando e modo de edição.

1. Uma célula está no modo de comando quando não há um cursor de texto solicitando que você digite. Quando uma célula está no modo de comando, você pode editar o bloco de anotações como um todo, mas não digitar em células individuais. Insira o modo de comando `ESC` pressionando ou usando o mouse para clicar fora da área do editor de uma célula.

   ![modo de comando](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. O modo de edição é indicado por um cursor de texto solicitando que você digite na área do editor. Quando uma célula está no modo de edição, você não consegue digitar na célula. Insira o modo de edição `Enter` pressionando ou usando o mouse para clicar na área do editor de uma célula.
   
   ![modo-de-edição](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho no modo de comando

Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade nos blocos de anotações do Azure Synapse.

| Ação |Atalhos do bloco de anotações do Synapse Studio  |
|--|--|
|Execute a célula atual e selecione abaixo | Shift+Enter |
|Execute a célula atual e insira abaixo | Alt+Enter |
|Selecionar célula acima| Para cima |
|Selecione a célula abaixo| Para baixo |
|Inserir célula acima| Um |
|Inserir célula abaixo| B |
|Estender células selecionadas acima| Shift + para cima |
|Estender as células selecionadas abaixo| Shift + seta para baixo|
|Mover célula para cima| CTRL + ALT + ↑ |
|Mover célula para baixo| CTRL + ALT + ↓ |
|Excluir células selecionadas| D, D |
|Alternar para o modo de edição| Digite |

### <a name="shortcut-keys-under-edit-mode"></a>Teclas de atalho no modo de edição

Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade nos blocos de anotações do Azure Synapse quando estiver no modo de edição.

| Ação |Atalhos do bloco de anotações do Synapse Studio  |
|--|--|
|Mover cursor para cima | Para cima |
|Mover cursor para baixo|Para baixo|
|Desfazer|Ctrl + Z|
|Refaz|Ctrl + Y|
|Inserir/remover comentário|CTRL +/|
|Excluir palavra antes|Ctrl + Backspace|
|Excluir palavra após|Ctrl + Delete|
|Ir para início da célula|Ctrl + Home|
|Ir para final da célula |Ctrl + End|
|Ir uma palavra para a esquerda|CTRL + esquerda|
|Ir uma palavra para a direita|CTRL + direita|
|Selecionar tudo|Ctrl + A|
|Dividir| CTRL +]|
|Desrecuar|CTRL + [|
|Alternar para o modo de comando| Esc |

## <a name="next-steps"></a>Próximas etapas

- [Documentação do .NET para Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
