---
title: Azure HDInsight para Visual Studio Code
description: Saiba como usar as ferramentas do hive do Spark & (Azure HDInsight) para Visual Studio Code. Use as ferramentas para criar e enviar consultas e scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2020
ms.custom: devx-track-python
ms.openlocfilehash: bb2fff699b31d8b3b311180c4b85e2bfd1da892c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530146"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Use as ferramentas do Spark & Hive para Visual Studio Code

Saiba como usar Apache Spark & ferramentas Hive para Visual Studio Code. Use as ferramentas para criar e enviar Apache Hive trabalhos do lote, consultas interativas do hive e scripts PySpark para Apache Spark. Primeiro, descreveremos como instalar as ferramentas do Spark & Hive no Visual Studio Code. Em seguida, vamos examinar como enviar trabalhos para as ferramentas do Spark & Hive.  

As ferramentas do hive do Spark & podem ser instaladas em plataformas com suporte Visual Studio Code. Observe os seguintes pré-requisitos para diferentes plataformas.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster Azure HDInsight. Para criar um cluster, consulte [Introdução ao HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Ou use um cluster Spark e Hive que dê suporte a um ponto de extremidade Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). O mono é necessário apenas para Linux e macOS.
- [Um ambiente PySpark interativo para Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Um diretório local. Este artigo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalar Ferramentas do Spark & Hive

Depois de atender aos pré-requisitos, você pode instalar as ferramentas do Spark & Hive para Visual Studio Code seguindo estas etapas:

1. Abra o Visual Studio Code.

2. Na barra de menus, navegue até **Exibir**  >  **extensões**.

3. Na caixa de pesquisa, insira **Spark & Hive**.

4. Selecione **Ferramentas do hive do Spark &** nos resultados da pesquisa e selecione **instalar**:

   ![& hive do Spark para Visual Studio Code instalação do Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecione **recarregar** quando necessário.

## <a name="open-a-work-folder"></a>Abrir uma pasta de trabalho

Para abrir uma pasta de trabalho e criar um arquivo em Visual Studio Code, siga estas etapas:

1. Na barra de menus, navegue até **arquivo**  >  **abrir pasta..**  >  . **C:\HD\HDexample**e selecione o botão **Selecionar pasta** . A pasta aparece no modo de exibição do **Explorer** à esquerda.

2. Na exibição do **Gerenciador** , selecione a pasta **HDexample** e, em seguida, selecione o ícone **novo arquivo** ao lado da pasta de trabalho:

   ![ícone de novo arquivo do Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nomeie o novo arquivo usando a extensão de `.hql` arquivo (consultas do hive) ou `.py` (script do Spark). Este exemplo usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure

Para um usuário de nuvem nacional, siga estas etapas para definir o ambiente do Azure primeiro e, em seguida, use o comando **Azure: entrar** para entrar no Azure:

1. Navegue até **arquivo**  >  **preferências**  >  **configurações**.
2. Pesquise na seguinte cadeia de caracteres: **Azure: Cloud**.
3. Selecione a nuvem nacional na lista:

   ![Definir a configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Conectar-se a uma conta do Azure

Antes que você possa enviar scripts para seus clusters de Visual Studio Code, o usuário pode entrar na assinatura do Azure ou [vincular um cluster HDInsight](#link-a-cluster). Use a credencial Ambari/nome de usuário/senha ou ingresso no domínio para o cluster ESP para se conectar ao cluster HDInsight. Siga estas etapas para se conectar ao Azure:

1. Na barra de menus, navegue até **Exibir**  >  **paleta de comandos...** e insira **Azure: entrar**:

    ![Ferramentas do hive do Spark & para o logon do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de entrada para entrar no Azure. Depois que você estiver conectado, o nome da sua conta do Azure será exibido na barra de status na parte inferior da janela de Visual Studio Code.  

## <a name="link-a-cluster"></a>Vincular um cluster

### <a name="link-azure-hdinsight"></a>Link: Azure HDInsight

Você pode vincular um cluster normal usando um nome de usuário gerenciado pelo [Apache Ambari](https://ambari.apache.org/)ou pode vincular um cluster Hadoop seguro do pacote de segurança do Enterprise usando um nome de usuário de domínio (como: `user1@contoso.com` ).

1. Na barra de menus, navegue até **Exibir**  >  **paleta de comandos...** e insira **Spark/Hive: vincular um cluster**.

   ![Comando do cluster de link da paleta de comandos](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecione o tipo de cluster vinculado **Azure HDInsight**.

3. Insira a URL do cluster HDInsight.

4. Insira seu nome de usuário do Ambari; o padrão é **admin**.

5. Insira sua senha do Ambari.

6. Selecione o tipo de cluster.

7. Defina o nome de exibição do cluster (opcional).

8. Examine a exibição **SAÍDA** para verificação.

   > [!NOTE]  
   > O nome de usuário e a senha vinculados serão usados se o cluster fizer logon na assinatura do Azure e vinculado a um cluster.  

### <a name="link-generic-livy-endpoint"></a>Link: ponto de extremidade Livy genérico

1. Na barra de menus, navegue até **Exibir**  >  **paleta de comandos...** e insira **Spark/Hive: vincular um cluster**.

2. Selecione o tipo de cluster vinculado **Ponto de Extremidade Genérico do Livy**.

3. Insira o ponto de extremidade Livy genérico. Por exemplo: http \: //10.172.41.42:18080.

4. Selecione o tipo de autorização, **Básico** ou **Nenhum**.  Se você selecionar **básico**:  
    &emsp;a. Insira seu nome de usuário do Ambari; o padrão é **admin**.  
    &emsp;b. Insira sua senha do Ambari.

5. Examine a exibição **SAÍDA** para verificação.

## <a name="list-clusters"></a>Listar clusters

1. Na barra de menus, navegue até **Exibir**  >  **paleta de comandos...** e insira **Spark/Hive: list cluster**.

2. Selecione a assinatura desejada.

3. Examine a exibição **SAÍDA**. Essa exibição mostra o cluster (ou clusters) vinculado e todos os clusters em sua assinatura do Azure:

    ![Definir uma configuração de cluster padrão](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Definir o cluster padrão

1. Reabra a pasta **HDexample** que foi discutida [anteriormente](#open-a-work-folder), se fechada.  

2. Selecione o arquivo **HelloWorld. HQL** que foi criado [anteriormente](#open-a-work-folder). Ele é aberto no editor de scripts.

3. Clique com o botão direito do mouse no editor de scripts e selecione **Spark/Hive: definir cluster padrão**.  

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Selecione um cluster como o cluster padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o **.VSCode\settings.jsno arquivo de** configuração:

   ![Definir configuração de cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Enviar consultas de Hive interativas e scripts de lote do hive

Com as ferramentas do Spark & Hive para Visual Studio Code, você pode enviar consultas interativas do hive e scripts de lote do hive para seus clusters.

1. Reabra a pasta **HDexample** que foi discutida [anteriormente](#open-a-work-folder), se fechada.  

2. Selecione o arquivo **HelloWorld. HQL** que foi criado [anteriormente](#open-a-work-folder). Ele é aberto no editor de scripts.

3. Copie e cole o código a seguir em seu arquivo de Hive e, em seguida, salve-o:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Clique com o botão direito do mouse no editor de scripts e selecione **Hive: interativo** para enviar a consulta ou use o atalho de teclado CTRL + ALT + I.  Selecione **Hive: lote** para enviar o script ou use o atalho de teclado CTRL + ALT + H.  

6. Se você não tiver especificado um cluster padrão, selecione um cluster. As ferramentas também permitem que você envie um bloco de código em vez do arquivo de script inteiro usando o menu de contexto. Após alguns instantes, os resultados da consulta aparecem em uma nova guia:

   ![Resultado da consulta Apache Hive interativa](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel de **resultados** : você pode salvar o resultado inteiro como um arquivo CSV, JSON ou Excel em um caminho local ou apenas selecionar várias linhas.

    - Painel de **mensagens** : quando você seleciona um número de **linha** , ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas de PySpark

Os usuários podem executar o PySpark Interactive das seguintes maneiras:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Usando o comando interativo PySpark no arquivo PY
Usando o comando interativo PySpark para enviar as consultas, siga estas etapas:

1. Reabra a pasta **HDexample** que foi discutida [anteriormente](#open-a-work-folder), se fechada.  

2. Crie um novo arquivo **HelloWorld.py** , seguindo as etapas [anteriores](#open-a-work-folder) .

3. Copie e cole o seguinte código no arquivo de script:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. O prompt para instalar o kernel do PySpark é exibido no canto inferior direito da janela. Você pode clicar no botão **instalar** para prosseguir com as instalações do PySpark; ou clique no botão **ignorar** para ignorar esta etapa.

   ![Captura de tela mostra uma opção para ignorar a instalação do PySpark.](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Se precisar instalá-lo mais tarde, você poderá navegar até **arquivo**  >  **Preference**  >  **configurações**de preferência e desmarcar **Hdinsight: habilitar ignorar a instalação do Pyspark** nas configurações. 
    
    ![Captura de tela mostra a opção para habilitar a instalação do Skip Pyspark.](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Se a instalação for bem-sucedida na etapa 4, a caixa de mensagem "PySpark instalado com êxito" será exibida no canto inferior direito da janela. Clique no botão **recarregar** para recarregar a janela.
    ![pyspark instalado com êxito](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

8. Selecione todo o código, clique com o botão direito do mouse no editor de scripts e selecione **Spark: PySpark Interactive** para enviar a consulta. Ou use o atalho CTRL + ALT + I.

    ![menu de contexto do pyspark interativo](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

9. Selecione o cluster, se você não tiver especificado um cluster padrão. Após alguns instantes, os resultados **interativos do Python** aparecem em uma nova guia. Clique em PySpark para alternar o kernel para **PySpark**e o código será executado com êxito. As ferramentas também permitem que você envie um bloco de código em vez do arquivo de script inteiro usando o menu de contexto:

   ![janela de python interativo pyspark interativo](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

10. Insira **%% info**e pressione Shift + Enter para exibir as informações do trabalho (opcional):

    ![informações do trabalho de exibição interativa do pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

A ferramenta também dá suporte à consulta **SQL do Spark** :

   ![resultado da exibição interativa do pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Executar consulta interativa no arquivo PY usando um comentário #%%

1. Adicione **#%%** antes do código py para obter experiência com o notebook.

    ![Adicionar #%%](./media/hdinsight-for-vscode/run-cell.png)

2. Clique em **executar célula**. Após alguns instantes, os resultados de Python Interativo aparecem em uma nova guia.

   ![executar resultados da célula](./media/hdinsight-for-vscode/run-cell-get-results.png)

   > [!NOTE]  
   > Quando o kernel ou as configurações se bagunçarem, use o comando **Python: SELECT do intérprete para iniciar o Jupyter Server** e **reinicie o kernel do ipython**, em seguida, recarregue o VSCode, ele poderá ser resolvido.

## <a name="leverage-ipynb-support-from-python-extension"></a>Aproveite o suporte do IPYNB da extensão do Python

1. Você pode criar um Jupyter Notebook por comando na paleta de comandos ou criando um novo arquivo. ipynb em seu espaço de trabalho. Para obter mais informações, consulte [trabalhando com notebooks Jupyter no Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Clique em PySpark para alternar o kernel para **PySpark**e, em seguida, clique em **executar célula**, após um tempo, o resultado será exibido.

   ![executar resultados do ipynb](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
>A versão do MS-Python >= 2020.5.78807 não tem suporte nessa extensão é um [problema conhecido](#known-issues).

## <a name="submit-pyspark-batch-job"></a>Enviar trabalho em lotes PySpark

1. Reabra a pasta **HDexample** que você discutiu [anteriormente](#open-a-work-folder), se fechada.  

2. Crie um novo arquivo **BatchFile.py** seguindo as etapas [anteriores](#open-a-work-folder) .

3. Copie e cole o seguinte código no arquivo de script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Clique com o botão direito do mouse no editor de scripts e selecione **Spark: PySpark batch**ou use o atalho de teclado CTRL + ALT + H.

6. Selecione um cluster para o qual enviar seu trabalho do PySpark:

   ![Enviar saída de resultado do trabalho do Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Após você enviar um trabalho do Python, os logs de envio aparecem na janela de **SAÍDA** no Visual Studio Code. A URL da interface do usuário do Spark e a URL da interface do usuário yarn também são mostradas Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrar com o (HIB) Agente de Identidade do HDInsight

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Conectar-se ao cluster do HDInsight ESP com o agente de ID (HIB)

Você pode seguir as etapas normais para entrar na assinatura do Azure para se conectar ao cluster do HDInsight ESP com o agente de ID (HIB). Depois de entrar, você verá a lista de clusters no Azure Explorer. Para obter mais instruções, confira [Conectar-se a seu cluster do HDInsight](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Executar um trabalho de Hive/PySpark em um cluster do HDInsight com o agente de ID (HIB)

Para executar um trabalho do hive, você pode seguir as etapas normais para enviar o trabalho ao cluster do HDInsight ESP com o agente de ID (HIB). Consulte [enviar consultas de Hive interativas e scripts de lote do hive](#submit-interactive-hive-queries-and-hive-batch-scripts) para obter mais instruções.

Para executar um trabalho de PySpark interativo, você pode seguir as etapas normais para enviar o trabalho para o cluster do HDInsight ESP com o agente de ID (HIB). Consulte [enviar consultas interativas do PySpark](#submit-interactive-pyspark-queries) para obter mais instruções.

Para executar um trabalho do lote PySpark, você pode seguir as etapas normais para enviar o trabalho ao cluster do HDInsight para o agente com ID (HIB). Consulte [enviar o trabalho do lote PySpark](#submit-pyspark-batch-job) para obter mais instruções.


## <a name="apache-livy-configuration"></a>Configuração do Apache Livy

Há suporte para a configuração do [Apache Livy](https://livy.incubator.apache.org/) . Você pode configurá-lo no **.VSCode\settings.jsno** arquivo na pasta do espaço de trabalho. Atualmente, a configuração do Livy dá suporte apenas ao script de Python. Para obter mais informações, consulte [LIVY README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Como disparar a configuração do Livy**

Método 1  
1. Na barra de menus, navegue até **arquivo**  >  **preferências**  >  **configurações**.
2. Na caixa **configurações de pesquisa** , insira **envio de trabalho do HDInsight: Livy conf**.  
3. Selecione **Editar em settings.json** para o resultado da pesquisa relevante.

O método 2 envia um arquivo e observa que a `.vscode` pasta é adicionada automaticamente à pasta de trabalho. Você pode ver a configuração do Livy selecionando **.vscode\settings.jsem**.

+ As configurações do projeto:

    ![Configuração do Apache Livy do HDInsight](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Para as configurações de **driverMemory** e **executorMemory** , defina o valor e a unidade. Por exemplo: 1G ou 1024M.

+ Configurações de Livy com suporte:

    **Postar/batches** Corpo da solicitação

    | name | descrição | tipo |
    | --- | --- | --- |
    | Arquivo  | Arquivo que contém o aplicativo a ser executado | Caminho (obrigatório) |
    | proxyUser | Usuário a representar ao executar o trabalho | String |
    | className | Classe principal de Java/Spark do aplicativo | String |
    | args | Argumentos de linha de comando para o aplicativo | Lista de cadeias de caracteres |
    | jars | Jars a serem usados nesta sessão | Lista de cadeias de caracteres | 
    | pyFiles | Arquivos Python a serem usados nesta sessão | Lista de cadeias de caracteres |
    | files | Arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | driverMemory | Quantidade de memória a ser usada para o processo do driver | String |
    | driverCores | Número de núcleos a serem usados para o processo do driver | Int |
    | executorMemory | Quantidade de memória a ser usada pelo processo de executor | String |
    | executorCores | Número de núcleos a serem usados para cada executor | Int |
    | numExecutors | Número de executores a serem iniciados para esta sessão | Int |
    | archives | Arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | fila | Nome da fila de YARN a ser enviada| String |
    | name | Nome desta sessão | String |
    | conf | Propriedades de configuração do Spark | Mapa de chave = valor |

    Corpo da resposta o objeto do lote criado.

    | name | descrição | tipo |
    | --- | ---| --- |
    | ID | ID da sessão | Int |
    | appId | ID do aplicativo desta sessão | String |
    | appInfo | Informações detalhadas do aplicativo | Mapa de chave = valor |
    | log | Linhas de log | Lista de cadeias de caracteres |
    | state |Estado do lote | String |

    > [!NOTE]
    > A configuração Livy atribuída é exibida no painel de saída quando você envia o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar ao Azure HDInsight pelo Explorer

Você pode visualizar a tabela do hive em seus clusters diretamente por meio do **Azure HDInsight** Explorer:

1. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure se ainda não tiver feito isso.

2. Selecione o ícone do **Azure** na coluna mais à esquerda.

3. No painel esquerdo, expanda **Azure: HDINSIGHT**. As assinaturas e os clusters disponíveis são listados.

4. Expanda o cluster para exibir o banco de dados de metadados do hive e o esquema de tabela.

5. Clique com o botão direito do mouse na tabela Hive. Por exemplo: **hivesampletable**. Selecione **Visualização**.

   ![Hive do Spark & para a tabela do hive do Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. A janela **Visualizar resultados** é aberta:

   ![& hive do Spark para Visual Studio Code janela de resultados da visualização](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Painel de resultados

   Você pode salvar todo o resultado como um arquivo CSV, JSON ou Excel em um caminho local ou apenas selecionar várias linhas.

- Painel de mensagens
   1. Quando o número de linhas na tabela for maior que 100, você verá a seguinte mensagem: "as primeiras 100 linhas são exibidas para a tabela Hive".
   2. Quando o número de linhas na tabela for menor ou igual a 100, você verá a seguinte mensagem: "60 linhas são exibidas para a tabela do hive".
   3. Quando não houver conteúdo na tabela, você verá a seguinte mensagem: " `0 rows are displayed for Hive table.` "

        >[!NOTE]
        >
        >No Linux, instale o XCLIP para habilitar os dados da tabela de cópia.
        >
        >![Spark & Hive para Visual Studio Code no Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Recursos adicionais

O Spark & Hive para Visual Studio Code também dá suporte aos seguintes recursos:

- **Preenchimento automático do IntelliSense**. Sugestões pop up para palavras-chave, métodos, variáveis e outros elementos de programação. Os diferentes ícones representam diferentes tipos de objetos:

    ![Ferramentas do hive do Spark & para objetos Visual Studio Code IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha erros de edição no script do hive.     
- **Destaques da sintaxe**. O serviço de idioma usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções e outros elementos de programação:

    ![Destaques de sintaxe das Ferramentas do Spark & Hive para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Função somente leitura

Os usuários que recebem a função somente de leitor para o cluster não podem enviar trabalhos para o cluster HDInsight nem exibir o banco de dados do hive. Contate o administrador de cluster para atualizar sua função para o [**operador de cluster HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) no [portal do Azure](https://ms.portal.azure.com/). Se você tiver credenciais Ambari válidas, poderá vincular manualmente o cluster usando as diretrizes a seguir.

### <a name="browse-the-hdinsight-cluster"></a>Procurar o cluster HDInsight  

Ao selecionar o Azure HDInsight Explorer para expandir um cluster HDInsight, você será solicitado a vincular o cluster se tiver a função somente de leitor para o cluster. Use o método a seguir para vincular ao cluster usando suas credenciais do Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Enviar o trabalho para o cluster HDInsight

Ao enviar o trabalho para um cluster HDInsight, você será solicitado a vincular o cluster se estiver na função somente de leitor para o cluster. Use as etapas a seguir para vincular ao cluster usando as credenciais do Ambari.

### <a name="link-to-the-cluster"></a>Link para o cluster

1. Insira um nome de usuário Ambari válido.
2. Insira uma senha válida.

   ![Ferramentas do hive do Spark & para Visual Studio Code nome de usuário](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Ferramentas do hive do Spark & para Visual Studio Code senha](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Você pode usar `Spark / Hive: List Cluster` para verificar o cluster vinculado:
  >
  >![Ferramentas do hive do Spark & para Visual Studio Code Reader vinculado](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Procurar uma conta de Data Lake Storage Gen2

Selecione o Azure HDInsight Explorer para expandir uma conta de Data Lake Storage Gen2. Você será solicitado a inserir a chave de acesso de armazenamento se sua conta do Azure não tiver acesso ao armazenamento Gen2. Depois que a chave de acesso for validada, a conta de Data Lake Storage Gen2 será expandida automaticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Enviar trabalhos para um cluster HDInsight com Data Lake Storage Gen2

Envie um trabalho para um cluster HDInsight usando Data Lake Storage Gen2. Você será solicitado a inserir a chave de acesso de armazenamento se sua conta do Azure não tiver acesso de gravação ao armazenamento Gen2. Depois que a chave de acesso for validada, o trabalho será enviado com êxito.

![Ferramentas do hive & Spark para Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Você pode obter a chave de acesso para a conta de armazenamento do portal do Azure. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Desvincular cluster

1. Na barra de menus, vá para **Exibir**  >  **paleta de comandos**e, em seguida, insira **Spark/Hive: Desvincular um cluster**.  

2. Selecione um cluster para desvincular.  

3. Consulte a exibição de **saída** para verificação.  

## <a name="sign-out"></a>Sair  

Na barra de menus, vá para **Exibir**  >  **paleta de comandos**e, em seguida, insira **Azure: sair**.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>Não há suporte para a versão MS-Python >= 2020.5.78807 nesta extensão 

"Falha ao conectar ao Jupyter Notebook". é um problema conhecido para a versão do Python >= 2020.5.78807. É recomendável que os usuários usem a versão **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** do MS-Python para evitar esse problema.

![Problemas conhecidos do](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>Próximas etapas

Para ver um vídeo que demonstra o uso do Spark & Hive para Visual Studio Code, consulte [spark & Hive para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
