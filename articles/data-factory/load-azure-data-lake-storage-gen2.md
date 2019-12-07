---
title: Carregar dados no Azure Data Lake Store Gen2
description: Usar o Azure Data Factory para copiar dados para o Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 203fd294f90e3b904116c1ddd72f581c293cba13
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891092"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory

O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Isso permite fazer interface com os dados usando paradigmas de armazenamento de objetos e sistema de arquivos.

Azure Data Factory (ADF) é um serviço de integração de dados baseado em nuvem totalmente gerenciado. É possível usar o serviço para preencher o Data Lake com dados de um conjunto avançado de armazenamentos de dados locais e baseados em nuvem e economizar tempo ao compilar as soluções de análise. Para obter uma lista detalhada de conectores com suporte, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimentação de dados gerenciados de expansão. Devido à arquitetura de expansão do ADF, é possível ingerir dados com alta taxa de transferência. Para obter detalhes, consulte [Desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo descreve como usar a ferramenta Copiar Dados do Data Factory para carregar dados do _serviço Amazon Web Services S3_ no _Azure Data Lake Store Gen2_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2, consulte [este passo a passo específico](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de armazenamento do Azure com Data Lake Storage Gen2 habilitado: se você não tiver uma conta de armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Conta de AWS com um bucket S3 que contém dados: este artigo mostra como copiar dados do Amazon S3. Você pode usar outros repositórios de dados seguindo as etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**:
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir: 
      
   ![Página de novo data factory](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "Nome do Data factory \"LoadADLSDemo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_ **ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **V2**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. 

3. Clique em **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Página inicial do data factory](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados no Azure Data Lake Store Gen2

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data: 

   ![Bloco Ferramenta Copy Data](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na página **Propriedades**, especifique Copy Data, especifique **CopyFromAmazonS3ToADLS** para o campo **Nome da tarefa** e clique em **Avançar**:

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, clique em **+Criar nova conexão**:

    ![Página Armazenamento de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selecione **Amazon S3** na galeria de conectores e selecione **Continuar**
    
    ![Página Armazenamento de dados de origem s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na página **Especificar conexão Amazon S3**, faça o seguinte:

   1. Especifique o valor da **ID da chave de acesso**.
   2. Especifique o valor da **chave de acesso secreta**.
   3. Clique em **Testar conectividade** para validar as configurações, selecione **Concluir**.
   4. Você verá uma nova conexão ser criada. Selecione **Avançar**.
   
      ![Especifique a conta do Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na página **Escolher arquivo de entrada ou pasta** página, navegue até a pasta e o arquivo que você deseja copiar. Selecione a pasta/arquivo, selecione **Escolher**:

    ![Escolha a pasta ou arquivo de entrada](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Especifique o comportamento da cópia, marcando as opções **Copiar arquivos recursivamente** e **Cópia binária**. Selecione **Avançar**:

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na página **Armazenamento de dados de destino**, clique em **+ Criar nova conexão**, selecione **Azure Data Lake Storage Gen2** e **Continuar**:

    ![Página Armazenamento de dados de destino](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na página **Especificar conexão do Armazenamento do Azure Data Lake**, siga as seguintes etapas:

   1. Selecione a conta do Data Lake Store Gen2 compatível na lista suspensa "Nome da conta de armazenamento".
   2. Selecione **Concluir** para criar a conexão. Em seguida, selecione **Avançar**.
   
   ![Especificar a conta do Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na página **escolher o arquivo de saída ou a pasta** , insira **copyfroms3** como o nome da pasta de saída e selecione **Avançar**. O ADF criará o sistema de arquivos ADLS Gen2 e as subpastas correspondentes durante a cópia, se ela não existir.

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na página **Configurações**, selecione **Avançar** para usar as configurações padrão:

    ![Página Configurações](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na página **Resumo**, examine as configurações e selecione **Avançar**:

    ![Página Resumo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **Página de implantação**, selecione **Monitorar** para monitorar o pipeline:

    ![Página Implantação](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente:

    ![Monitorar execuções de pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline. Assim, você vê apenas uma entrada. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (imagem de óculos) em **Ações** na exibição de monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Monitorar detalhes da execução da atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Verifique se os dados são copiados para a conta do Data Lake Store Gen2.

## <a name="next-steps"></a>Próximos passos

* [Visão geral da atividade de cópia](copy-activity-overview.md)
* [Conector do Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md)
