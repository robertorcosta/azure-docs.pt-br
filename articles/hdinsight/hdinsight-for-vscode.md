---
title: Azure HDInsight para Visual Studio Code
description: Aprenda a usar o Spark & Hive Tools (Azure HDInsight) para criar e enviar consultas e scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435677"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Use ferramentas de colmeia spark & para o código visual do estúdio

Aprenda a usar o Spark & Hive Tools for Visual Studio Code para criar e enviar trabalhos em lote do Apache Hive, consultas interativas de Colmeia e scripts PySpark para Apache Spark. Primeiro descreveremos como instalar ferramentas de colmeia Spark & no Visual Studio Code, e depois vamos mostrar como enviar trabalhos para a Spark & Hive Tools.  

As Ferramentas do Spark & Hive podem ser instaladas em plataformas com suporte do Visual Studio Code, que incluem Windows, Linux e macOS. Observe os seguintes pré-requisitos para diferentes plataformas.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster Azure HDInsight. Para criar um cluster, consulte [Introdução ao HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Ou use um cluster de Faísca e Colméia que suporta um ponto final apache livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono é necessário apenas para Linux e macOS.
- [Um ambiente interativo PySpark para visual studio code](set-up-pyspark-interactive-environment.md).
- Um diretório local. Este artigo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalar Ferramentas do Spark & Hive

Depois de atender aos pré-requisitos, você pode instalar as Ferramentas de Colmeia Spark & para o Visual Studio Code seguindo estas etapas:

1. Abra o Visual Studio Code.

2. Na barra de menus, navegue até **Exibir** > **extensões**.

3. Na caixa de pesquisa, insira **Spark & Hive**.

4. Selecione **Spark & Ferramentas colmeias** dos resultados da pesquisa e, em seguida, **selecione Instalar**:

   ![Spark & Colmeia para visual Studio Code Python instalar](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecione **Recarregar** quando necessário.

## <a name="open-a-work-folder"></a>Abra uma pasta de trabalho

Para abrir uma pasta de trabalho e criar um arquivo no Visual Studio Code, siga estas etapas:

1. Na barra de menu, navegue até **File** > **Open Folder...**  >  **C:\HD\HDexample**e, em seguida, selecione o botão **Selecionar pasta.** A pasta aparece no modo de exibição do **Explorer** à esquerda.

2. Na **exibição Do Explorer,** selecione a pasta **HDexample** e selecione o ícone **Novo Arquivo** ao lado da pasta de trabalho:

   ![visual studio código novo ícone de arquivo](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nomeie o novo `.hql` arquivo usando a extensão `.py` de arquivo (hive) ou (script Spark). Este exemplo usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure

Para um usuário nacional de nuvem, siga estas etapas para definir primeiro o ambiente Azure e, em seguida, use o comando **Azure: Faça login** no Azure:

1. Navegue até **as** > **configurações de preferências de** > **arquivo**.
2. Pesquisar na seguinte string: **Azure: Cloud**.
3. Selecione a nuvem nacional na lista:

   ![Definir a configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Conecte-se a uma conta do Azure

Antes de enviar scripts para seus clusters do Visual Studio Code, você deve se conectar à sua conta do Azure ou vincular um cluster (usando o nome de usuário apache Ambari e credenciais de senha ou uma conta de domínio). Siga estas etapas para se conectar ao Azure:

1. Na barra de menus, navegue até **Exibir** > **paleta de comando...** e **digite Azure: Faça login**:

    ![Spark & Ferramentas de Colmeia para login visual studio code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de login para entrar no Azure. Depois de conectado, o nome da conta do Azure é mostrado na barra de status na parte inferior da janela Visual Studio Code.  

## <a name="link-a-cluster"></a>Vincular um cluster

### <a name="link-azure-hdinsight"></a>Link: Azure HDInsight

Você pode vincular um cluster normal usando um nome de usuário gerenciado pelo [Apache Ambari](https://ambari.apache.org/)ou pode vincular um cluster `user1@contoso.com`Hadoop seguro do Enterprise Security Pack usando um nome de usuário de domínio (como: ).

1. Na barra de menus, navegue até **Exibir** > **paleta de comando...** e **digite Spark / Hive: Link a Cluster**.

   ![Comando de cluster de link da Paleta de Comando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecione o tipo de cluster vinculado **Azure HDInsight**.

3. Digite a URL do cluster HDInsight.

4. Digite seu nome de usuário Ambari; o padrão é **o admin**.

5. Digite sua senha Ambari.

6. Selecione o tipo de cluster.

7. Defina o nome de exibição do cluster (opcional).

8. Examine a exibição **SAÍDA** para verificação.

   > [!NOTE]  
   > O nome de usuário e a senha vinculados são usados se o cluster entrar na assinatura do Azure e vincular um cluster.  

### <a name="link-generic-livy-endpoint"></a>Link: Ponto final genérico da Livy

1. Na barra de menus, navegue até **Exibir** > **paleta de comando...** e **digite Spark / Hive: Link a Cluster**.

2. Selecione o tipo de cluster vinculado **Ponto de Extremidade Genérico do Livy**.

3. Digite o ponto final genérico de Livy. Por exemplo:\:http //10.172.41.42:18080.

4. Selecione o tipo de autorização, **Básico** ou **Nenhum**.  Se você selecionar **Básico:**  
    &emsp;a. Digite seu nome de usuário Ambari; o padrão é **o admin**.  
    &emsp;b. Digite sua senha Ambari.

5. Examine a exibição **SAÍDA** para verificação.

## <a name="list-clusters"></a>Listar clusters

1. Na barra de menus, navegue até **Exibir** > **paleta de comando...** e **digite Spark / Hive: List Cluster**.

2. Selecione a assinatura que você deseja.

3. Examine a exibição **SAÍDA**. Esta exibição mostra seu cluster vinculado (ou clusters) e todos os clusters sua assinatura do Azure:

    ![Definir uma configuração de cluster padrão](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Defina o cluster padrão

1. Reabrir a pasta **HDexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Selecione o arquivo **HelloWorld.hql** que foi criado [anteriormente](#open-a-work-folder). Abre no editor de roteiro.

3. Clique com o botão direito do mouse no editor de script e, em seguida, **selecione Faísca / Colmeia: Definir cluster padrão**.  

4. [Conecte-se](#connect-to-an-azure-account) à sua conta do Azure ou vincule um cluster se você ainda não o fez.

5. Selecione um cluster como o cluster padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o **. Arquivo de configuração VSCode\settings.json:**

   ![Definir configuração de cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Envie consultas interativas da Colmeia e scripts em lote da Colmeia

Com o Spark & Hive Tools for Visual Studio Code, você pode enviar consultas interativas da Colmeia e scripts em lote da Colmeia para seus clusters.

1. Reabrir a pasta **HDexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Selecione o arquivo **HelloWorld.hql** que foi criado [anteriormente](#open-a-work-folder). Abre no editor de roteiro.

3. Copie e cole o seguinte código no seu arquivo Hive e, em seguida, salve-o:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Conecte-se](#connect-to-an-azure-account) à sua conta do Azure ou vincule um cluster se você ainda não o fez.

5. Clique com o botão direito do mouse no editor de scripts e **selecione Hive: Interativo** para enviar a consulta ou usar o atalho de teclado Ctrl+Alt+I.  Selecione **Hive: Lote** para enviar o script ou usar o atalho de teclado Ctrl+Alt+H.  

6. Se você não tiver especificado um cluster padrão, selecione um cluster. As ferramentas também permitem que você envie um bloco de código em vez de todo o arquivo de script usando o menu de contexto. Após alguns momentos, os resultados da consulta aparecem em uma nova guia:

   ![Resultado interativo da consulta da Colmeia Apache](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Painel RESULTADOS:** Você pode salvar todo o resultado como um arquivo CSV, JSON ou Excel em um caminho local ou apenas selecionar várias linhas.

    - **Painel MENSAGENS:** Quando você seleciona um número **de linha,** ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas de PySpark

Para enviar consultas interativas do PySpark, siga estas etapas:

1. Reabrir a pasta **HDexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Crie um novo **arquivo HelloWorld.py,** seguindo as etapas [anteriores.](#open-a-work-folder)

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

4. [Conecte-se](#connect-to-an-azure-account) à sua conta do Azure ou vincule um cluster se você ainda não o fez.

5. Selecione todo o código, clique com o botão direito do mouse no editor de script e selecione **Spark: PySpark Interactive** para enviar a consulta. Ou, use o atalho Ctrl+Alt+I.

   ![menu de contexto do pyspark interativo](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selecione o cluster, se você não tiver especificado um cluster padrão. Após alguns momentos, os resultados **do Python Interactive** aparecem em uma nova guia. As ferramentas também permitem que você envie um bloco de código em vez de todo o arquivo de script usando o menu de contexto:

   ![janela de python interativo pyspark interativo](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Digite **%%info**e pressione Shift+Enter para visualizar as informações do trabalho (opcional):

   ![pyspark informações de trabalho de visualização interativa](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. A ferramenta também suporta a consulta **Spark SQL:**

   ![Resultado da visualização Pyspark Interactive](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   O status de envio aparece à esquerda da barra de status inferior quando você está executando consultas. Não envie outras consultas quando o status for **PySpark Kernel (ocupado)**.  

   > [!NOTE]
   >
   > Quando **o Python Extension Enabled** for liberado nas configurações (é selecionado por padrão), os resultados de interação de pyspark enviados usarão a janela antiga:
   >
   > ![extensão de python interativa do pyspark desabilitada](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Enviar trabalho em lotes PySpark

1. Reabra a pasta **HDexample** que você discutiu [anteriormente,](#open-a-work-folder)se fechada.  

2. Crie um novo arquivo **BatchFile.py** seguindo as etapas [anteriores.](#open-a-work-folder)

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

4. [Conecte-se](#connect-to-an-azure-account) à sua conta do Azure ou vincule um cluster se você ainda não o fez.

5. Clique com o botão direito do mouse no editor de script e selecione **Spark: PySpark Batch**ou use o atalho de teclado Ctrl+Alt+H.

6. Selecione um cluster para enviar seu trabalho do PySpark para:

   ![Enviar produção de resultado do trabalho Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Após você enviar um trabalho do Python, os logs de envio aparecem na janela de **SAÍDA** no Visual Studio Code. O URL da UI Spark e o URL de UI do Fio também são mostrados. Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.

## <a name="apache-livy-configuration"></a>Configuração do Apache Livy

A configuração [apache livy](https://livy.incubator.apache.org/) é suportada. Você pode configurá-lo no **. ARQUIVO VSCode\settings.json** na pasta workspace. Atualmente, a configuração do Livy dá suporte apenas ao script de Python. Para obter mais detalhes, consulte [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Como ativar a configuração livy**

Método 1  
1. Na barra de menus, navegue até **as** > **configurações de preferências de** > **arquivo**.
2. Na caixa **De configurações de pesquisa,** digite **HDInsight Job Submission: Livy Conf**.  
3. Selecione **Editar em settings.json** para o resultado da pesquisa relevante.

Método 2 Envie um arquivo e observe que a pasta .vscode foi adicionada automaticamente à pasta de trabalho. Você pode ver a configuração De Livy selecionando **.vscode\settings.json**.

+ As configurações do projeto:

    ![Configuração HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Para as configurações **driverMemória** e **executorMemória,** defina o valor e a unidade. Por exemplo: 1g ou 1024m.

+ Configurações de Livy suportadas:

    **POST /lotes** Solicitar corpo

    | name | descrição | type |
    | :- | :- | :- |
    | file | Arquivo que contém o aplicativo a ser executado | Caminho (obrigatório) |
    | proxyUser | Usuário a representar ao executar o trabalho | String |
    | className | Classe principal de Java/Spark do aplicativo | String |
    | args | Argumentos de linha de comando para o aplicativo | Lista de strings |
    | jars | Frascos para serem usados nesta sessão | Lista de strings | 
    | pyFiles | Arquivos Python a serem usados nesta sessão | Lista de strings |
    | files | Arquivos a serem usados nesta sessão | Lista de strings |
    | driverMemory | Quantidade de memória a ser usada para o processo do driver | String |
    | driverCores | Número de núcleos a serem usados para o processo do driver | Int |
    | executorMemory | Quantidade de memória a ser usada pelo processo de executor | String |
    | executorCores | Número de núcleos a serem usados para cada executor | Int |
    | numExecutors | Número de executores a serem iniciados para esta sessão | Int |
    | archives | Arquivos a serem usados nesta sessão | Lista de strings |
    | fila | Nome da fila do YARN a ser submetido| String |
    | name | Nome desta sessão | String |
    | conf | Propriedades de configuração do Spark | Mapa de chave = valor |

    Corpo de resposta O objeto Batch criado.

    | name | descrição | type |
    | :- | :- | :- |
    | id | Id da sessão | Int |
    | appId | Id de aplicação desta sessão | String |
    | appInfo | Informações detalhadas do aplicativo | Mapa de chave = valor |
    | log | Linhas de log | Lista de strings |
    | state |Estado do lote | String |

    > [!NOTE]
    > A configuração de Livy atribuída é exibida no painel de saída quando você envia o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar ao Azure HDInsight pelo Explorer

Você pode visualizar a Tabela Colmeia em seus clusters diretamente através do explorador **Azure HDInsight:**

1. [Conecte-se](#connect-to-an-azure-account) à sua conta do Azure se você ainda não fez isso.

2. Selecione o ícone **Azure** na coluna mais à esquerda.

3. A partir do painel esquerdo, **expanda AZURE: HDINSIGHT**. As assinaturas e clusters disponíveis estão listados.

4. Expanda o cluster para visualizar o banco de dados de metadados da Colmeia e o esquema da tabela.

5. Clique com o botão direito do mouse na tabela Colmeia. Por exemplo: **colmeia.** Selecione **Visualização**.

   ![Spark & Hive para visual studio code tabela de visualização colmeia](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. A janela **Resultados de visualização** é aberta:

   ![Spark & Hive for Visual Studio Code preview results window](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Painel DE RESULTADOS

   Você pode salvar todo o resultado como um arquivo CSV, JSON ou Excel em um caminho local ou apenas selecionar várias linhas.

- Painel MENSAGENS
   1. Quando o número de linhas na tabela é maior que 100, você vê a seguinte mensagem: "As primeiras 100 linhas são exibidas para a tabela Colmeia".
   2. Quando o número de linhas na tabela é menor ou igual a 100, você vê uma mensagem como a seguinte: "60 linhas são exibidas para a tabela Colmeia".
   3. Quando não há conteúdo na tabela, você vê a seguinte mensagem: "0 linhas são exibidas para a tabela Colmeia".

        >[!NOTE]
        >
        >No Linux, instale xclip para ativar dados de tabela de cópia.
        >
        >![Spark & Colmeia para código Visual Studio no Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Recursos adicionais

Spark & Hive for Visual Studio Code também suporta os seguintes recursos:

- **IntelliSense autocompletar**. Sugestões aparecem para palavras-chave, métodos, variáveis e outros elementos de programação. Os diferentes ícones representam diferentes tipos de objetos:

    ![Spark & Ferramentas de Colmeia para objetos Visual Studio Code IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Marcador de erro IntelliSense**. O serviço de idiomas sublinha erros de edição no script Hive.     
- **Destaques da sintaxe**. O serviço de linguagem usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções e outros elementos de programação:

    ![Destaques de sintaxe das Ferramentas do Spark & Hive para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Função somente leitura

Os usuários que são atribuídos à função somente para leitor para o cluster não podem mais enviar trabalhos para o cluster HDInsight, nem podem visualizar o banco de dados Hive. Entre em contato com o administrador de clusters para atualizar sua função para [**HDInsight Cluster Operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) no [portal Azure](https://ms.portal.azure.com/). Se você tiver credenciais Ambari válidas, você pode vincular manualmente o cluster usando a seguinte orientação.

### <a name="browse-the-hdinsight-cluster"></a>Navegue pelo cluster HDInsight  

Quando você seleciona o explorador Azure HDInsight para expandir um cluster HDInsight, você é solicitado a vincular o cluster se você tiver a função somente para o leitor para o cluster. Use o seguinte método para vincular ao cluster usando suas credenciais Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Envie o trabalho para o cluster HDInsight

Ao enviar o trabalho para um cluster HDInsight, você é solicitado a vincular o cluster se você estiver na função somente de leitor para o cluster. Use as seguintes etapas para vincular ao cluster usando credenciais Ambari.

### <a name="link-to-the-cluster"></a>Link para o cluster

1. Digite um nome de usuário Ambari válido.
2. Digite uma senha válida.

   ![Spark & Ferramentas de Colmeia para o nome de usuário do código visual do estúdio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Ferramentas de Colmeia para senha de código visual do estúdio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Você pode `Spark / Hive: List Cluster` usar para verificar o cluster vinculado:
  >
  >![Spark & Ferramentas de Colmeia para Leitor de Código visual studio ligado](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Navegue por uma conta Data Lake Storage Gen2

Quando você seleciona o explorador Azure HDInsight para expandir uma conta Data Lake Storage Gen2, você é solicitado a inserir a chave de acesso ao armazenamento se sua conta do Azure não tiver acesso ao armazenamento Gen2. Depois que a chave de acesso é validada, a conta Data Lake Storage Gen2 é expandida automaticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Enviar trabalhos para um cluster HDInsight com Data Lake Storage Gen2

Quando você envia um trabalho para um cluster HDInsight usando data lake storage Gen2, você é solicitado a inserir a chave de acesso de armazenamento se sua conta Do Azure não tiver acesso à gravação ao armazenamento Gen2. Depois que a chave de acesso for validada, o trabalho será submetido com sucesso.

![Spark & Ferramentas de Colmeia para Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Você pode obter a chave de acesso para a conta de armazenamento no portal Azure. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Desvincular cluster

1. Na barra de menus, vá para **Exibir** > **paleta de**comando , e, em seguida, **digite Faísca / Colmeia: Desvincular um cluster**.  

2. Selecione um cluster para desvincular.  

3. Consulte a **exibição OUTPUT** para verificação.  

## <a name="sign-out"></a>Sair  

Na barra de menus, vá para **Exibir** > **paleta de**comando e, em seguida, **digite Azure: Sign Out**.

## <a name="next-steps"></a>Próximas etapas

Para obter um vídeo que demonstre usando Spark & Hive for Visual Studio Code, consulte [Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
