---
title: Conecte-se aos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar datastores para se conectar com segurança aos serviços de armazenamento do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ca892b5f360f523ee2b5ff875dfb0707136a5ab5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383436"
---
# <a name="connect-to-azure-storage-services"></a>Conecte-se aos serviços de armazenamento do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como se conectar aos serviços de armazenamento do Azure através de datastores de aprendizado de máquina do Azure. Os datastores armazenam informações de conexão, como seu ID de assinatura e autorização de token em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho, para que você possa acessar seu armazenamento com segurança sem ter que coclá-los em seus scripts.

Você pode criar datastores a partir dessas soluções de [armazenamento Do Zure.](#matrix) Para soluções de armazenamento sem suporte e para economizar o custo de saída de dados durante experimentos de aprendizado de máquina, recomendamos que você [mova seus dados](#move) para soluções de armazenamento Azure suportadas. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:
- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento Azure com um [contêiner de blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou [compartilhamento de arquivos Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao estúdio [Azure Machine Learning](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um espaço de trabalho de Aprendizado de Máquina do Azure](how-to-manage-workspace.md) ou use um existente através do Python SDK. Importe `Workspace` `Datastore` a classe e carregue suas `config.json` informações de `from_config()`assinatura do arquivo usando a função . Isso procura o arquivo JSON no diretório atual por padrão, mas você também pode `from_config(path="your/file/path")`especificar um parâmetro de caminho para apontar para o arquivo usando .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviço de armazenamento de dados suportados

Atualmente, os datastores suportam armazenar informações de conexão aos serviços de armazenamento listados na matriz a seguir.

| Tipo&nbsp;de armazenamento | Tipo&nbsp;de autenticação | [Estúdio de&nbsp;&nbsp;Aprendizado de Máquina Azure](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Cli de&nbsp;&nbsp;aprendizagem de máquina do Azure](reference-azure-machine-learning-cli.md) | [API&nbsp;de&nbsp;&nbsp; descanso de aprendizado de máquina do Azure](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Armazenamento&nbsp;azure&nbsp;Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Compartilhamento de&nbsp;&nbsp;arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Entidade de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entidade de serviço| ✓ | ✓ | ✓ |✓
[Banco de&nbsp;dados&nbsp;Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticação SQL <br>Entidade de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Autenticação SQL| ✓ | ✓ | ✓ |✓
[Banco de&nbsp;&nbsp;dados&nbsp;Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticação SQL|  | ✓* | ✓* |✓*
[Sistema de&nbsp;&nbsp;arquivos Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓ ** |✓** 

*O MySQL só é suportado para o pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks é suportado apenas para pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientação de armazenamento

Recomendamos a criação de um datastore para [um contêiner Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Tanto o armazenamento padrão quanto o premium estão disponíveis para bolhas. Embora o armazenamento premium seja mais caro, suas velocidades de throughput mais rápidas podem melhorar a velocidade de suas corridas de treinamento, especialmente se você treinar contra um grande conjunto de dados. Para obter informações sobre o custo das contas de armazenamento, consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[O Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) é construído em cima do armazenamento Azure Blob e projetado para análise de big data corporativo. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados.

Quando você cria um espaço de trabalho, um contêiner de blob do Azure e um compartilhamento de arquivos Do Zure são automaticamente registrados no espaço de trabalho. Eles são `workspaceblobstore` nomeados e, `workspacefilestore`respectivamente. `workspaceblobstore`é usado para armazenar artefatos de espaço de trabalho e seus registros de experimentos de aprendizado de máquina. `workspacefilestore`é usado para armazenar notebooks e scripts R autorizados via [instância de computação](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). O `workspaceblobstore` contêiner é definido como o armazenamento de dados padrão.

> [!IMPORTANT]
> O azure Machine Learning designer (preview) criará um datastore chamado **azureml_globaldatasets** automaticamente quando você abrir uma amostra na página inicial do designer. Este datastore contém apenas conjuntos de dados de amostra. Por **favor, não** use este armazenamento de dados para qualquer acesso confidencial de dados.
> ![Datastore criado automaticamente para conjuntos de dados de exemplo de designer](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar datastores

Quando você registra uma solução de armazenamento DoZure como um datastore, você cria e registra automaticamente esse datastore em um espaço de trabalho específico. Você pode criar e registrar datastores em um espaço de trabalho usando o estúdio Python SDK ou Azure Machine Learning.

>[!IMPORTANT]
> Como parte do processo inicial de criação e registro do datastore, o Azure Machine Learning valida que o serviço de armazenamento subjacente existe e que o usuário forneceu principal (nome de usuário, principal de serviço ou token SAS) tem acesso a esse armazenamento. Para os datastores Azure Data Lake Storage Gen 1 e 2, no [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) entanto, essa validação acontece mais tarde, quando os métodos de acesso a dados gostam ou são chamados. 
<br><br>
Após a criação do datastore, essa validação é realizada apenas para métodos que requerem acesso ao contêiner de armazenamento subjacente, **não** cada vez que objetos de armazenamento de dados são recuperados. Por exemplo, a validação acontece se você quiser baixar arquivos do seu datastore; mas se você quiser apenas alterar seu datastore padrão, então a validação não acontecerá.

### <a name="python-sdk"></a>SDK do Python

Todos os métodos de [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) registro estão `register_azure_*`na classe e possuem o formulário .

Você pode encontrar as informações necessárias para preencher o `register()` método no portal [Azure](https://portal.azure.com).
Selecione **Contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que deseja registrar. A **página Visão geral** fornece informações como o nome da conta, contêiner e nome do compartilhamento de arquivos. 

* Para itens de autenticação, como a chave da conta ou o token SAS, vá para **as teclas de acesso** no painel **Configurações.** 

* Para atender itens principais do serviço, como, ID do inquilino e ID do cliente, vá para seus **registros do Aplicativo** e selecione qual aplicativo você deseja usar. Sua página **de visão geral** correspondente conterá esses itens.

> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, somente a criação de armazenamentos de dados Blob, File share, ADLS Gen 1 e ADLS Gen 2 **através do SDK** é suportada. Para conceder seu acesso ao espaço de trabalho `grant_workspace_access` `True`à sua conta de armazenamento, defina o parâmetro para .

Os exemplos a seguir mostram como registrar um contêiner de blob do Azure, um compartilhamento de arquivos Azure e o Azure Data Lake Storage Generation 2 como um data store. Para outros serviços de armazenamento, consulte a [documentação de referência para os métodos `register_azure_*` aplicáveis.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Contêiner de blob

Para registrar um contêiner azure blob [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)como um datastore, use .

O código a seguir `blob_datastore_name` cria e `ws` registra o datastore no espaço de trabalho. Este datastore acessa o `my-container-name` `my-account-name` contêiner blob na conta de armazenamento, usando a chave de acesso da conta fornecida.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Compartilhamento de arquivo

Para registrar um compartilhamento de arquivos Azure como um datastore, use [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

O código a seguir `file_datastore_name` cria e `ws` registra o datastore no espaço de trabalho. Esse datastore acessa `my-fileshare-name` o `my-account-name` compartilhamento de arquivos na conta de armazenamento, usando a chave de acesso da conta fornecida.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para um armazenamento de dados Azure Data Lake Storage Generation 2 (ADLS Gen 2), use [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um armazenamento de dados credencial conectado a um armazenamento Azure DataLake Gen 2 com [permissões principais do serviço.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Para utilizar seu principal de serviço, você precisa [registrar sua solicitação](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder ao diretor do serviço o acesso certo aos dados. Saiba mais sobre o [controle de acesso configurado para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O código a seguir `adlsgen2_datastore_name` cria e `ws` registra o datastore no espaço de trabalho. Esse datastore acessa `test` o `account_name` sistema de arquivos na conta de armazenamento, usando as credenciais principais do serviço fornecido.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Crie um novo datastore em alguns passos no estúdio Azure Machine Learning:

> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, somente a criação de datastores [através do SDK](#python-sdk) é suportada. 

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).
1. Selecione **Datastores** no painel esquerdo em **Gerenciar**.
1. Selecione **+ Novo datastore**.
1. Preencha o formulário para um novo datastore. O formulário se atualiza de forma inteligente com base em suas seleções para o tipo de armazenamento e tipo de autenticação do Azure.
  
Você pode encontrar as informações necessárias para preencher o formulário no [portal Azure](https://portal.azure.com). Selecione **Contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que deseja registrar. A **página Visão geral** fornece informações como o nome da conta, contêiner e nome do compartilhamento de arquivos. 

* Para itens de autenticação, como a chave da conta ou o token SAS, vá para **as teclas de acesso** no painel **Configurações.** 

* Para atender itens principais do serviço, como, ID do inquilino e ID do cliente, vá para seus **registros do Aplicativo** e selecione qual aplicativo você deseja usar. Sua página **de visão geral** correspondente conterá esses itens. 

O exemplo a seguir demonstra como o formulário se parece quando você cria um armazenamento de dados azure blob: 
    
![Formulário para um novo datastore](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obtenha armazenamentos de dados do seu espaço de trabalho

Para obter um datastore específico registrado no espaço [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) de trabalho `Datastore` atual, use o método estático na classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de datastores registrados com um [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) determinado espaço de trabalho, você pode usar a propriedade em um objeto de espaço de trabalho:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Para obter o armazenamento de dados padrão do espaço de trabalho, use esta linha:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Carregar e baixar dados

Os [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos descritos nos exemplos a seguir são específicos e operam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)

### <a name="upload"></a>Carregar

Faça upload de um diretório ou arquivos individuais para o armazenamento de dados usando o Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O `target_path` parâmetro especifica o local no compartilhamento de arquivos (ou recipiente blob) para carregar. Ele é `None`padrão para , então os dados são carregados para raiz. Se `overwrite=True`, quaisquer dados `target_path` existentes em forem substituídos.

Você também pode carregar uma lista de arquivos `upload_files()` individuais para o datastore através do método.

### <a name="download"></a>Baixar

Baixe dados de um datastore para o sistema de arquivos local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O `target_path` parâmetro é a localização do diretório local para baixar os dados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de blob) para baixar, forneça esse caminho para `prefix`. Se `prefix` `None`for, todo o conteúdo do seu compartilhamento de arquivos (ou recipiente blob) será baixado.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Acesse seus dados durante o treinamento

Para interagir com dados em seus datastores ou para empacotar seus dados em um objeto consumível para tarefas de aprendizado de máquina, como treinamento, [crie um conjunto de dados azure Machine Learning](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabulares em pandas ou Spark DataFrame. Os conjuntos de dados também fornecem a capacidade de baixar ou montar arquivos de qualquer formato do armazenamento Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. [Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Acessando o código fonte durante o treinamento

O armazenamento Do Azure Blob tem velocidades de throughput mais altas do que um compartilhamento de arquivos do Azure e será dimensionado para um grande número de empregos iniciados em paralelo. Por essa razão, recomendamos configurar suas corridas para usar o armazenamento Blob para transferir arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual blob datastore para usar para transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Dados de acesso durante a pontuação

O Azure Machine Learning fornece várias maneiras de usar seus modelos para marcar. Alguns desses métodos não fornecem acesso a datastores. Use a tabela a seguir para entender quais métodos permitem acessar datastores durante a pontuação:

| Método | Acesso ao datastore | Descrição |
| ----- | :-----: | ----- |
| [Previsão em lote](how-to-use-parallel-run-step.md) | ✔ | Fazer previsões sobre grandes quantidades de dados assincronamente. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos como um serviço web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos para dispositivos IoT Edge. |

Para situações em que o SDK não fornece acesso a datastores, você pode ser capaz de criar código personalizado usando o SDK Azure relevante para acessar os dados. Por exemplo, o [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que você pode usar para acessar dados armazenados em blobs ou arquivos.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento Azure suportadas

O Azure Machine Learning suporta o acesso a dados do armazenamento Azure Blob, Arquivos Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. Se você estiver usando armazenamento sem suporte, recomendamos que você mova seus dados para soluções de armazenamento Azure suportadas usando [a Fábrica de Dados Do Azure e essas etapas](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Mover dados para armazenamento suportado pode ajudá-lo a economizar custos de saída de dados durante experimentos de aprendizado de máquina. 

A Fábrica de Dados Do Azure fornece transferência de dados eficiente e resiliente com mais de 80 conectores pré-construídos sem custo adicional. Esses conectores incluem serviços de dados do Azure, fontes de dados locais, Amazon S3 e Redshift e Google BigQuery.

## <a name="next-steps"></a>Próximas etapas

* [Crie um conjunto de dados de aprendizado de máquina do Azure](how-to-create-register-datasets.md)
* [Treinar um modelo](how-to-train-ml-models.md)
* [Implantar um modelo](how-to-deploy-and-where.md)
