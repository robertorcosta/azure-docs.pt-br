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
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: c5200214946b52ce974a8b7557e38eb57481028a
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782984"
---
# <a name="connect-to-azure-storage-services"></a>Conectar aos serviços de armazenamento do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como **se conectar aos serviços de armazenamento do Azure por meio de Azure Machine Learning repositórios de**armazenamento. Os armazenamentos de dados conectam-se com segurança ao serviço de armazenamento do Azure sem colocar suas credenciais de autenticação e a integridade da fonte original em risco. Eles armazenam informações de conexão, como a sua ID de assinatura e a autorização de token em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho, para que você possa acessar o armazenamento com segurança sem precisar embuti-los em seus scripts. Você pode usar o [Azure Machine Learning SDK do Python](#python) ou o [Azure Machine Learning Studio](#studio) para criar e registrar armazenamentos de datastores.

Se você preferir criar e gerenciar repositórios de armazenamento usando a extensão de VS Code de Azure Machine Learning; Visite o [Guia de instruções de gerenciamento de recursos vs Code](how-to-manage-resources-vscode.md#datastores) para saber mais.

Você pode criar armazenamentos de dados [dessas soluções de armazenamento do Azure](#matrix). **Para soluções de armazenamento sem suporte**e para salvar o custo de egresso de dados durante experimentos de ml, [mova seus dados](#move) para uma solução de armazenamento do Azure com suporte.  

Para entender onde os armazenamentos de dados se encaixam no fluxo de trabalho de acesso a dados geral do Azure Machine Learning, confira o artigo [Acessar dados com segurança](concept-data.md#data-workflow).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:
- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento do Azure com um [tipo de armazenamento com suporte](#matrix).

- O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um workspace do Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python. 

    Importe as classes `Workspace` e `Datastore` e carregue as informações de assinatura do arquivo `config.json` usando a função `from_config()`. Isso procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são automaticamente registrados como repositórios de armazenamento no espaço de trabalho. Seus nomes são `workspaceblobstore` e `workspacefilestore`, respectivamente. O `workspaceblobstore` é usado para armazenar artefatos de espaço de trabalho e seus logs de experimento do Machine Learning. Ele também é definido como o **repositório de armazenamento padrão** e não pode ser excluído do espaço de trabalho. O `workspacefilestore` é usado para armazenar blocos de anotações e scripts de R autorizados por meio da [instância de computação](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files).
    
    > [!NOTE]
    > O designer do Azure Machine Learning (versão prévia) criará um armazenamento de dados chamado **azureml_globaldatasets** automaticamente quando você abrir um exemplo na página inicial do designer. Esse armazenamento de dados contém apenas conjuntos de dados de amostra. **Não** use esse armazenamento de dados para acesso a dados confidenciais.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviço de armazenamento de dados com suporte

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir.

| Tipo de&nbsp;armazenamento | Tipo de&nbsp;autenticação | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; – SDK do Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI do Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [API REST do &nbsp;Azure&nbsp;Machine&nbsp;Learning](https://docs.microsoft.com/rest/api/azureml/) | Código VS
---|---|---|---|---|---|---
[Armazenamento de&nbsp;Blobs&nbsp;do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓ |✓
[Compartilhamento de&nbsp;arquivo do &nbsp;Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Entidade de serviço| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entidade de serviço| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticação SQL <br>Entidade de serviço| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Autenticação SQL| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;para&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticação SQL|  | ✓* | ✓* |✓*|
[Sistema de&nbsp;arquivos&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓** |✓** |

*Só há suporte ao MySQL no pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
*Só há suporte ao Databricks no pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientação de armazenamento

Recomendamos criar um armazenamento de dados para um [contêiner de blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Tanto o armazenamento Standard quanto o Premium estão disponíveis para blobs. Embora o armazenamento Premium seja mais caro, suas velocidades de taxa de transferência mais rápidas podem melhorar a velocidade de seus treinamentos, especialmente se você treina um grande conjunto de dados. Para obter informações sobre o custo de contas de armazenamento, confira a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

O [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) é criado sobre o Armazenamento de Blobs do Azure e projetado para análise corporativa de Big Data. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados.

## <a name="storage-access-and-permissions"></a>Acesso e permissões de armazenamento

Para garantir que você se conecte com segurança ao serviço de armazenamento do Azure, Azure Machine Learning exige que você tenha permissão para acessar o contêiner de armazenamento de dados correspondente. Esse acesso depende das credenciais de autenticação usadas para registrar o repositório de armazenamento. 

### <a name="virtual-network"></a>Rede virtual 

Se sua conta de armazenamento de dados estiver em uma **rede virtual**, serão necessárias etapas de configuração adicionais para garantir que Azure Machine Learning tenha acesso aos seus dados. Consulte [isolamento de rede & privacidade](how-to-enable-virtual-network.md#machine-learning-studio) para garantir que as etapas de configuração apropriadas sejam aplicadas quando você criar e registrar seu repositório de armazenamento.  

### <a name="access-validation"></a>Validação de acesso

**Como parte do processo inicial de criação e registro de armazenamento de datastore**, Azure Machine Learning valida automaticamente que o serviço de armazenamento subjacente existe e que a entidade de segurança fornecida pelo usuário (nome de usuário, entidade de serviço ou token SAS) tem acesso ao armazenamento especificado.

**Após a criação do repositório de armazenamento**, essa validação é executada somente para métodos que exigem acesso ao contêiner de armazenamento subjacente, e **não** sempre que os objetos de repositório de data são recuperados. Por exemplo, a validação ocorrerá se você quiser baixar arquivos do seu armazenamento de dados; mas se você quiser apenas alterar o armazenamento de dados padrão, a validação não ocorrerá.

Para autenticar seu acesso ao serviço de armazenamento subjacente, você pode fornecer sua chave de conta, tokens de SAS (assinaturas de acesso compartilhado) ou entidade de serviço no `register_azure_*()` método correspondente do tipo de repositório de armazenamento que você deseja criar. A [matriz de tipo de armazenamento](#matrix) lista os tipos de autenticação com suporte que correspondem a cada tipo de repositório de armazenamento.

Você pode encontrar informações de chave de conta, token SAS e entidade de serviço em seu [portal do Azure](https://portal.azure.com).

* Se você planeja usar uma chave de conta ou um token SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que deseja registrar. 
  * A página **Visão Geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivo. 
      1. Para obter as chaves de conta, vá para **Chaves de acesso** no painel **Configurações**. 
      1. Para obter os tokens SAS, vá para **Assinaturas de acesso compartilhado** no painel **Configurações**.

* Se você planeja usar uma entidade de serviço para autenticação, vá para o **registros de aplicativo** e selecione qual aplicativo você deseja usar. 
    * Sua página de **visão geral** correspondente conterá informações necessárias, como ID do locatário e ID do cliente.

> [!IMPORTANT]
> Por motivos de segurança, talvez seja necessário alterar suas chaves de acesso para uma conta de armazenamento do Azure (chave de conta ou token SAS). Ao fazer isso, certifique-se de sincronizar as novas credenciais com seu espaço de trabalho e os repositórios de armazenamento conectados a ela. Saiba como sincronizar suas credenciais atualizadas com [estas etapas](how-to-change-storage-access-key.md). 

### <a name="permissions"></a>Permissões

Para o contêiner de blob do Azure e o armazenamento Azure Data Lake Gen 2, verifique se suas credenciais de autenticação têm acesso ao **leitor de dados de blob de armazenamento** . Saiba mais sobre o [leitor de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

<a name="python"></a>

## <a name="create-and-register-datastores-via-the-sdk"></a>Criar e registrar repositórios de armazenamento por meio do SDK

Quando registra uma solução de armazenamento do Azure como um armazenamento de dados, você cria e registra automaticamente esse armazenamento de dados em um workspace específico. Examine a seção [permissões de acesso de armazenamento &](#storage-access-and-permissions) para entender onde encontrar as credenciais de autenticação necessárias.

Nesta seção estão exemplos de como criar e registrar um repositório de armazenamento por meio do SDK do Python para os seguintes tipos de armazenamento. Os parâmetros fornecidos nesses exemplos são os **parâmetros necessários** para criar e registrar um armazenamento de dados.

* [Contêiner de blobs do Azure](#azure-blob-container)
* [Compartilhamento de arquivos do Azure](#azure-file-share)
* [Azure Data Lake Storage Generation 2](#azure-data-lake-storage-generation-2)

 Para criar repositórios de armazenamento para outros serviços de armazenamento com suporte, consulte a [documentação de referência para os `register_azure_*` métodos aplicáveis](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

Se você preferir uma baixa experiência de código, consulte [criar repositórios de armazenamento no Azure Machine Learning Studio](#studio).

> [!NOTE]
> O nome do repositório de armazenamento deve conter apenas letras minúsculas, dígitos e sublinhados. 

### <a name="azure-blob-container"></a>Contêiner de blobs do Azure

Para registrar um contêiner de blob do Azure como armazenamento de dados, use [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

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

### <a name="azure-file-share"></a>Compartilhamento de arquivos do Azure

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para um armazenamento de dados de Azure Data Lake Storage Generation 2 (ADLS Gen 2), use [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um armazenamento de dados de credenciais conectado a um armazenamento do Azure Data Lake Gen 2 com [permissões de entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). 

Para utilizar sua entidade de serviço, você precisa [registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder a entidade de serviço com acesso ao **leitor de dados de blob de armazenamento** . Saiba mais sobre [configuração de controle de acesso para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

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

<a name="studio"></a>


## <a name="create-datastores-in-the-studio"></a>Criar repositórios de armazenamento no estúdio 


Crie um novo repositório de armazenamento em algumas etapas com o Azure Machine Learning Studio.

> [!IMPORTANT]
> Se sua conta de armazenamento de dados estiver em uma rede virtual, serão necessárias etapas de configuração adicionais para garantir que o estúdio tenha acesso aos seus dados. Consulte [isolamento de rede & privacidade](how-to-enable-virtual-network.md#machine-learning-studio) para garantir que as etapas de configuração apropriadas sejam aplicadas. 

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).
1. Selecione **Armazenamentos de dados** no painel esquerdo em **Gerenciar**.
1. Selecione **+ Novo armazenamento de dados**.
1. Preencha o formulário de um novo armazenamento de dados. O formulário se atualiza de forma inteligente com base nas seleções de tipo de armazenamento e tipo de autenticação do Azure. Consulte a [seção acesso e permissões de armazenamento](#access-validation) para entender onde encontrar as credenciais de autenticação de que você precisa para preencher este formulário.

O exemplo a seguir demonstra a aparência do formulário quando você cria um **repositório de armazenamento de BLOBs do Azure**: 
    
![Formulário de um novo armazenamento de dados](media/how-to-access-data/new-datastore-form.png)

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Usar dados em seus armazenamentos

Depois de criar um datastore, [crie um conjunto](how-to-create-register-datasets.md) de dados do Azure Machine Learning para interagir com os seus dados. Os conjuntos de dados embalam seu dado em um objeto de consumo avaliado lentamente para tarefas de aprendizado de máquina, como treinamento. Eles também fornecem a capacidade de [baixar ou montar](how-to-train-with-datasets.md#mount-vs-download) arquivos de qualquer formato dos serviços de armazenamento do Azure, como o armazenamento de BLOBs do Azure e o ADLS Gen 2. Você também pode usá-los para carregar dados tabulares em um dataframe pandas ou Spark.

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
