---
title: Conectar-se aos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os repositórios de armazenamento para se conectar com segurança aos serviços de armazenamento do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: f5a7605a1fa68c3a600c77ded762722990d7a514
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231196"
---
# <a name="connect-to-azure-storage-services"></a>Conectar-se aos serviços de armazenamento do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como se conectar aos serviços de armazenamento do Azure por meio de Azure Machine Learning repositórios de armazenamento. Os armazenamentos de dados armazenam informações de conexão, como sua ID de assinatura e autorização de token em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho, para que você possa acessar o armazenamento com segurança sem ter que embutir código neles em seus scripts. Para entender onde os armazenamentos de dados se encaixam no fluxo de trabalho de acesso de data de Azure Machine Learning geral, consulte o artigo [dados de acesso seguro](concept-data.md#data-workflow) .

Você pode criar repositórios de armazenamento a partir [dessas soluções de armazenamento do Azure](#matrix). Para soluções de armazenamento sem suporte e para salvar o custo de egresso de dados durante experimentos de Machine Learning, recomendamos que você [mova seus dados](#move) para soluções de armazenamento do Azure com suporte. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:
- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento do Azure com um [contêiner de blob do](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) Azure ou [compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python. Importe a `Workspace` classe `Datastore` e e carregue as informações de assinatura do arquivo `config.json` usando a função. `from_config()` Isso procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviço de armazenamento de dados com suporte

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir.

| Tipo&nbsp;de armazenamento | Tipo&nbsp;de autenticação | [Estúdio&nbsp;de&nbsp;aprendizado de máquina do Azure](https://ml.azure.com/) | [SDK&nbsp;do&nbsp;Python&nbsp; do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI&nbsp;do&nbsp;Azure Machine Learning](reference-azure-machine-learning-cli.md) | [API&nbsp;REST&nbsp;do&nbsp; Azure Machine Learning](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Armazenamento&nbsp;de&nbsp;BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Compartilhamento&nbsp;de&nbsp;arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Armazenamento&nbsp;de&nbsp;data Lake do&nbsp;Azure Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Entidade de serviço| ✓ | ✓ | ✓ |✓
[Armazenamento&nbsp;de&nbsp;data Lake do&nbsp;Azure Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entidade de serviço| ✓ | ✓ | ✓ |✓
[Banco&nbsp;de&nbsp;dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticação SQL <br>Entidade de serviço| ✓ | ✓ | ✓ |✓
[PostgreSQL&nbsp;do Azure](https://docs.microsoft.com/azure/postgresql/overview) | Autenticação SQL| ✓ | ✓ | ✓ |✓
[Banco&nbsp;de&nbsp;dados&nbsp;do Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticação SQL|  | ✓* | ✓* |✓*
[Sistema de&nbsp;arquivos&nbsp;do databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓ ** |✓** 

* Há suporte para o MySQL somente no pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
* * Só há suporte para databricks no pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientação de armazenamento

É recomendável criar um repositório de armazenamento para um [contêiner de blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Tanto o armazenamento Standard quanto o Premium estão disponíveis para BLOBs. Embora o armazenamento Premium seja mais caro, suas velocidades de taxa de transferência mais rápidas podem melhorar a velocidade de suas execuções de treinamento, especialmente se você treinar um grande conjunto de grandes. Para obter informações sobre o custo de contas de armazenamento, consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

O [Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) é criado sobre o armazenamento de BLOBs do Azure e projetado para Enterprise Big data Analytics. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados.

Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são automaticamente registrados no espaço de trabalho. Eles são `workspaceblobstore` nomeados `workspacefilestore`e, respectivamente. `workspaceblobstore`é usado para armazenar artefatos de espaço de trabalho e seus logs de experimento do Machine Learning. `workspacefilestore`é usado para armazenar blocos de anotações e scripts de R autorizados por meio da [instância de computação](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). O `workspaceblobstore` contêiner é definido como o repositório de armazenamento padrão.

> [!IMPORTANT]
> O designer de Azure Machine Learning (versão prévia) criará um repositório de armazenamento chamado **azureml_globaldatasets** automaticamente quando você abrir um exemplo na página inicial do designer. Esse datastore contém apenas conjuntos de exemplo de conjunto de exemplos. **Não** Use esse armazenamento de dados para qualquer acesso confidencial a dados.
> ![Conjunto de exemplos de armazenamento de datastore para designer criado automaticamente](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar repositórios de armazenamento

Ao registrar uma solução de armazenamento do Azure como um repositório de armazenamento, você cria e registra automaticamente esse repositório de armazenamento em um espaço de trabalho específico. Você pode criar e registrar repositórios de armazenamento em um espaço de trabalho usando o [SDK do Python](#python-sdk) ou o [Azure Machine Learning Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> Como parte do processo inicial de criação e registro de armazenamento de datastore, Azure Machine Learning valida que o serviço de armazenamento subjacente existe e que a entidade de segurança fornecida pelo usuário (nome de usuário, entidade de serviço ou token SAS) tem acesso a esse armazenamento. No entanto, para os repositórios de dados Azure Data Lake Storage Gen 1 e 2, essa validação ocorre mais tarde, [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) quando [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) os métodos de acesso a data, como ou são chamados. 
<br><br>
Após a criação do repositório de armazenamento, essa validação é executada somente para métodos que exigem acesso ao contêiner de armazenamento subjacente, e **não** sempre que os objetos de repositório de data são recuperados. Por exemplo, a validação ocorrerá se você quiser baixar arquivos do seu repositório de armazenamento; Mas se você quiser apenas alterar seu repositório de armazenamento padrão, a validação não ocorrerá.

### <a name="python-sdk"></a>SDK do Python

Todos os métodos de registro estão na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e têm o formulário `register_azure_*`.
> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, haverá suporte para a criação somente de repositórios de armazenamento **por meio do SDK** .

Você pode encontrar as informações necessárias para preencher o `register_azure_*()` método no [portal do Azure](https://portal.azure.com).

* Se você planeja usar uma chave de conta ou um token SAS para autenticação, selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. 
  * A página **visão geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivos. 
      1. Para chaves de conta, acesse **chaves de acesso** no painel **configurações** . 
      1. Para tokens SAS, acesse **assinaturas de acesso compartilhado** no painel **configurações** .

* Se você planeja usar uma entidade de serviço para autenticação, vá para o **registros de aplicativo** e selecione qual aplicativo você deseja usar. 
    * Sua página de **visão geral** correspondente conterá informações necessárias, como ID do locatário e ID do cliente.

Os exemplos a seguir mostram como registrar um contêiner de blob do Azure, um compartilhamento de arquivos do Azure e Azure Data Lake Storage a geração 2 como um repositório de armazenamento. Os parâmetros fornecidos nesses exemplos são os **parâmetros necessários** para criar e registrar um repositório de armazenamento. 

Para criar armazenamentos de datapara outros serviços de armazenamento e ver parâmetros opcionais para esses métodos, consulte a [documentação de referência `register_azure_*` para os métodos aplicáveis](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Contêiner de blob

Para registrar um contêiner de blob do Azure como um repositório de [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)armazenamento, use.

O código a seguir cria e registra `blob_datastore_name` o repositório de armazenamento `ws` no espaço de trabalho. Esse datastore acessa o `my-container-name` contêiner de blob na conta `my-account-name` de armazenamento, usando a chave de acesso da conta fornecida.

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
Se o seu contêiner de BLOBs estiver na rede virtual, `skip_validation=True` inclua o [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) parâmetro em seu método. 

#### <a name="file-share"></a>Compartilhamento de arquivo

Para registrar um compartilhamento de arquivos do Azure como um repositório de [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)armazenamento, use. 

O código a seguir cria e registra `file_datastore_name` o repositório de armazenamento `ws` no espaço de trabalho. Esse datastore acessa o `my-fileshare-name` compartilhamento de arquivos na conta `my-account-name` de armazenamento, usando a chave de acesso da conta fornecida.

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
Se o compartilhamento de arquivos estiver na rede virtual, inclua o `skip_validation=True` parâmetro em [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) seu método. 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage geração 2

Para um repositório de armazenamento de Azure Data Lake Storage geração 2 (ADLS Gen 2), use [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um repositório de armazenamento de credenciais conectado a um armazenamento do Azure datalake Gen 2 com [permissões de entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Para utilizar sua entidade de serviço, você precisa [registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder à entidade de serviço acesso de *proprietário de dados do blob de armazenamento* . Saiba mais sobre o [controle de acesso configurado para o ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Para utilizar sua entidade de serviço, você precisa [registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder a entidade de serviço com o acesso de dados correto. Saiba mais sobre o [controle de acesso configurado para o ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O código a seguir cria e registra `adlsgen2_datastore_name` o repositório de armazenamento `ws` no espaço de trabalho. Esse datastore acessa o sistema `test` de arquivos na conta `account_name` de armazenamento, usando as credenciais de entidade de serviço fornecidas.

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

Crie um novo repositório de armazenamento em algumas etapas no Azure Machine Learning Studio:

> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, haverá suporte para a criação somente de repositórios de armazenamento [por meio do SDK](#python-sdk) . 

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).
1. Selecione **repositórios de armazenamento** no painel esquerdo em **gerenciar**.
1. Selecione **+ Novo armazenamento de dados**.
1. Preencha o formulário para um novo repositório de armazenamento. O formulário se atualiza de forma inteligente com base em suas seleções para tipo de armazenamento e tipo de autenticação do Azure.
  
Você pode encontrar as informações necessárias para preencher o formulário na [portal do Azure](https://portal.azure.com). Selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. A página **visão geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivos. 

* Para itens de autenticação, como chave de conta ou token SAS, acesse **chaves de acesso** no painel **configurações** . 

* Para itens de entidade de serviço como, ID do locatário e ID do cliente, vá para o **registros de aplicativo** e selecione qual aplicativo você deseja usar. Sua página de **visão geral** correspondente conterá esses itens. 

O exemplo a seguir demonstra a aparência do formulário quando você cria um repositório de armazenamento de BLOBs do Azure: 
    
![Formulário para um novo repositório de armazenamento](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obter repositórios de armazenamento do seu espaço de trabalho

Para obter um repositório de armazenamento específico registrado no espaço de trabalho atual, [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) use o método estático `Datastore` na classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de armazenamentos de dados registrados com um determinado espaço de trabalho, você [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) pode usar a propriedade em um objeto de espaço de trabalho:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Para obter o armazenamento de datastore padrão do espaço de trabalho, use esta linha:

```Python
datastore = ws.get_default_datastore()
```
Você também pode alterar o repositório de armazenamento padrão com o código a seguir. Essa capacidade só tem suporte por meio do SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Carregar e baixar dados

Os [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) métodos [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e descritos nos exemplos a seguir são específicos para e operam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

> [!NOTE]
> No momento, não há suporte para o carregamento em repositórios de AzureDataLakeGen2.

### <a name="upload"></a>Carregar

Carregue um diretório ou arquivos individuais no repositório de armazenamento usando o SDK do Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O `target_path` parâmetro especifica o local no compartilhamento de arquivos (ou contêiner de BLOB) a ser carregado. Por padrão `None`, os dados são carregados para a raiz. Se `overwrite=True`, todos os dados existentes `target_path` em são substituídos.

Você também pode carregar uma lista de arquivos individuais no repositório de armazenamento por meio `upload_files()` do método.

### <a name="download"></a>Baixar

Baixar dados de um repositório de armazenamento para o sistema de arquivos local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O `target_path` parâmetro é o local do diretório local para o qual baixar os dados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de blob) para baixar, forneça esse caminho para `prefix`. Se `prefix` for `None`, todo o conteúdo do seu compartilhamento de arquivos (ou contêiner de BLOB) será baixado.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Acesse seus dados durante o treinamento

Para interagir com os dados em seus armazenamentos de dados ou para empacotar seus dados em um objeto consumível para tarefas de aprendizado de máquina, como treinamento, [crie um conjunto de Azure Machine Learning](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabulares em um data frame do pandas ou do Spark. Os conjuntos de dados também fornecem a capacidade de baixar ou montar arquivos de qualquer formato do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure e banco de dados do Azure para PostgreSQL. [Saiba mais sobre como treinar com conjuntos de](how-to-train-with-datasets.md)os.

### <a name="accessing-source-code-during-training"></a>Acessando o código-fonte durante o treinamento

O armazenamento de BLOBs do Azure tem velocidades de taxa de transferência mais altas que um compartilhamento de arquivos do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo. Por esse motivo, é recomendável configurar suas execuções para usar o armazenamento de BLOBs para transferir arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual repositório de armazenamento de blob usar para transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Acessar dados durante a Pontuação

Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não fornecem acesso a repositórios de armazenamento. Use a tabela a seguir para entender quais métodos permitem que você acesse os repositórios de armazenamento durante a Pontuação:

| Método | Acesso ao repositório de armazenamento | Descrição |
| ----- | :-----: | ----- |
| [Previsão em lote](how-to-use-parallel-run-step.md) | ✔ | Fazer previsões sobre grandes quantidades de dados assincronamente. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos como um serviço Web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos para IoT Edge dispositivos. |

Para situações em que o SDK não fornece acesso aos armazenamentos de dados, você poderá criar código personalizado usando o SDK do Azure relevante para acessar os dados. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de cliente que você pode usar para acessar dados armazenados em BLOBs ou arquivos.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento do Azure com suporte

Azure Machine Learning dá suporte ao acesso a dados do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure e banco de dados do Azure para PostgreSQL. Se você estiver usando um armazenamento sem suporte, recomendamos que você mova seus dados para soluções de armazenamento do Azure com suporte usando [Azure data Factory e essas etapas](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Mover dados para o armazenamento com suporte pode ajudá-lo a salvar os custos de egresso de dados durante experimentos de aprendizado de máquina. 

Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores predefinidos sem custo adicional. Esses conectores incluem serviços de dados do Azure, fontes de dados locais, Amazon S3 e redshift e Google BigQuery.

## <a name="next-steps"></a>Próximas etapas

* [Criar um conjunto de informações do Azure Machine Learning](how-to-create-register-datasets.md)
* [Treinar um modelo](how-to-train-ml-models.md)
* [Implantar um modelo](how-to-deploy-and-where.md)
