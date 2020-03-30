---
title: Copiar dados da Fábrica de Dados do Azure para o Azure Data Explorer
description: Neste artigo, você aprende como ingerir dados (carregar) no Azure Data Explorer usando a ferramenta de cópia da Fábrica de Dados do Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300592"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados para o Azure Data Explorer usando a fábrica de dados do Azure 

O Azure Data Explorer é um serviço de análise de dados rápido, totalmente gerenciado. Ele oferece análise em tempo real em grandes volumes de dados que são transmitidos de muitas fontes, como aplicativos, sites e dispositivos IoT. Com o Azure Data Explorer, você pode explorar iterativamente dados e identificar padrões e anomalias para melhorar produtos, melhorar as experiências dos clientes, monitorar dispositivos e impulsionar as operações. Ele ajuda você a explorar novas perguntas e obter respostas em minutos. 

O Azure Data Factory é um serviço de integração de dados totalmente gerenciado, baseado em nuvem. Você pode usá-lo para preencher seu banco de dados do Azure Data Explorer com dados do seu sistema existente. Ele pode ajudá-lo a economizar tempo quando você está construindo soluções de análise.

Quando você carrega dados no Azure Data Explorer, a Data Factory fornece os seguintes benefícios:

* **Configuração fácil**: Obtenha um assistente intuitivo de cinco passos sem necessidade de roteiragem.
* **Suporte a armazenamento de dados rico**: Obtenha suporte integrado para um rico conjunto de armazenamentos de dados on-premises e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Seguro e compatível**: Os dados são transferidos pelo HTTPS ou Azure ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Alto desempenho**: A velocidade de carregamento de dados é de até 1 gigabyte por segundo (GBps) no Azure Data Explorer. Para obter mais informações, consulte [O desempenho da atividade do Copy](/azure/data-factory/copy-activity-performance).

Neste artigo, você usa a ferramenta Data Factory Copy Data para carregar dados do Amazon Simple Storage Service (S3) no Azure Data Explorer. Você pode seguir um processo semelhante para copiar dados de outros armazenamentos de dados, tais como:
* [Armazenamento de Blobs do Azure](/azure/data-factory/connector-azure-blob-storage)
* [Banco de dados SQL do Azure](/azure/data-factory/connector-azure-sql-database)
* [SQL Data Warehouse do Azure](/azure/data-factory/connector-azure-sql-data-warehouse)
* [BigQuery do Google](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Sistema de arquivos](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md).
* Uma fonte de dados.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Faça login no [portal Azure](https://ms.portal.azure.com).

1. No painel esquerdo, selecione **Criar uma** > fábrica de dados**de análise** > **de**recursos .

   ![Crie uma fábrica de dados no portal Azure](media/data-factory-load-data/create-adf.png)

1. No **painel de fábrica de dados Novo,** forneça valores para os campos na tabela a seguir:

   ![O painel "Nova fábrica de dados"](media/data-factory-load-data/my-new-data-factory.png)  

   | Configuração  | Valor para inserir  |
   |---|---|
   | **Nome** | Na caixa, digite um nome globalmente único para sua fábrica de dados. Se você receber um erro, *o \"nome\" de fábrica de dados LoadADXDemo não está disponível,* digite um nome diferente para a fábrica de dados. Para obter regras sobre a nomeação de artefatos da Fábrica de Dados, consulte [regras de nomeação da Fábrica de Dados](/azure/data-factory/naming-rules).|
   | **Assinatura** | Na lista de paradas, selecione a assinatura do Azure para criar a fábrica de dados. |
   | **Grupo de recursos** | Selecione **Criar novo**e, em seguida, digite o nome de um novo grupo de recursos. Se você já tiver um grupo de recursos, selecione **Usar existente**. |
   | **Versão** | Na lista de isto, selecione **V2**. |  
   | **Local** | Na lista de desporto, selecione o local da fábrica de dados. Somente os locais suportados são exibidos na lista. Os armazenamentos de dados que são usados pela fábrica de dados podem existir em outros locais ou regiões. |

1. Selecione **Criar**.

1. Para monitorar o processo de criação, selecione **Notificações** na barra de ferramentas. Depois de criar a fábrica de dados, selecione-a.
   
   O painel **da Fábrica** de Dados é aberto.

   ![O painel da Fábrica de Dados](media/data-factory-load-data/data-factory-home-page.png)

1. Para abrir a aplicação em um painel separado, selecione o **bloco Autor & Monitor.**

## <a name="load-data-into-azure-data-explorer"></a>Carregar dados no Azure Data Explorer

Você pode carregar dados de vários tipos de [armazenamentos](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) de dados no Azure Data Explorer. Este artigo discute como carregar dados do Amazon S3.

Você pode carregar seus dados de qualquer uma das seguintes maneiras:

* Na interface de usuário do Azure Data Factory, no painel esquerdo, selecione o ícone **Autor,** como mostrado na seção "Criar uma fábrica de dados" de Criar uma fábrica de [dados usando a interface do usuário da Fábrica de Dados do Azure](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* Na ferramenta Azure Data Factory Copy Data, como mostrado na [ferramenta Use the Copy Data para copiar dados](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Copiar dados do Amazon S3 (fonte)

1. No **painel Vamos começar,** abra a ferramenta Copiar dados selecionando **Dados de cópia**.

   ![O botão da ferramenta Copiar dados](media/data-factory-load-data/copy-data-tool-tile.png)

1. No painel **Propriedades,** na caixa **Nome da tarefa,** digite um nome e, em seguida, selecione **Next**.

    ![O painel Propriedades de dados de cópia](media/data-factory-load-data/copy-from-source.png)

1. No painel do **armazenamento de dados** Origem, selecione Criar nova **conexão**.

    ![O painel Copiar dados "Fonte de dados"](media/data-factory-load-data/source-create-connection.png)

1. Selecione **Amazon S3**e selecione **Continuar**.

    ![O painel do Novo Serviço Vinculado](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. No **painel Do Novo Serviço Vinculado (Amazon S3),** faça o seguinte:

    ![Especificar serviço vinculado ao Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Na caixa **Nome,** digite o nome do seu novo serviço vinculado.

    b. Na lista de paradas de tempo de **execução de integração,** selecione o valor.

    c. Na caixa **ID da chave de acesso,** digite o valor.
    
    > [!NOTE]
    > No Amazon S3, para localizar sua chave de acesso, selecione seu nome de usuário da Amazon na barra de navegação e selecione **Minhas Credenciais de Segurança**.
    
    d. Na caixa **Chave de Acesso Secreto,** digite um valor.

    e. Para testar a conexão de serviço vinculada criada, selecione **'Conexão de teste'.**

    f. Selecione **Concluir**.
    
      O painel **do armazenamento de dados** Source exibe sua nova conexão AmazonS31. 

1. Selecione **Avançar**.

   ![Conexão criada pelo armazenamento de dados de origem](media/data-factory-load-data/source-data-store-created-connection.png)

1. No **Escolher o arquivo de entrada ou** painel de pasta, faça o seguinte:

    a. Navegue até o arquivo ou pasta que deseja copiar e selecione-o.

    b. Selecione o comportamento de cópia que deseja. Certifique-se de que a caixa de seleção **de cópia binária** esteja limpa.

    c. Selecione **Avançar**.

    ![Escolha a pasta ou arquivo de entrada](media/data-factory-load-data/source-choose-input-file.png)

1. No **painel Configurações** do formato Arquivo, selecione as configurações relevantes para o seu arquivo. e, em seguida, selecione **Next**.

   ![O painel "Configurações de formato de arquivo"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiar dados no Azure Data Explorer (destino)

O novo serviço vinculado ao Azure Data Explorer foi criado para copiar os dados na tabela de destino do Azure Data Explorer (sink) especificada nesta seção.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Crie o serviço vinculado ao Azure Data Explorer

Para criar o serviço vinculado ao Azure Data Explorer, faça o seguinte;

1. Para usar uma conexão de armazenamento de dados existente ou especificar um novo armazenamento de dados, no painel **do armazenamento de dados Destino,** selecione Criar nova **conexão**.

    ![Painel de armazenamento de dados de destino](media/data-factory-load-data/destination-create-connection.png)

1. No **painel Novo Serviço Vinculado,** selecione **O Azure Data Explorer**e selecione **Continuar**.

    ![O novo painel de serviços vinculado](media/data-factory-load-data/adx-select-new-linked-service.png)

1. No painel **Novo Serviço Vinculado (Azure Data Explorer),** faça o seguinte:

    ![O painel do Novo Serviço Vinculado do Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   a. Na caixa **Nome,** digite um nome para o serviço vinculado ao Azure Data Explorer.

   b. No **método de seleção de**contas, faça um dos seguintes: 

    * Selecione **A assinatura do Azure** e, em seguida, nas listas de saque, selecione sua **assinatura do Azure** e seu **Cluster**. 

        > [!NOTE]
        > O controle de saque do **Cluster** lista apenas clusters associados à sua assinatura.

    * Selecione **Enter manualmente**e, em seguida, digite seu **ponto final**.

   c. Na caixa do **inquilino,** digite o nome do inquilino.

   d. Na caixa **de ID principal do Serviço,** digite o ID principal do serviço.

   e. Selecione **a tecla principal do Serviço** e, em seguida, na caixa de **tecla principal do Serviço,** digite o valor da chave.

   f. Na lista de desoneração do banco de **dados,** selecione o nome do banco de dados. Alternativamente, selecione a caixa **Editar** seleção e, em seguida, digite o nome do banco de dados.

   g. Para testar a conexão de serviço vinculada criada, selecione **'Conexão de teste'.** Se você puder se conectar ao seu serviço vinculado, o painel exibirá uma marca de seleção verde e uma mensagem **de sucesso conexão.**

   h. Selecione **Concluir** para concluir a criação de serviço vinculado.

    > [!NOTE]
    > O principal de serviço é usado pela Fábrica de Dados Do Azure para acessar o serviço Azure Data Explorer. Para criar um principal de serviço, vá [criar um diretor de serviço do Azure Active Directory (Azure AD).](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Não use o método Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configure a conexão de dados do Azure Data Explorer

Depois de criar a conexão de serviço vinculado, o painel **do armazenamento de dados Destino** é aberto e a conexão que você criou está disponível para uso. Para configurar a conexão, faça o seguinte;

1. Selecione **Avançar**.

    ![O painel "Destination Data store" do Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. No painel **de mapeamento tabela,** defina o nome da tabela de destino e selecione **Next**.

    ![O painel "Mapeamento de tabela" do conjunto de dados de destino](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. No painel **de mapeamento da Coluna,** ocorrem os seguintes mapeamentos:

    a. O primeiro mapeamento é realizado pela Azure Data Factory de acordo com o mapeamento do [esquema da Fábrica de Dados do Azure](/azure/data-factory/copy-activity-schema-and-type-mapping). Faça o seguinte:

    * Defina os **mapeamentos** da coluna para a tabela de destino da Fábrica de Dados do Azure. O mapeamento padrão é exibido da origem para a tabela de destino da Fábrica de Dados do Azure.

    * Cancele a seleção das colunas que você não precisa para definir o mapeamento da coluna.

    b. O segundo mapeamento ocorre quando esses dados tabulares são ingeridos no Azure Data Explorer. O mapeamento é realizado de acordo com [as regras de mapeamento do CSV.](/azure/kusto/management/mappings#csv-mapping) Mesmo que os dados de origem não sejam no formato CSV, a Fábrica de Dados do Azure converte os dados em um formato tabular. Portanto, o mapeamento do CSV é o único mapeamento relevante nesta fase. Faça o seguinte:

    * (Opcional) Em **propriedades de sumidouro do Azure Data Explorer (Kusto),** adicione o nome de mapeamento do **Ingestion** relevante para que o mapeamento da coluna possa ser usado.

    * Se o nome de **mapeamento da Ingestion** não for especificado, a ordem de mapeamento *por nome* definida na seção **Mapeamentos** da Coluna será usada. Se o mapeamento *de nomes* falhar, o Azure Data Explorer tentará ingerir os dados em uma ordem de posição *por coluna* (ou seja, ele mapeia por posição como padrão).

    * Selecione **Avançar**.

    ![O painel "Mapeamento de colunas" do conjunto de dados de destino](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. No painel **Configurações,** faça o seguinte:

    a. Em **Configurações de tolerância a falhas,** digite as configurações relevantes.

    b. Em **Configurações de desempenho,** **a ativação** de estágio não se aplica e as **configurações avançadas** incluem considerações de custo. Se você não tiver requisitos específicos, deixe essas configurações como estão.

    c. Selecione **Avançar**.

    ![O painel de "Configurações" de dados de cópia](media/data-factory-load-data/copy-data-settings.png)

1. No **painel Resumo,** revise as configurações e selecione **Next**.

    ![O painel "Resumo" de dados de cópia](media/data-factory-load-data/copy-data-summary.png)

1. No painel **completo de Implantação,** faça o seguinte:

    a. Para mudar para a guia **Monitorar** e visualizar o status do pipeline (ou seja, progresso, erros e fluxo de dados), selecione **Monitor**.

    b. Para editar serviços vinculados, conjuntos de dados e pipelines, selecione **Editar pipeline**.

    c. Selecione **Concluir** para concluir a tarefa de dados de cópia.

    ![O painel "Implantação completa"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [conector Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) na Fábrica de Dados Do Azure.
* Saiba mais sobre a edição de serviços vinculados, conjuntos de dados e pipelines na [ui da Fábrica de Dados](/azure/data-factory/quickstart-create-data-factory-portal).
* Saiba mais sobre [as consultas do Azure Data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.
