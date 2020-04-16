---
title: Transformação com o Azure Databricks
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
ms.date: 03/03/2020
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414858"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com o Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você cria um pipeline completo que contém as atividades **de Validação,** Cópia de **dados**e **Notebook** na Fábrica de Dados do Azure.

- **A validação** garante que o conjunto de dados de origem esteja pronto para o consumo a jusante antes de acionar o trabalho de cópia e análise.

- **Os dados** de cópia duplicam o conjunto de dados de origem para o armazenamento do dissipador, que é montado como DBFS no notebook Azure Databricks. Desta forma, o conjunto de dados pode ser diretamente consumido pela Spark.

- **O notebook** aciona o notebook Databricks que transforma o conjunto de dados. Ele também adiciona o conjunto de dados a uma pasta processada ou ao Azure SQL Data Warehouse.

Para simplificar, o modelo neste tutorial não cria um gatilho programado. Você pode adicionar um, se necessário.

![Diagrama do gasoduto](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de armazenamento Azure `sinkdata` Blob com um contêiner chamado para uso como pia.

  Anote o nome da conta de armazenamento, o nome do contêiner e a chave de acesso. Você precisará desses valores mais tarde no modelo.

- Um espaço de trabalho do Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importar um notebook para transformação

Para importar um notebook **de transformação** para o seu espaço de trabalho Databricks:

1. Faça login no seu espaço de trabalho do Azure Databricks e, em seguida, selecione **Importar**.
       ![Comando menu para importar](media/solution-template-Databricks-notebook/import-notebook.png) um espaço de trabalho Seu caminho de espaço de trabalho pode ser diferente do mostrado, mas lembre-se dele para depois.
1. Selecione **Importar de: URL**. Na caixa de `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`texto, digite .

   ![Seleções para importar um notebook](media/solution-template-Databricks-notebook/import-from-url.png)

1. Agora vamos atualizar o notebook **Transformação** com suas informações de conexão de armazenamento.

   No notebook importado, vá para o **comando 5,** conforme mostrado no seguinte trecho de código.

   - `<storage name>`Substitua `<access key>` e com suas próprias informações de conexão de armazenamento.
   - Use a conta `sinkdata` de armazenamento com o recipiente.

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

1. Gere um **token de acesso do Databricks** para o Data Factory acessá-lo.
   1. Em seu espaço de trabalho Databricks, selecione o ícone do perfil do usuário no canto superior direito.
   1. Selecione **Configurações do usuário**.
    ![Comando menu para configurações do usuário](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selecione **Gerar novo token** na guia **Tokens de acesso.**
   1. Selecione **Gerar**.

    ![Botão "Gerar"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Salve o token de acesso para* uso posterior na criação de um serviço vinculado ao Databricks. O token de `dapi32db32cbb4w6eee18b7d87e45exxxxxx`acesso parece algo com .

## <a name="how-to-use-this-template"></a>Como usar este modelo

1. Vá para o modelo **Transformação com Databricks do Azure** e crie novos serviços vinculados para seguir conexões.

   ![Configuração de conexões](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Conexão Blob fonte** - para acessar os dados de origem.

       Para este exercício, você pode usar o armazenamento blob público que contém os arquivos de origem. Consulte a captura de tela a seguir para a configuração. Use o SEGUINTE **URL SAS** para se conectar ao armazenamento de origem (acesso somente leitura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Seleções para método de autenticação e URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Conexão Blob de Destino** - para armazenar os dados copiados.

       Na **janela de serviço vinculada New,** selecione a bolha de armazenamento da pia.

       ![Bolha de armazenamento de pia como um novo serviço vinculado](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - para se conectar ao cluster Databricks.

        Crie um serviço vinculado ao Databricks usando a chave de acesso que você gerou anteriormente. Você pode optar por selecionar um *cluster interativo* se tiver um. Este exemplo usa a opção **Novo cluster de trabalho.**

        ![Seleções para conexão ao cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecione **Usar este modelo**. Você verá um oleoduto criado.

    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração do pipeline

No novo pipeline, a maioria das configurações são configuradas automaticamente com valores padrão. Revise as configurações do seu pipeline e faça quaisquer alterações necessárias.

1. No sinalizador **Disponibilidade**de atividade **validação,** verifique se `SourceAvailabilityDataset` o valor do conjunto de dados **de** origem está definido como o que você criou anteriormente.

   ![Valor do conjunto de dados de origem](media/solution-template-Databricks-notebook/validation-settings.png)

1. Na **atividade copiar dados** **arquivo-para-blob,** verifique as guias **Origem** e **Sink.** Alterar as configurações, se necessário.

   - **Guia** ![de origem Guia de origem Guia de origem](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Guia 'Sink** sink ![Sink'](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Na atividade **do Notebook** **Transformação**, revise e atualize os caminhos e configurações conforme necessário.

   **O serviço vinculado a Databricks** deve ser pré-preenchido com ![o valor de uma etapa anterior, como mostrado: Valor preenchido para o serviço vinculado databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Para verificar as configurações do **Notebook:**
  
    1. Selecione a guia **Configurações.** Para **o caminho do notebook,** verifique se o caminho padrão está correto. Você pode precisar navegar e escolher o caminho correto do notebook.

       ![Caminho do notebook](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expanda o seletor **parâmetros** base e verifique se os parâmetros correspondem ao mostrado na captura de tela a seguir. Esses parâmetros são passados para o notebook Databricks da Data Factory.

       ![Parâmetros básicos](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verifique se os **parâmetros** do pipeline correspondem ao mostrado na captura de tela a seguir: ![Parâmetros do pipeline](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Conecte-se aos seus conjuntos de dados.

   - **SourceAvailabilityDataset** - para verificar se os dados de origem estão disponíveis.

     ![Seleções para serviço vinculado e caminho de arquivo para SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - para acessar os dados de origem.

       ![Seleções para serviço vinculado e caminho de arquivo para SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - para copiar os dados no local de destino do sink. Use os seguintes valores:

     - **Serviço vinculado,** - `sinkBlob_LS`criado em uma etapa anterior.

     - **Caminho do arquivo** - `sinkdata/staged_sink`.

       ![Seleções para serviço vinculado e caminho de arquivo para DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecione **Debug** para executar o pipeline. Você pode encontrar o link para registros do Databricks para registros mais detalhados do Spark.

    ![Link para registros databricks da saída](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Você também pode verificar o arquivo de dados usando o Azure Storage Explorer.

    > [!NOTE]
    > Para correlacionar com as corridas de pipeline da Fábrica de Dados, este exemplo anexa o ID de execução do pipeline da fábrica de dados para a pasta de saída. Isso ajuda a manter o controle dos arquivos gerados por cada execução.
    > ![ID de execução de pipeline anexado](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
