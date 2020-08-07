---
title: Azure Data Lake Storage Gen2 o SDK do Python para arquivos & ACLs
description: Use os diretórios de gerenciamento do Python e ACLs (listas de controle de acesso) de arquivo e diretório em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 04/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 762f616a27338750973e843a999b36a22171fbcd
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847594"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o Python para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Python para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado. 

[Pacote (índice do pacote do Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar um.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca de cliente Azure Data Lake Storage para Python usando [Pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Adicione essas instruções de importação na parte superior do seu arquivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Conectar à conta

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Substitua o valor de espaço reservado `storage_account_name` pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor de espaço reservado pela chave de acesso da conta de armazenamento.

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) .

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar um chamando o método **FileSystemDataLakeServiceClient. create_file_system** .

Este exemplo cria um contêiner chamado `my-file-system` .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **FileSystemClient. create_directory** .

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método **DataLakeDirectoryClient. rename_directory** . Passe o caminho do diretório desejado de um parâmetro. 

Este exemplo renomeia um subdiretório para o nome `my-subdirectory-renamed` .

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método **DataLakeDirectoryClient. delete_directory** .

Este exemplo exclui um diretório chamado `my-directory`.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Gerenciar permissões de diretório

Obtenha a ACL (lista de controle de acesso) de um diretório chamando o método **DataLakeDirectoryClient. get_access_control** e defina a ACL chamando o método **DataLakeDirectoryClient. set_access_control** .

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . A cadeia de caracteres `rwxr-xrw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, chame o método **FileSystemClient. _get_root_directory_client** .

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório 

Primeiro, crie uma referência de arquivo no diretório de destino criando uma instância da classe **DataLakeFileClient** . Carregue um arquivo chamando o método **DataLakeFileClient. append_data** . Certifique-se de concluir o carregamento chamando o método **DataLakeFileClient. flush_data** .

Este exemplo carrega um arquivo de texto em um diretório chamado `my-directory` .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Se o tamanho do arquivo for grande, seu código terá que fazer várias chamadas para o método **DataLakeFileClient. append_data** . Considere usar o método **DataLakeFileClient. upload_data** em vez disso. Dessa forma, você pode carregar o arquivo inteiro em uma única chamada. 

## <a name="upload-a-large-file-to-a-directory"></a>Carregar um arquivo grande em um diretório

Use o método **DataLakeFileClient. upload_data** para carregar arquivos grandes sem precisar fazer várias chamadas para o método **DataLakeFileClient. append_data** .

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Gerenciar permissões de arquivo

Obtenha a ACL (lista de controle de acesso) de um arquivo chamando o método **DataLakeFileClient. get_access_control** e defina a ACL chamando o método **DataLakeFileClient. set_access_control** .

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Este exemplo obtém e define a ACL de um arquivo chamado `my-file.txt` . A cadeia de caracteres `rwxr-xrw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório 

Abra um arquivo local para gravação. Em seguida, crie uma instância de **DataLakeFileClient** que representa o arquivo que você deseja baixar. Chame o **DataLakeFileClient. read_file** para ler bytes do arquivo e, em seguida, grave esses bytes no arquivo local. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo do diretório chamando o método **FileSystemClient. get_paths** e, em seguida, enumerando pelos resultados.

Este exemplo, imprime o caminho de cada subdiretório e arquivo que está localizado em um diretório chamado `my-directory` .

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Confira também

* [Documentação de referência da API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pacote (Índice de Pacotes do Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)
