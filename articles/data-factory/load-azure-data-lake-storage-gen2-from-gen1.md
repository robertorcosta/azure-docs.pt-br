---
title: Copiar dados do Azure Data Lake Storage Gen1 para gen2
description: Usar o Azure Data Factory para copiar dados do Azure Data Lake Storage Gen1 para Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 81f8577592f1d53627bc09a2f9ace8c060ad4660
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668864"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiar dados do Azure Data Lake Storage Gen1 para Gen2 com o Azure Data Factory

O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de big data que é incorporado ao [armazenamento Azure Blob](../storage/blobs/storage-blobs-introduction.md). Você pode usá-lo para interagir com seus dados usando paradigmas de armazenamento de arquivos e objetos.

Se você atualmente usa o Azure Data Lake Storage Gen1, você pode avaliar o Azure Data Lake Storage Gen2 copiando dados do Data Lake Storage Gen1 para o Gen2 usando o Azure Data Factory.

O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para preencher o lago com dados de um rico conjunto de armazenamentos de dados no local e na nuvem e economizar tempo quando você constrói suas soluções de análise. Para obter uma lista de conectores suportados, consulte a tabela de armazenamentos de [dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimentação de dados gerenciados de expansão. Devido à arquitetura de escala de Data Factory, ele pode ingerr dados com um alto throughput. Para obter mais informações, consulte [O desempenho da atividade do Copy](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de dados de cópia da Fábrica de Dados para copiar dados do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta do Azure Data Lake Store Gen1 contendo dados.
* Conta de armazenamento azure com Data Lake Storage Gen2 ativado. Se você não tiver uma conta de armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, **selecione Criar um recurso** > **Data + Analytics** > **Data Factory**.
   
   ![Seleção da Fábrica de Dados no Novo painel](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na **página de fábrica de dados Nova,** forneça valores para os campos mostrados na seguinte imagem: 
      
   ![Nova página de fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "Nome do Data factory \"LoadADLSDemo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Crie a fábrica de dados novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista de paradas. Você também pode selecionar a nova opção **Criar** e inserir o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 
    * **Versão**: selecione **V2**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados usados pelo data factory podem estar em outras localizações e regiões. 

3. Selecione **Criar**.
4. Após o término da criação, vá para sua fábrica de dados. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Página inicial do data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selecione o **bloco De monitor de & autor** para iniciar o aplicativo Integração de dados em uma guia separada.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados no Azure Data Lake Store Gen2

1. Na página **'Iniciar',** selecione o bloco **Copiar dados** para iniciar a ferramenta de dados de cópia. 

   ![Copiar ladrilho da ferramenta de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na página **Propriedades,** especifique **CopyFromADLSGen1ToGen2** para o campo **Nome da tarefa.** Selecione **Avançar**.

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na página do **armazenamento de dados Origem,** selecione **+ Crie uma nova conexão**.

    ![Página Armazenamento de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selecione **Azure Data Lake Storage Gen1** na galeria de conectores e selecione **Continuar**.
    
    ![Página de armazenamento de dados de origem do Azure Data Lake Store Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na página **de conexão Specifie Azure Data Lake Storage Gen1,** siga estas etapas:

   a. Selecione o Data Lake Storage Gen1 para o nome da conta e especifique ou valide o **Locatário**.
  
   b. Selecione **A conexão Teste** para validar as configurações. Em seguida, selecione **Concluir**.
  
   c. Você vê que uma nova conexão foi criada. Selecione **Avançar**.
   
   > [!IMPORTANT]
   > Neste passo a passo, você usa uma identidade gerenciada para os recursos do Azure para autenticar o Azure Data Lake Storage Gen1. Para conceder à identidade gerenciada as permissões adequadas no Azure Data Lake Storage Gen1, siga [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especifique a conta do Azure Data Lake Store Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na **página Escolher o arquivo de entrada ou pasta,** navegue até a pasta e o arquivo que deseja copiar. Selecione a pasta ou o arquivo e selecione **Escolher**.

    ![Escolha a pasta ou arquivo de entrada](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Especifique o comportamento da cópia selecionando os **arquivos Copiar recursivamente** e **opções de cópia binária.** Selecione **Avançar**.

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na página do **armazenamento de dados Destino,** selecione **+ Crie nova conexão** > **Azure Data Lake Storage Gen2** > **Continue**.

    ![Página Armazenamento de dados de destino](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na página **de conexão Specifie Azure Data Lake Storage Gen2,** siga estas etapas:

   a. Selecione sua conta com capacidade para armazenamento de dados Gen2 na lista de desígeração do nome da **conta armazenamento.**
   
   b. Selecione **Concluir** para criar a conexão. Em seguida, selecione **Next**.
   
   ![Especificar a conta do Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na **página Escolher o arquivo de saída ou pasta,** digite **copyfromadlsgen1** como o nome da pasta de saída e selecione **Next**. A Fábrica de Dados cria o sistema de arquivos Azure Data Lake Storage Gen2 correspondente e subpastas durante a cópia, se elas não existirem.

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na página **Configurações**, selecione **Avançar** para usar as configurações padrão.

12. Na página **Resumo,** revise as configurações e selecione **Next**.

    ![Página Resumo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **página 'Implantação',** selecione **Monitor** para monitorar o pipeline.

    ![Página Implantação](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente.

    ![Monitorar execuções de pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline. Assim, você vê apenas uma entrada. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (imagem de óculos) em **Ações** na exibição de monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da fonte para o dissipador, throughput de dados, etapas de execução com duração correspondente e configurações usadas.

    ![Monitorar detalhes da execução da atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verifique se os dados foram copiados para sua conta Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Práticas recomendadas

Para avaliar a atualização do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2 em geral, consulte [Atualize suas soluções de análise de big data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). As seções a seguir introduzem práticas recomendadas para o uso da Data Factory para uma atualização de dados do Data Lake Storage Gen1 para o Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partição de dados para cópia de dados históricos

- Se o tamanho total dos dados no Data Lake Storage Gen1 for inferior a 30 TB e o número de arquivos for inferior a 1 milhão, você poderá copiar todos os dados em uma única execução de atividade de cópia.
- Se você tiver uma quantidade maior de dados para copiar, ou quiser a flexibilidade para gerenciar a migração de dados em lotes e fazer com que cada um deles seja concluído dentro de um período de tempo específico, particione os dados. A particionagem também reduz o risco de qualquer problema inesperado.

Use uma prova de conceito para verificar a solução completa e testar o throughput da cópia em seu ambiente. Principais etapas de prova de conceito: 

1. Crie um pipeline de Fábrica de Dados com uma única atividade de cópia para copiar vários TBs de dados do Data Lake Storage Gen1 para o Data Lake Storage Gen2 para obter uma linha de base de desempenho de cópia. Comece com [unidades de integração de dados (DIUs)](copy-activity-performance-features.md#data-integration-units) como 128. 
2. Com base no throughput de cópia que você recebe na etapa 1, calcule o tempo estimado necessário para toda a migração de dados. 
3. (Opcional) Crie uma tabela de controle e defina o filtro de arquivo para particionar os arquivos a serem migrados. A maneira de particionar os arquivos é: 

    - Partição por nome da pasta ou nome da pasta com um filtro curinga. Este método é recomendável.
    - Partição pelo último tempo modificado de um arquivo.

### <a name="network-bandwidth-and-storage-io"></a>Largura de banda da rede e I/O de armazenamento 

Você pode controlar a concorrência de trabalhos de cópia da Fábrica de Dados que lêem dados do Data Lake Storage Gen1 e escrevem dados para data lake storage Gen2. Desta forma, você pode gerenciar o uso dessa I/O de armazenamento para evitar afetar o trabalho normal de negócios no Data Lake Storage Gen1 durante a migração.

### <a name="permissions"></a>Permissões 

Na Fábrica de Dados, o [conector Data Lake Storage Gen1](connector-azure-data-lake-store.md) suporta o principal do serviço e a identidade gerenciada para autenticações de recursos do Azure. O [conector Data Lake Storage Gen2](connector-azure-data-lake-storage.md) suporta a chave da conta, o principal de serviço e a identidade gerenciada para autenticações de recursos do Azure. Para tornar a Data Factory capaz de navegar e copiar todos os arquivos ou listas de controle de acesso (ACLs) que você precisa, conceda permissões altas o suficiente para a conta que você fornece para acessar, ler ou escrever todos os arquivos e definir ACLs se você quiser. Conceda-lhe uma função de superusuário ou proprietário durante o período de migração. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar ACLs do Data Lake Storage Gen1

Se você quiser replicar as ACLs juntamente com arquivos de dados ao atualizar de Data Lake Storage Gen1 para Data Lake Storage Gen2, consulte [Preservar ACLs do Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Cópia incremental 

Você pode usar várias abordagens para carregar apenas os arquivos novos ou atualizados do Data Lake Storage Gen1:

- Carregar arquivos novos ou atualizados por tempo particionado pasta ou nome do arquivo. Um exemplo é /2019/05/13/*.
- Carregar arquivos novos ou atualizados por LastModifiedDate.
- Identifique arquivos novos ou atualizados por qualquer ferramenta ou solução de terceiros. Em seguida, passe o nome do arquivo ou da pasta para o pipeline da Fábrica de Dados via parâmetro ou uma tabela ou arquivo. 

A freqüência adequada para fazer a carga incremental depende do número total de arquivos no Azure Data Lake Storage Gen1 e do volume de arquivos novos ou atualizados a serem carregados todas as vezes. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Copiar visão geral da atividade](copy-activity-overview.md)
> [azure Data Lake Storage Gen1 conector](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 conector](connector-azure-data-lake-storage.md)