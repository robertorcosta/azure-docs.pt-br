---
title: Usar pontos de extremidade privados para criar um pipeline do Azure Data Factory
description: Este tutorial fornece instruções passo a passo para usar o portal do Azure a fim de criar um data factory com um pipeline. O pipeline usa a atividade de cópia para copiar dados do Armazenamento de Blobs do Azure para um Banco de Dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540437"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copiar dados com segurança do Armazenamento de Blobs do Azure para um Banco de Dados SQL usando pontos de extremidade privados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você criará um data factory ao usar a interface do usuário do Azure Data Factory. **O pipeline deste data factory copia os dados com segurança do Armazenamento de Blobs do Azure para um Banco de Dados SQL do Azure (permitindo o acesso somente às redes selecionadas) usando pontos de extremidade privados em [Rede Virtual gerenciada do Azure Data Factory](managed-virtual-network-private-endpoint.md).** O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira a tabela [Armazenamentos de dados com suporte](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Se estiver se familiarizando com o Data Factory, confira a [Introdução ao Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Neste tutorial, você executa as seguintes etapas:

> * Criar uma data factory
> * Criar um pipeline com uma atividade de cópia


## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de *origem*. Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) a fim de conhecer as etapas para criar uma. **Verifique se a conta de armazenamento permite acesso somente em 'Redes selecionadas'.** 
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de *coletor*. Se você não tiver um Banco de Dados SQL do Azure, confira [Criar um banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) para saber as etapas para criar um. **Verifique se a conta do Banco de Dados SQL do Azure permite acesso somente em 'Redes selecionadas'.** 

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu armazenamento de blobs e o banco de dados SQL para o tutorial executando as etapas a seguir.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **emp.txt** no disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Crie um contêiner chamado **adftutorial** no seu Armazenamento de blobs. Crie uma pasta chamada **input** nesse contêiner. Em seguida, carregue o arquivo **emp.txt** na pasta **input**. Use as ferramentas ou o portal do Azure, como [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para realizar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o script SQL a seguir para criar a tabela **dbo.emp** em seu banco de dados SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Permita que os serviços do Azure acessem o SQL Server. Verifique se a opção **Permitir acesso aos serviços do Azure** está **ATIVADA** para o SQL Server, para que o Data Factory possa gravar dados no SQL Server. Para verificar e ativar essa configuração, vá até seu servidor do Azure SQL > Visão geral > Configurar o firewall do servidor > defina a opção **Permitir acesso aos serviços do Azure** para **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory.

1. Abra o **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.


2. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**.

3. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](https://docs.microsoft.com/azure/data-factory/naming-rules).

4. Selecione a **assinatura** do Azure na qual deseja criar o data factory.

5. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. Em **Versão**, selecione **V2**.

7. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.


8. Selecione **Criar**.


9. Depois que a criação for concluída, você verá o aviso no centro de notificações. Selecione **Ir para o recurso** para navegar até a página do Data Factory.

10. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Criar um Azure Integration Runtime na Rede Virtual Gerenciada do ADF
Nesta etapa, você criará um Azure Integration Runtime e habilitará a Rede Virtual Gerenciada.

1. No portal do ADF, acesse **Gerenciar Hub** e clique em **Novo** para criar um Azure Integration Runtime.
   ![Criar um Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Opte por criar um Azure** Integration Runtime.
   ![Novo Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Habilite **Rede Virtual**.
   ![Novo Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selecione **Criar**.

## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você criará um pipeline com a atividade de cópia no data factory. A Atividade de cópia copia dados do Armazenamento de blobs para o Banco de Dados SQL. No [Tutorial de início rápido](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal), você criou um pipeline seguindo estas etapas:

1. Criar o serviço vinculado.
1. Criar conjuntos de dados de entrada e saída.
1. Crie um pipeline.

Neste tutorial, inicie com a criação do pipeline. Em seguida, crie conjuntos de dados e serviços vinculados quando forem necessários para configurar o pipeline.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Criar um pipeline](./media/doc-common-process/get-started-page.png)
1. No painel **Propriedades** do pipeline, insira **CopyPipeline** como o **Nome** do pipeline.

1. Na caixa de ferramentas de **Atividades**, expanda a categoria **Mover e transformar** e arraste e solte a atividade **Copiar dados** da caixa de ferramentas para a superfície do designer do pipeline. Especifique **CopyFromBlobToSql** para o **Nome**.

    ![Atividade de cópia](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurar fonte

>[!TIP]
>Neste tutorial, você usa *Chave de conta* como o tipo de autenticação para o armazenamento de dados de origem, mas você pode escolher outros métodos de autenticação compatíveis: *URI de SAS*, *Entidade de Serviço* e *Identidade Gerenciada* se necessário. Veja as seções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) para obter detalhes.
>Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para obter ilustrações detalhadas.

#### <a name="create-source-dataset-and-linked-service"></a>Criar um conjunto de dados de origem e um serviço vinculado

1. Vá para a guia **Origem**. Selecione **+ Novo** para criar um conjunto de dados de origem.

1. Na caixa de diálogo **Novo conjunto de dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Continuar**. Os dados de origem estão em um Armazenamento de blobs e, portanto, você deve selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Selecionar formato**, escolha o tipo de formato dos seus dados e, em seguida, selecione **Continuar**.

1. Na caixa de diálogo **Definir propriedades**, insira **SourceBlobDataset** como o nome. Marque a caixa de seleção de **Primeira linha como cabeçalho**. Na caixa de texto **Serviço vinculado**, selecione **+ Novo**.

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , insira **AzureStorageLinkedService** como o nome e selecione sua conta de armazenamento na lista **Nome da conta de armazenamento**. 

1. Lembre-se de habilitar **Criação Interativa**. Pode levar cerca de um minuto para habilitar essa opção.

    ![Criação interativa](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selecione **Testar conectividade**; ela deverá falhar quando a conta de armazenamento permitir acesso somente na "Rede Selecionada" e exigir que o Azure Data Factory crie um ponto de extremidade privado nela, que deverá ser aprovado antes do uso. Na mensagem de erro, você verá um link para criar um **ponto de extremidade privado** que poderá seguir para criar um ponto de extremidade privado gerenciado. *Uma alternativa é acessar diretamente a guia Gerenciar e seguir as instruções da [próxima seção](#create-a-managed-private-endpoint) para criar um ponto de extremidade privado gerenciado*
> [!NOTE]
> É possível que a guia Gerenciar não fique disponível para todas as instâncias do data factory. Se ela não for exibida, você ainda poderá acessar pontos de extremidade privados por meio da guia '**Criar**' --> '**Conexões**' --> '**Ponto de Extremidade Privado**'
1. Mantenha a caixa de diálogo aberta e acesse sua conta de armazenamento selecionada acima.

1. Siga as instruções [desta seção](#approval-of-a-private-link-in-storage-account) para aprovar o link privado.

1. Volte à caixa de diálogo. Clique novamente em **Testar conectividade** e selecione **Criar** para implantar o serviço vinculado.

1. Depois que o serviço vinculado for criado, ele será redirecionado de volta para a página **Definir propriedades**. Ao lado de **Caminho do arquivo**, selecione **Procurar**.

1. Navegue até a pasta **adftutorial/input**, selecione o arquivo **emp.txt** e, em seguida, selecione **OK**.

1. Selecione **OK**. Ele navega automaticamente para a página do pipeline. Na guia **Origem**, confirme se **SourceBlobDataset** está selecionado. Para visualizar os dados da página, selecione **Visualizar dados**.

    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Caso você não tenha clicado no hiperlink ao testar a conexão acima, siga o caminho a seguir. Agora você precisará criar um ponto de extremidade privado gerenciado que será conectado ao serviço vinculado criado acima.

1. Acesse a guia Gerenciar.
> [!NOTE]
> É possível que a guia Gerenciar não fique disponível para todas as instâncias do data factory. Se ela não for exibida, você ainda poderá acessar pontos de extremidade privados por meio da guia '**Criar**' --> '**Conexões**' --> '**Ponto de Extremidade Privado**'

1. Acesse a seção Pontos de extremidade privados gerenciados.

1. Selecione **+ Novo** em Pontos de extremidade privados gerenciados.

    ![Novo ponto de extremidade privado gerenciado](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o bloco do Armazenamento de Blobs do Azure na lista e escolha **Continuar**.

1. Insira o nome da conta de armazenamento criada acima.

1. Selecione **Criar**.

1. Depois de aguardar alguns segundos, você verá que o link privado criado precisa de uma aprovação.

1. Selecione o ponto de extremidade privado criado acima. Você poderá ver um hiperlink que vai levar você à aprovação do ponto de extremidade privado no nível da conta de armazenamento.

    ![Gerenciar o ponto de extremidade privado](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Aprovação de um link privado na conta de armazenamento
1. Na conta de armazenamento, acesse **Conexões de ponto de extremidade privado** na seção Configurações.

1. Marque o ponto de extremidade privado que você criou acima e selecione **Aprovar**.

    ![Aprovar ponto de extremidade privado](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adicione uma descrição e clique em **Sim**
1. Volte à seção **Pontos de extremidade privados gerenciados** da guia **Gerenciar** no Azure Data Factory.
1. Deverá levar cerca de 1 a 2 minutos para que a aprovação do ponto de extremidade privado seja refletida na interface do usuário do Azure Data Factory.


### <a name="configure-sink"></a>Configurar coletor
>[!TIP]
>Neste tutorial, você usa *Autenticação de SQL* como o tipo de autenticação para o armazenamento de dados coletor, mas você pode escolher outros métodos de autenticação compatíveis: *Entidade de Serviço* e *Identidade Gerenciada* se necessário. Veja as seções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) para obter detalhes.
>Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para obter ilustrações detalhadas.

#### <a name="create-sink-dataset-and-linked-service"></a>Criar um conjunto de dados do coletor e um serviço vinculado
1. Alterne para a guia **Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor.

1. Na caixa de diálogo **Novo conjunto de dados**, insira "SQL" na caixa de pesquisa para filtrar os conectores, selecione **Banco de Dados SQL do Azure** e, em seguida, selecione **Continuar**. Neste tutorial, você copia dados para um banco de dados SQL.

1. Na caixa de diálogo **Definir propriedades**, insira **OutputSqlDataset** como o nome. Na lista suspensa **Serviço vinculado**, selecione **+ Novo**. Um conjunto de dados deve ser associado um serviço vinculado. O serviço vinculado tem a cadeia de conexão que o Data Factory usa para se conectar ao banco de dados SQL no runtime. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) para o qual os dados são copiados.

1. Na caixa de diálogo **Novo serviço vinculado (Banco de Dados SQL do Azure)** , execute as etapas a seguir:

    1. Em **Nome**, insira **AzureSqlDatabaseLinkedService**.
    1. Em **Nome do servidor**, selecione sua instância do SQL Server.
    1. Lembre-se de habilitar **Criação Interativa**.
    1. Em **Nome do banco de dados**, selecione seu banco de dados SQL.
    1. Em **Nome de usuário**, insira o nome do usuário.
    1. Em **Senha**, insira a senha do usuário.
    1. Selecione **Testar conexão**. Ela deverá falhar, porque o SQL Server permite o acesso somente em 'redes selecionadas' e exige que o Azure Data Factory crie um ponto de extremidade privado nela, que deverá ser aprovado antes do uso. Na mensagem de erro, você verá um link para criar um **ponto de extremidade privado** que poderá seguir para criar um ponto de extremidade privado gerenciado. *Uma alternativa é acessar diretamente a guia Gerenciar e seguir as instruções da próxima seção para criar um ponto de extremidade privado gerenciado*
    1. Mantenha a caixa de diálogo aberta e acesse o SQL Server selecionado acima.    
    1. Siga as instruções [desta seção](#approval-of-a-private-link-in-sql-server) para aprovar o link privado.
    1. Volte à caixa de diálogo. Clique novamente em **Testar conectividade** e selecione **Criar** para implantar o serviço vinculado.

1. Ele navega automaticamente para a caixa de diálogo **Definir propriedades**. Em **Tabela**, selecione **[dbo].[emp]** . Depois, selecione **OK**.

1. Alterne para a guia com o pipeline e, em **Conjunto de Dados do Coletor**, confirme se **OutputSqlDataset** está selecionado.

    ![Guia Pipeline](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Opcionalmente, é possível mapear o esquema da origem para o esquema de destino correspondente seguindo [Mapeamento de esquema na atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping).

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Caso você não tenha clicado no hiperlink ao testar a conexão acima, siga o caminho a seguir. Agora você precisará criar um ponto de extremidade privado gerenciado que será conectado ao serviço vinculado criado acima.

1. Acesse a guia Gerenciar.
1. Acesse a seção Pontos de extremidade privados gerenciados.
1. Selecione **+ Novo** em Pontos de extremidade privados gerenciados.

    ![Novo ponto de extremidade privado gerenciado](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o bloco do Banco de Dados SQL do Azure na lista e escolha **Continuar**.
1. Insira o nome do SQL Server selecionado acima.
1. Selecione **Criar**.
1. Depois de aguardar alguns segundos, você verá que o link privado criado precisa de uma aprovação.
1. Selecione o ponto de extremidade privado criado acima. Você poderá ver um hiperlink que vai levar você à aprovação do ponto de extremidade privado no nível do SQL Server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Aprovação de um link privado no SQL Server
1. No SQL Server, acesse **Conexões de ponto de extremidade privado** na seção Configurações.
1. Marque o ponto de extremidade privado que você criou acima e selecione **Aprovar**.
1. Adicione uma descrição e clique em **Sim**.
1. Volte à seção **Pontos de extremidade privados gerenciados** da guia **Gerenciar** no Azure Data Factory.
1. Deverá levar cerca de 1 a 2 minutos para que a aprovação do ponto de extremidade privado seja refletida.

#### <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline

Você pode depurar um pipeline antes de publicar os artefatos (serviços vinculados, conjuntos de dados e pipeline) no Data Factory ou em seu próprio repositório Git do Azure Repos.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela.
2. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Data Factory.
3. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique em **Mostrar Notificações** no canto superior direito (botão de sino).


#### <a name="summary"></a>Resumo
O pipeline deste exemplo copia dados do Armazenamento de Blobs para o BD SQL do Azure usando o ponto de extremidade privado na Rede Virtual gerenciada. Você aprendeu a:

> * Criar uma data factory
> * Criar um pipeline com uma atividade de cópia

