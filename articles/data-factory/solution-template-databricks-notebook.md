---
title: Transformação com Azure Databricks
description: Saiba como usar um modelo de solução para transformar dados usando um notebook Databricks no Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227596"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com Azure Databricks

Neste tutorial, você cria um pipeline de ponta a ponta que contém atividades de **validação**, **cópia**e **Notebook** no data Factory.

-   A atividade de **validação** é usada para garantir que o conjunto de fonte de origem esteja pronto para o consumo downstream, antes de disparar o trabalho de cópia e de análise.

-   A atividade de **cópia** copia o arquivo/conjunto de arquivos de origem para o armazenamento do coletor. O armazenamento do coletor é montado como DBFS no notebook Databricks para que o conjunto de dados possa ser diretamente consumido pelo Spark.

-   A atividade do **databricks Notebook** dispara o bloco de anotações do databricks que transforma o conjunto de e adiciona-o a uma pasta processada/SQL DW.

Para manter esse modelo simples, não é criado um gatilho agendado. Você pode adicionar um, se achar necessário.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

1. Crie uma **conta de armazenamento de blobs** e um contêiner chamado `sinkdata` para ser usado como **coletor**. Anote o **nome da conta de armazenamento**, o **nome do contêiner** e a **chave de acesso**, pois eles são utilizados posteriormente no modelo.

2. Verifique se você tem um **workspace do Azure Databricks** ou crie um novo.

3. **Importe o bloco de anotações para transformação**. 
    1. No Azure Databricks, faça referência a capturas de tela a seguir para importar um bloco de anotações de **transformação** para o espaço de trabalho do databricks. Ele não precisa estar no mesmo local que está abaixo, mas lembre-se do caminho que você escolher para mais tarde.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Selecione "importar de: **URL**" e digite a seguinte URL na caixa de texto:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. Agora, vamos atualizar o bloco de anotações de **transformação** com suas informações de conexão de armazenamento. Vá para o **comando 5** (conforme mostrado no trecho de código abaixo) no bloco de anotações importado acima e substitua `<storage name>`e `<access key>` com suas próprias informações de conexão de armazenamento. Assegure-se de que essa conta seja a mesma conta de armazenamento criada anteriormente e que tenha o contêiner `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Gere um **token de acesso do Databricks** para o Data Factory acessá-lo. **Salve o token de acesso** para uso posterior na criação de um serviço vinculado do databricks, que é semelhante a ' dapi32db32cbb4w6eee18b7d87e45exxxxxx '.

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>Como usar este modelo

1.  Vá para **transformação com Azure Databricks** modelo. Crie novos serviços vinculados para as seguintes conexões. 
    
    ![Configuração de conexões](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Conexão de blob de origem** – para acessar dados de origem. 
        
        Você pode usar o armazenamento de blobs público que contém os arquivos de fonte para essa amostra. Faça referência à captura de tela a seguir para configuração. Use a **URL SAS** abaixo para se conectar ao armazenamento de origem (acesso somente leitura): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Conexão de blob de destino** – para copiar dados no. 
        
        No serviço vinculado do coletor, selecione um armazenamento criado no **pré-requisito** 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** – para se conectar ao cluster do databricks.

        Crie um serviço vinculado do databricks usando a chave de acesso gerada no **pré-requisito** 2. c. Se você tem um *cluster interativo*, pode selecioná-lo. (Este exemplo usa a opção *Novo cluster de trabalho*.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Selecione **usar este modelo**e você verá um pipeline criado conforme mostrado abaixo:
    
    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração de pipeline

No novo pipeline criado, a maioria das configurações foi configurada automaticamente com valores padrão. Confira as configurações e atualize quando necessário para atender às suas próprias configurações. Para obter detalhes, você pode verificar as instruções e capturas de tela abaixo para obter referência.

1.  Um sinalizador de **disponibilidade** da atividade de validação é criado para fazer uma verificação de disponibilidade de origem. *SourceAvailabilityDataset* criado na etapa anterior é selecionado como DataSet.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Um **arquivo-para-blob** da atividade de cópia é criado para copiar o conjunto de código da origem para o coletor. Consulte as capturas de tela abaixo para configurações da origem e do coletor na atividade de cópia.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Uma **transformação** de atividade do bloco de anotações é criada e o serviço vinculado criado na etapa anterior é selecionado.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Selecione a guia **configurações** . Para o *caminho do bloco de anotações*, o modelo define um caminho por padrão. Talvez seja necessário procurar e selecionar o caminho correto do bloco de anotações carregado no **pré-requisito** 2. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Confira os *parâmetros de base* criados conforme mostrado na captura de tela. Eles devem ser passados para o bloco de anotações do databricks de Data Factory. 

         ![Parâmetros de base](media/solution-template-Databricks-notebook/base-parameters.png)

1.  Os **parâmetros de pipeline** são definidos como abaixo.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Configurando conjuntos de os.
    1.  **SourceAvailabilityDataset** é criado para verificar se os dados de origem estão disponíveis.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** -para copiar os dados de origem.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** – para copiar para o local do coletor/destino.

        1.  Serviço vinculado- *sinkBlob_LS* criado na etapa anterior.

        2.  Caminho do arquivo- *SinkData/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Selecione **depurar** para executar o pipeline. Você pode encontrar um link para os logs do Databricks para saber mais sobre os logs do Spark.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Você também pode verificar o arquivo de dados usando o gerenciador de armazenamento. (Para correlacionar com execuções de pipeline do Data Factory, este exemplo anexa a ID de execução do pipeline do Data Factory à pasta de saída. Desta forma, você pode rastrear os arquivos gerados através de cada execução.)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Introdução ao Azure Data Factory](introduction.md)
