---
title: Conectar aos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os armazenamentos de dados para se conectar com segurança aos serviços de armazenamento do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 904738d73aaa0580773a085c70cd74f4240fc4b7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773929"
---
# <a name="connect-to-azure-storage-services"></a>Conectar aos serviços de armazenamento do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como se conectar aos serviços de armazenamento do Azure por meio dos armazenamentos de dados do Azure Machine Learning. Os armazenamentos de dados armazenam informações de conexão, como sua ID de assinatura e a autorização de token em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associadas ao espaço de trabalho, para que você possa acessar o armazenamento com segurança sem ter que embuti-los em seus scripts. Para entender onde os armazenamentos de dados se encaixam no fluxo de trabalho de acesso a dados geral do Azure Machine Learning, confira o artigo [Acessar dados com segurança](concept-data.md#data-workflow).

Você pode criar armazenamentos de dados [dessas soluções de armazenamento do Azure](#matrix). Para soluções de armazenamento sem suporte e para economizar o custo de saída de dados durante experimentos de Machine Learning, recomendamos que você [mova seus dados](#move) para soluções de armazenamento do Azure com suporte. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:
- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento do Azure com um [contêiner de blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou um [compartilhamento de arquivo do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um workspace do Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python. Importe as classes `Workspace` e `Datastore` e carregue as informações de assinatura do arquivo `config.json` usando a função `from_config()`. Isso procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviço de armazenamento de dados com suporte

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir.

| Tipo de&nbsp;armazenamento | Tipo de&nbsp;autenticação | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; – SDK do Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI do Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [API REST do &nbsp;Azure&nbsp;Machine&nbsp;Learning](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Armazenamento de&nbsp;Blobs&nbsp;do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Compartilhamento de&nbsp;arquivo do &nbsp;Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Entidade de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entidade de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;SQL&nbsp;Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticação SQL <br>Entidade de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Autenticação SQL| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Database&nbsp;para&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticação SQL|  | ✓* | ✓* |✓*
[Sistema de&nbsp;arquivos&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓** |✓** 

*Só há suporte ao MySQL no pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
*Só há suporte ao Databricks no pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientação de armazenamento

Recomendamos criar um armazenamento de dados para um [contêiner de blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Tanto o armazenamento Standard quanto o Premium estão disponíveis para blobs. Embora o armazenamento Premium seja mais caro, suas velocidades de taxa de transferência mais rápidas podem melhorar a velocidade de seus treinamentos, especialmente se você treina um grande conjunto de dados. Para obter informações sobre o custo de contas de armazenamento, confira a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

O [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) é criado sobre o Armazenamento de Blobs do Azure e projetado para análise corporativa de Big Data. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados.

Quando você cria um espaço de trabalho, um contêiner de blob do Azure e um compartilhamento de arquivo do Azure são automaticamente registrados no workspace. Seus nomes são `workspaceblobstore` e `workspacefilestore`, respectivamente. `workspaceblobstore` é usado para armazenar artefatos de workspace e seus logs de experimento do Machine Learning. `workspacefilestore` é usado para armazenar notebooks e scripts R autorizados via [instância de computação](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). O contêiner de `workspaceblobstore` é definido como o armazenamento de dados padrão.

> [!IMPORTANT]
> O designer do Azure Machine Learning (versão prévia) criará um armazenamento de dados chamado **azureml_globaldatasets** automaticamente quando você abrir um exemplo na página inicial do designer. Esse armazenamento de dados contém apenas conjuntos de dados de amostra. **Não** use esse armazenamento de dados para acesso a dados confidenciais.
> ![Armazenamento de dados criado automaticamente para conjuntos de dados de amostra do designer](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar armazenamentos de dados

Quando registra uma solução de armazenamento do Azure como um armazenamento de dados, você cria e registra automaticamente esse armazenamento de dados em um workspace específico. Você pode criar e registrar armazenamentos de dados em um workspace usando o [SDK do Python](#python-sdk) ou o [Azure Machine Learning Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> Como parte do processo inicial de criação e registro do armazenamento de dados, o Azure Machine Learning valida que o serviço de armazenamento subjacente existe e que a entidade de segurança fornecida pelo usuário (nome de usuário, entidade de serviço ou token SAS) tem acesso a esse armazenamento. No entanto, para os armazenamentos de dados do Azure Data Lake Storage Gen 1 e 2, essa validação ocorre mais tarde, quando os métodos de acesso a dados como [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) ou [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) são chamados. 
<br><br>
Após a criação do armazenamento de dados, essa validação é executada somente em métodos que exijam acesso ao contêiner de armazenamento subjacente, **não** cada vez que os objetos de armazenamento de data são recuperados. Por exemplo, a validação ocorrerá se você quiser baixar arquivos do seu armazenamento de dados; mas se você quiser apenas alterar o armazenamento de dados padrão, a validação não ocorrerá.

### <a name="python-sdk"></a>SDK do Python

Todos os métodos de registro estão na classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e têm o formato `register_azure_*`.
> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, haverá suporte apenas à criação de armazenamentos de dados **por meio do SDK**.

Você pode encontrar as informações necessárias para preencher o método `register_azure_*()` no [portal do Azure](https://portal.azure.com).

* Se você planeja usar uma chave de conta ou um token SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que deseja registrar. 
  * A página **Visão Geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivo. 
      1. Para obter as chaves de conta, vá para **Chaves de acesso** no painel **Configurações**. 
      1. Para obter os tokens SAS, vá para **Assinaturas de acesso compartilhado** no painel **Configurações**.

* Se você planeja usar uma entidade de serviço para autenticação, vá para **Registros de aplicativo** e selecione qual aplicativo deseja usar. 
    * A página **Visão Geral** correspondente conterá informações necessárias, como ID do locatário e iD do cliente.

Os exemplos a seguir mostram como registrar um contêiner de blob do Azure, um compartilhamento de arquivo do Azure e o Azure Data Lake Storage Generation 2 como um armazenamento de dados. Os parâmetros fornecidos nesses exemplos são os **parâmetros necessários** para criar e registrar um armazenamento de dados. 

Para criar armazenamentos de dados que serão usados por outros serviços de armazenamento e ver parâmetros opcionais para esses métodos, confira a [documentação de referência dos métodos `register_azure_*` aplicáveis](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Contêiner de blob

Para registrar um contêiner de blob do Azure como armazenamento de dados, use [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

O código a seguir cria e registra o armazenamento de dados `blob_datastore_name` no workspace `ws`. Esse armazenamento de dados acessa o contêiner de blob `my-container-name` na conta de armazenamento `my-account-name` com a chave de acesso da conta fornecida.

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
Se o seu contêiner de blob estiver na rede virtual, inclua o parâmetro `skip_validation=True` no método [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-). 

#### <a name="file-share"></a>Compartilhamento de arquivo

Para registrar um compartilhamento de arquivo do Azure como um armazenamento de dados, use [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

O código a seguir cria e registra o armazenamento de dados `file_datastore_name` no workspace `ws`. Esse armazenamento de dados acessa o compartilhamento de arquivo `my-fileshare-name` na conta de armazenamento `my-account-name` com a chave de acesso da conta fornecida.

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
Se o seu compartilhamento de arquivo estiver na rede virtual, inclua o parâmetro `skip_validation=True` no método [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para um armazenamento de dados de Azure Data Lake Storage Generation 2 (ADLS Gen 2), use [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um armazenamento de dados de credenciais conectado a um armazenamento do Azure Data Lake Gen 2 com [permissões de entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Para utilizar sua entidade de serviço, você precisará [registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder à entidade de serviço acesso ao *Leitor de Dados do Blob de Armazenamento*. Saiba mais sobre [configuração de controle de acesso para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Para utilizar sua entidade de serviço, você precisará [registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder à entidade de serviço o acesso de dados correto. Saiba mais sobre [configuração de controle de acesso para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O código a seguir cria e registra o armazenamento de dados `adlsgen2_datastore_name` no workspace `ws`. Esse armazenamento de dados acessa o sistema de arquivos `test` na conta de armazenamento `account_name` com as credenciais de entidade de serviço fornecidas.

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

Crie um novo armazenamento de dados em algumas etapas no Azure Machine Learning Studio:

> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, haverá suporte apenas à criação de armazenamentos de dados [por meio do SDK](#python-sdk). 

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).
1. Selecione **Armazenamentos de dados** no painel esquerdo em **Gerenciar**.
1. Selecione **+ Novo armazenamento de dados**.
1. Preencha o formulário de um novo armazenamento de dados. O formulário se atualiza de forma inteligente com base nas seleções de tipo de armazenamento e tipo de autenticação do Azure.
  
Você pode encontrar as informações necessárias para preencher o formulário no [portal do Azure](https://portal.azure.com). Selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. A página **Visão Geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivo. 

* Para obter itens de autenticação, como chave de conta ou token SAS, vá para **Chaves de acesso** no painel **Configurações**. 

* Para obter itens de entidade de serviço, como ID do locatário e ID do cliente, vá para **Registros de aplicativo** e selecione qual aplicativo você deseja usar. A página **Visão Geral** correspondente conterá esses itens. 

O seguinte exemplo demonstra a aparência do formulário quando você cria um armazenamento de dados de Blob do Azure: 
    
![Formulário de um novo armazenamento de dados](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obter armazenamentos de dados do seu workspace

Para obter um armazenamento de dados específico registrado no workspace atual, use o método estático [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) na classe `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de armazenamentos de dados registrados em determinado workspace, você pode usar a propriedade [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) em um objeto de workspace:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Para obter acesso ao repositório de dados padrão do workspace, use esta linha:

```Python
datastore = ws.get_default_datastore()
```
Você também pode alterar o armazenamento de dados padrão com o código a seguir. Essa capacidade só tem suporte por meio do SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Carregar e baixar dados

Os métodos [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) e [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) descritos nos exemplos a seguir são específicos das classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) e operam de forma idêntica.

> [!NOTE]
> No momento, não há suporte a upload em armazenamentos de dados do AzureDataLakeGen2.

### <a name="upload"></a>Carregar

Carregue um diretório ou arquivos individuais para o armazenamento de dados usando o SDK do Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O parâmetro `target_path` especifica o local no compartilhamento de arquivos (ou contêiner de blob) para o upload. Ele é padronizado como `None`, de modo que os dados são carregados para a raiz. Se `overwrite=True`, todos os dados existentes em `target_path` serão substituídos.

Você também pode carregar uma lista de arquivos individuais para o armazenamento de dados por meio do método `upload_files()`.

### <a name="download"></a>Baixar

Baixe os dados de um armazenamento de dados para o sistema de arquivos local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O parâmetro `target_path` é a localização do diretório local no qual os dados serão baixados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de blob) para baixar, forneça esse caminho para `prefix`. Se `prefix` for `None`, todo o conteúdo do compartilhamento de arquivo (ou contêiner de blob) será baixado.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Acessar seus dados durante o treinamento

Para interagir com os dados em seus armazenamentos de dados ou empacotar os dados em um objeto de consumo para tarefas de aprendizado de máquina, como um treinamento, [crie um conjunto de dados do Azure Machine Learning](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabulares em um dataframe do Pandas ou do Spark. Os conjuntos de dados também permitem baixar ou montar arquivos em qualquer formato do Armazenamento de Blobs do Azure, dos Arquivos do Azure, do Azure Data Lake Storage Gen1, do Azure Data Lake Storage Gen2, do Banco de Dados SQL do Azure e do Banco de Dados do Azure para PostgreSQL. [Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Acessando o código-fonte durante o treinamento

O Armazenamento de Blobs do Azure tem velocidades de taxa de transferência mais altas que um compartilhamento de arquivo do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo. Por esse motivo, recomendamos configurar suas execuções para usar o Armazenamento de Blobs na transferência de arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual armazenamento de dados de blob usar em transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Acessar dados durante a pontuação

O Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não dão acesso a armazenamentos de dados. Use a tabela abaixo para entender quais métodos permitem que você acesse os armazenamentos de dados durante a pontuação:

| Método | Acesso ao armazenamento de dados | Descrição |
| ----- | :-----: | ----- |
| [Previsão em lote](how-to-use-parallel-run-step.md) | ✔ | Fazer previsões sobre grandes quantidades de dados assincronamente. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos como serviço Web. |
| [Módulo do Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos em dispositivos do IoT Edge. |

Para situações em que o SDK não fornece acesso aos armazenamentos de dados, você poderá criar código personalizado usando o SDK do Azure relevante para acessar os dados. Por exemplo, o [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que você pode usar para acessar dados armazenados em blobs ou arquivos.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento do Azure com suporte

O Azure Machine Learning dá suporte ao acesso de dados de Armazenamento de Blobs do Azure, Arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Banco de Dados SQL do Azure e Banco de Dados do Azure para PostgreSQL. Se você estiver usando um armazenamento sem suporte, recomendamos que mova seus dados para soluções de armazenamento do Azure com suporte usando o [Azure Data Factory e estas etapas](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). A mudança de dados para o armazenamento com suporte pode ajudá-lo a economizar custos de saída de dados durante experimentos de aprendizado de máquina. 

O Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores predefinidos sem custo adicional. Esses conectores incluem serviços de dados do Azure, fontes de dados locais, o Amazon S3, o Redshift e o Google BigQuery.

## <a name="next-steps"></a>Próximas etapas

* [Criar um conjunto de dados do Azure Machine Learning](how-to-create-register-datasets.md)
* [Treinar um modelo](how-to-train-ml-models.md)
* [Implantar um modelo](how-to-deploy-and-where.md)
