---
title: Crie, desenvolva e mantenha notebooks Azure Synapse Studio (preview)
description: Neste artigo, você aprende a criar e desenvolver notebooks Azure Synapse Studio (preview) para fazer a preparação e visualização de dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430221"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Crie, desenvolva e mantenha notebooks Azure Synapse Studio (preview)

Um notebook Azure Synapse Studio (preview) é uma interface web para você criar arquivos que contenham código ao vivo, visualizações e texto narrativo. Os notebooks são um bom lugar para validar ideias e usar experimentos rápidos para obter insights de seus dados. Os notebooks também são amplamente utilizados na preparação de dados, visualização de dados, machine learning e outros cenários de Big Data.

Com um notebook Azure Synapse Studio, você pode:

* Comece com zero esforço de configuração.
* Mantenha os dados seguros com recursos de segurança corporativa incorporados.
* Analisar dados em formatos brutos (CSV, txt, JSON, etc.), formatos de arquivos processados (parquet, Delta Lake, ORC, etc.), e arquivos de dados tabulares SQL contra Spark e SQL.
* Seja produtivo com recursos aprimorados de autoria e visualização de dados incorporada.

Este artigo descreve como usar notebooks no Azure Synapse Studio.

## <a name="create-a-notebook"></a>Criar um notebook

Há duas maneiras de criar um caderno. Você pode criar um novo notebook ou importar um notebook existente para um espaço de trabalho Do Azure Synapse do **Object Explorer**. Os notebooks Azure Synapse Studio podem reconhecer arquivos IPYNB padrão do Jupyter Notebook.

![sinapse-criar-importação-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Desenvolver notebooks

Os cadernos consistem em células, que são blocos individuais de código ou texto que podem ser reparados independentemente ou em grupo.

### <a name="add-a-cell"></a>Adicione uma célula

Existem várias maneiras de adicionar um novo celular ao seu notebook.

1. Expanda o botão superior esquerdo **+ Célula** e selecione Adicionar célula **de código** ou Adicionar célula **de texto**.

    ![adicionar-célula com-celular-botão](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Passar o tempo sobre o espaço entre duas células e selecionar **Adicionar código** ou **Adicionar texto**.

    ![adicionar célula entre espaço](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Use [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Pressione **B** para inserir uma célula abaixo da célula atual.

### <a name="set-a-primary-language"></a>Defina uma linguagem primária

Os notebooks Azure Synapse Studio suportam quatro idiomas de faísca:

* pyspark (python)
* faísca (Scala)
* faíscaSQL
* Spark.NET (C#)

Você pode definir o idioma principal para novas células adicionadas da lista de itens de toda a lista na barra de comando superior.

   ![padrão-sinapse-linguagem](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Use vários idiomas

Você pode usar várias línguas em um notebook especificando o comando mágico da linguagem correta no início de uma célula. A tabela a seguir lista os comandos mágicos para mudar as línguas celulares.

|Comando mágico |Linguagem | Descrição |  
|---|------|-----|
|%% de pirolha| Python | Execute uma consulta **Python** contra o Spark Context.  |
|%% faísca| Scala | Execute uma consulta **Scala** contra o Spark Context.  |  
|%%sql| SparksqL | Execute uma consulta **SparkSQL** contra o Spark Context.  |
|%%csharp | Spark.NET C # | Execute uma **consulta Spark.NET C#** contra O Contexto de Faísca. |

A imagem a seguir é um exemplo de como você pode escrever uma consulta PySpark usando o comando **mágico %%pyspark** ou uma consulta SparkSQL com o comando **%%sql** magic em um notebook **Spark (Scala).** Observe que a língua primária para o notebook está definida como Scala.

   ![sinapse-faísca-magias](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Use tabelas temporárias para referenciar dados em idiomas

Não é possível referenciar dados ou variáveis diretamente em diferentes idiomas em um notebook Sinapse Studio. Em Spark, uma tabela temporária pode ser referenciada em todos os idiomas. Aqui está um exemplo de `Scala` como `PySpark` ler `SparkSQL` um DataFrame e usar uma tabela temporária Spark como solução.

1. Na Célula 1, leia um DataFrame do conector de pool SQL usando o Scala e crie uma tabela temporária.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Na Célula 2, consulta os dados usando Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Na Célula 3, use os dados em PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense estilo IDE

Os notebooks Azure Synapse Studio são integrados com o editor de Mônaco para levar o IntelliSense ao editor de celular no estilo IDE. O destaque da sintaxe, o criador de erros e os preenchimentos automáticos de código ajudam você a escrever códigos e identificar problemas mais rapidamente.

Os recursos do IntelliSense estão em diferentes níveis de maturidade para diferentes idiomas. Use a tabela abaixo para ver o que é suportado.

|Languages| Destaque da sintaxe | Marcador de erro de sintaxe  | Conclusão do código de sintaxe | Conclusão do código de variável| Conclusão do código da função do sistema| Conclusão do código da função do usuário| Recuo Inteligente | Dobrando código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Faísca (Scala)|Sim|Sim|Sim|Sim|-|-|-|Sim|
|SparksqL|Sim|Sim|-|-|-|-|-|-|
|Spark.NET (C#)|Sim|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formato célula de texto com botões de barra de ferramentas

Você pode usar os botões de formato na barra de ferramentas das células de texto para fazer ações comuns de marcação. Inclui texto em negrito, italicização de texto, inserção de trechos de código, inserção de lista não ordenada, inserção de lista ordenada e inserção de imagem de URL.

  ![sinapse-texto-célula-ferramenta](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Desfazer operações de células
Clique no botão **desfazer** ou **pressione Ctrl+Z** para revogar a operação celular mais recente. Agora você pode desfazer até as últimas 20 ações de células históricas. 

   ![sinapse-undo-células](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Mova uma célula

Selecione as elipses (...) para acessar o menu de ações celulares adicionais na extrema direita. Em seguida, **selecione Mover célula para cima** ou Mover célula para **baixo** para mover a célula atual. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Ctrl+Alt+↑** para subir a célula atual. Pressione **Ctrl+Alt+,** para mover a célula atual para baixo.

   ![mover-a-célula](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Exclua um celular

Para excluir uma célula, selecione as elipses (...) para acessar o menu de ações celulares adicionais na extrema direita e selecione **Excluir célula**. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **D,D** para excluir a célula atual.
  
   ![excluir-a-célula](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Colapso de uma entrada de célula
Clique no botão de seta na parte inferior da célula atual para colapsá-lo. Para expandi-lo, clique no botão de seta enquanto a célula está em colapso.

   ![entrada de células de colapso](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Colapso de uma saída celular

Clique no botão **de saída de colapso** no canto superior esquerdo da saída da célula atual para colapsá-lo. Para expandi-la, clique na **saída da célula Show** enquanto a saída da célula é colapsada.

   ![colapso-célula-saída](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Executar cadernos

Você pode executar as células de código em seu notebook individualmente ou todos de uma vez. O status e o progresso de cada célula estão representados no caderno.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código em uma célula.

1. Passar o tempo no celular que deseja executar e selecionar o botão **Run Cell** ou pressionar **Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Para acessar o menu de ações celulares adicionais na extrema direita, selecione as elipses (**...**). Em seguida, **selecione Executar célula**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Use [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Shift+Enter** para executar a célula atual e selecione a célula abaixo. Pressione **Alt+Enter** para executar a célula atual e inserir uma nova célula abaixo.


### <a name="run-all-cells"></a>Executar todas as células
Clique no botão **Executar tudo** para executar todas as células no notebook atual em seqüência.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

Para acessar o menu de ações celulares adicionais na extrema direita, selecione as elipses (**...**). Em seguida, **selecione Executar células acima** para executar todas as células acima da corrente em seqüência. Selecione **Executar células abaixo** para executar todas as células abaixo da corrente em seqüência.

   ![run-cells-acima ou abaixo](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indicador de status celular

Um status de execução de célula passo a passo é exibido sob a célula para ajudá-lo a ver seu progresso atual. Uma vez que a execução do celular esteja concluída, um resumo de execução com a duração total e o tempo final são mostrados e mantidos lá para referência futura.

![status celular](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso da faísca

O notebook Azure Synapse Studio é puramente baseado em Spark. As células de código são executadas na piscina spark remotamente. Um indicador de progresso do trabalho spark é fornecido com uma barra de progresso em tempo real parece ajudá-lo a entender o status de execução do trabalho.


![faísca-progresso-indicador](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuração da sessão de faísca

Você pode especificar a duração do tempo limite, o número e o tamanho dos executores a serem dados à sessão Spark atual na **sessão Configurar**. Reinicie a sessão Spark para que as alterações de configuração entrem em vigor. Todas as variáveis do notebook em cache são limpas.

![sessão-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Levar dados para um notebook

Você pode carregar dados do Azure Blob Storage, Azure Data Lake Store Gen 2 e Pool SQL, conforme mostrado nas amostras de código abaixo.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leia um CSV do Azure Data Lake Store Gen2 como um Spark DataFrame

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leia um CSV do Azure Blob Storage como um Quadro de Dados spark

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

### <a name="read-data-from-the-primary-storage-account"></a>Leia dados da conta de armazenamento principal

Você pode acessar dados na conta de armazenamento principal diretamente. Não há necessidade de fornecer as chaves secretas. No Data Explorer, clique com o botão direito do mouse em um arquivo e selecione **Novo notebook** para ver um novo notebook com extrator de dados gerado automaticamente.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualize dados em um notebook

### <a name="display"></a>Exibição()

Uma exibição de resultados tabulares é fornecida com a opção de criar um gráfico de barras, gráfico de linha, gráfico de tortas, gráfico de dispersão e gráfico de área. Você pode visualizar seus dados sem ter que escrever código. Os gráficos podem ser personalizados nas **Opções de Gráficos**. 

A saída de comandos mágicos **%%sql** aparece na exibição da tabela renderizada por padrão. Você pode chamar **a exibição`<DataFrame name>`()** na função Spark DataFrames ou NacD (Resilient Distributed Datasets sets) para produzir a exibição da tabela renderizada.

   ![gráficos construídos](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Você pode renderizar bibliotecas HTML ou interativas, como **bokeh,** usando o **displayHTML()**.

A imagem a seguir é um exemplo de plotagem de glifos sobre um mapa usando **bokeh**.

   ![bokeh exemplo](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Execute o seguinte código de amostra para desenhar a imagem acima.

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

Você pode salvar um único notebook ou todos os notebooks em seu espaço de trabalho.

1. Para salvar as alterações feitas em um único notebook, **selecione** o botão Publicar na barra de comando do notebook.

   ![publicar-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para salvar todos os notebooks em seu espaço de trabalho, **selecione** o botão Publicar todos na barra de comando do espaço de trabalho. 

   ![publicar-tudo](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do notebook, você pode configurar se deve incluir a saída do celular ao salvar.

   ![notebook-propriedades](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos mágicos
Você pode usar seus comandos mágicos Jupyter familiares em notebooks Azure Synapse Studio. Confira a lista abaixo como os atuais comandos mágicos disponíveis. Conte-nos seus casos de uso no GitHub para que possamos continuar a construir mais comandos mágicos para atender às suas necessidades.

Magias de linha disponíveis: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magias celulares disponíveis: [%%tempo](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%captura](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%faísca](#use-multiple-languages), [%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Teclas de atalho

Semelhante aos Notebooks Jupyter, os notebooks Azure Synapse Studio têm uma interface de usuário modal. O teclado faz coisas diferentes dependendo de qual modo a célula do notebook está. Os notebooks Synapse Studio suportam os dois modos a seguir para uma determinada célula de código: modo de comando e modo de edição.

1. Uma célula está no modo de comando quando não há cursor de texto que o instifique. Quando uma célula está no modo Comando, você pode editar o notebook como um todo, mas não digitar em células individuais. Digite o `ESC` modo de comando pressionando ou usando o mouse para clicar fora da área do editor de uma célula.

   ![modo de comando](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. O modo de edição é indicado por um cursor de texto que solicita que você digite a área do editor. Quando uma célula está no modo de edição, você não pode digitar na célula. Digite o `Enter` modo de edição pressionando ou usando o mouse para clicar na área do editor de uma célula.
   
   ![modo-de-edição](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho no modo de comando

Usando os seguintes atalhos de tecla, você pode navegar e executar mais facilmente códigos em notebooks Azure Synapse.

| Ação |Atalhos do notebook Sinapse Studio  |
|--|--|
|Execute a célula atual e selecione abaixo | Shift+Enter |
|Execute a célula atual e insira abaixo | Alt+Enter |
|Selecione célula acima| Para cima |
|Selecione célula abaixo| Para baixo |
|Inserir célula acima| Um |
|Inserir célula abaixo| B |
|Estender células selecionadas acima| Shift+Up |
|Estender células selecionadas abaixo| Shift+Down|
|Mova a célula para cima| Ctrl+Alt+↑ |
|Mova a célula para baixo| Ctrl+Alt+・ |
|Excluir células selecionadas| D, D |
|Mude para o modo de edição| Digite |

### <a name="shortcut-keys-under-edit-mode"></a>Teclas de atalho no modo de edição

Usando os seguintes atalhos de tecla, você pode navegar e executar códigos mais facilmente em notebooks Azure Synapse quando no modo Editar.

| Ação |Atalhos de notebook do Synapse Studio  |
|--|--|
|Mova o cursor para cima | Para cima |
|Mova o cursor para baixo|Para baixo|
|Desfazer|Ctrl + Z|
|Refaz|Ctrl + Y|
|Inserir/remover comentário|Ctrl + /|
|Excluir palavra antes|Ctrl + Backspace|
|Excluir palavra depois|Ctrl + Delete|
|Vá para o início da célula|Ctrl + Home|
|Vá para o fim da cela |Ctrl + End|
|Vá uma palavra para a esquerda|Ctrl + Esquerda|
|Vá uma palavra certa|Ctrl + Direito|
|Selecionar tudo|Ctrl + A|
|Travessão| Ctrl + ]|
|Dent|Ctrl + [|
|Mude para o modo de comando| Esc |

## <a name="next-steps"></a>Próximas etapas

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
