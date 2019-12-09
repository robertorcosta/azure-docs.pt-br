---
title: Transformar dados usando o databricks
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
ms.openlocfilehash: d21bab2f358e8ae9460bff2305957ed901c70926
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927744"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformar dados usando o Databricks no Azure Data Factory

Neste tutorial, você cria um pipeline de ponta a ponta que contém as atividades **Consulta**, **Cópia** e **Notebook Databricks** do Data Factory.

-   A atividade **Consulta** ou GetMetadata é usada para garantir que o conjunto de dados de origem esteja pronto para o consumo downstream, antes de disparar o trabalho de cópia e análise.

-   A **atividade Cópia** copia o arquivo de origem/conjunto de dados para o armazenamento do coletor. O armazenamento do coletor é montado como DBFS no notebook Databricks para que o conjunto de dados possa ser diretamente consumido pelo Spark.

-   A **atividade Notebook Databricks** dispara o notebook Databricks que transforma o conjunto de dados e o adiciona a uma pasta processada/data warehouse do SQL.

Para manter esse modelo simples, não é criado um gatilho agendado. Você pode adicionar um, se achar necessário.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Pré-requisitos

1.  Crie uma **conta de armazenamento de blobs** e um contêiner chamado `sinkdata` para ser usado como **coletor**. Anote o **nome da conta de armazenamento**, o **nome do contêiner** e a **chave de acesso**, pois eles são utilizados posteriormente no modelo.

2.  Verifique se você tem um **workspace do Azure Databricks** ou crie um novo.

1.  **Importe o notebook para ETL**. Importe o notebook Transform abaixo para o workspace Databricks. (Não precisa estar no mesmo local que está abaixo, mas lembre-se do caminho que você escolher para mais tarde). Importe o bloco de anotações da URL a seguir digitando esta URL no campo URL: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Selecione **Importar**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Agora vamos atualizar o notebook **Transformation** com suas **informações de conexão de armazenamento** (nome e chave de acesso). Acesse **comando 5** no notebook importado acima, substitua-o pelo snippet de código abaixo depois de substituir os valores destacados. Assegure-se de que essa conta seja a mesma conta de armazenamento criada anteriormente e que tenha o contêiner `sinkdata`.

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

1.  Gere um **token de acesso do Databricks** para o Data Factory acessá-lo. **Salve o token de acesso** para uso posterior na criação de um serviço vinculado ao Databricks, que se parece com 'dapi32db32cbb4w6eee18b7d87e45exxxxxx'

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Crie serviços e conjuntos de dados vinculados

1.  Crie novos **serviços vinculados** na interface de usuário do Data Factory acessando *Serviços Vinculados de Conexões + Novo*

    1.  **Fonte**: para acessar os dados de fonte. Você pode usar o armazenamento de blobs público que contém os arquivos de fonte para essa amostra.

        Selecione **Armazenamento de Blobs** , use o **URI de SAS** abaixo para conectar-se ao armazenamento de origem (acesso somente leitura).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Coletor**: para o qual copiar dados.

        Selecione um armazenamento criado no pré-requisito 1, no serviço vinculado ao coletor.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks**: para se conectar ao cluster do Databricks

        Crie um serviço vinculado do Databricks usando a chave de acesso gerada no pré-requisito 2.c. Se você tem um *cluster interativo*, pode selecioná-lo. (Este exemplo usa a opção *Novo cluster de trabalho*.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Crie **conjuntos de dados**

    1.  Crie **'sourceAvailability_Dataset'** para verificar se os dados de origem estão disponíveis

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Conjunto de dados de origem**: para copiar os dados de origem (usando cópia binária)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Conjunto de dados do coletor**: para copiar para o coletor/local de destino

        1.  Serviço vinculado: selecione 'sinkBlob_LS' criado na etapa 1.b

        2.  Caminho do arquivo: ' sinkdata/staged_sink'

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Crie atividades

1.  Crie uma atividade de pesquisa '**sinalizador de Disponibilidade**' para fazer uma verificação de Disponibilidade da fonte (use Lookup ou GetMetadata). Selecione 'sourceAvailability_Dataset' criado na etapa 2.a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Crie uma atividade de cópia '**file-to-blob**' para copiar o conjunto de dados da origem para o coletor. Nesse caso, os dados são arquivos binários. Consulte as capturas de tela abaixo para configurações da origem e do coletor na atividade de cópia.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Defina os **parâmetros do pipeline**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Crie uma **atividade do Databricks**

    Selecione o serviço vinculado criado em uma etapa anterior.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Defina as **configurações**. Crie **Parâmetros Base** como mostrado na captura de tela e crie parâmetros para serem passados para o notebook Databricks pelo Data Factory. Procure e **selecione** o **caminho correto do notebook** carregado no **pré-requisito 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Execute o pipeline**. Você pode encontrar um link para os logs do Databricks para saber mais sobre os logs do Spark.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Você também pode verificar o arquivo de dados usando o gerenciador de armazenamento. (Para correlacionar com execuções de pipeline do Data Factory, este exemplo anexa a ID de execução do pipeline do Data Factory à pasta de saída. Desta forma, você pode rastrear os arquivos gerados através de cada execução.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure Data Factory](introduction.md)
