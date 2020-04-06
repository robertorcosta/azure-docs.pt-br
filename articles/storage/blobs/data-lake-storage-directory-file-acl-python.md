---
title: Azure Data Lake Storage Gen2 Python SDK para arquivos & ACLs
description: Use o Python gerenciar diretórios e listas de controle de acesso de arquivos e diretórios (ACL) em contas de armazenamento que tenham hns (namespace) hierárquico ativado.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a00713df2cdda626a76cc648826f7e56df214232
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668721"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use python para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Python para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm hns (namespace) hierárquico ativado. 

[Pacote (Índice de pacote python)](https://pypi.org/project/azure-storage-file-datalake/) | [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [de referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | da API[Gen1 ao mapeamento](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | Gen2[dão feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tenha hns (namespace) hierárquico habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca cliente do Azure Data Lake Storage para Python usando [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Adicione essas instruções de importação ao topo do seu arquivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Conecte-se à conta

Para usar os trechos deste artigo, você precisará criar uma instância **dataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** usando uma chave de conta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Substitua o valor de espaço reservado `storage_account_name` pelo nome da sua conta de armazenamento.

- Substitua `storage_account_key` o valor do espaço reservado pela chave de acesso da conta de armazenamento.

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se usando o Azure Active Directory (AD)

Você pode usar a [biblioteca cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância **DataLakeServiceClient** usando um ID do cliente, um segredo de cliente e um ID de inquilino.  Para obter esses valores, consulte [Adquirir um token do Azure AD para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

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
> Para obter mais exemplos, consulte a [biblioteca cliente de identidade do Azure para](https://pypi.org/project/azure-identity/) documentação Python.

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos funciona como um recipiente para seus arquivos. Você pode criar um chamando o método **FileSystemDataLakeServiceClient.create_file_system.**

Este exemplo cria um `my-file-system`sistema de arquivos chamado .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **FileSystemClient.create_directory.**

Este exemplo adiciona um `my-directory` diretório nomeado a um sistema de arquivos. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método **DataLakeDirectoryClient.rename_directory.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomeia um subdiretório `my-subdirectory-renamed`para o nome .

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

Exclua um diretório chamando o método **DataLakeDirectoryClient.delete_directory.**

Este exemplo exclui um `my-directory`diretório chamado .  

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

Obtenha a lista de controle de acesso (ACL) de um diretório ligando para o método **DataLakeDirectoryClient.get_access_control** e defina a ACL chamando o método **DataLakeDirectoryClient.set_access_control.**

> [!NOTE]
> Se o aplicativo autorizar o acesso usando o Azure Active Directory (Azure AD), certifique-se de que o principal de segurança que seu aplicativo usa para autorizar o acesso tenha sido atribuído à [função Deproprietário de dados do Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte o controle de [acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Este exemplo recebe e define a ACL de um diretório chamado `my-directory`. A `rwxr-xrw-` seqüência dá ao usuário possuinte permissões de leitura, gravação e execução, dá ao grupo possuinte apenas permissões de leitura e execução e dá a todos os outros permissão para ler e escrever.

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

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um arquivo para um diretório 

Primeiro, crie uma referência de arquivo no diretório de destino criando uma instância da classe **DataLakeFileClient.** Faça upload de um arquivo chamando o método **DataLakeFileClient.append_data.** Certifique-se de concluir o upload ligando para o método **DataLakeFileClient.flush_data.**

Este exemplo envia um arquivo de `my-directory`texto para um diretório chamado .   

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
> Se o tamanho do seu arquivo for grande, seu código terá que fazer várias chamadas para o método **DataLakeFileClient.append_data.** Considere usar o método **DataLakeFileClient.upload_data** em vez disso. Dessa forma, você pode carregar todo o arquivo em uma única chamada. 

## <a name="upload-a-large-file-to-a-directory"></a>Carregue um arquivo grande para um diretório

Use o método **DataLakeFileClient.upload_data** para carregar arquivos grandes sem ter que fazer várias chamadas para o método **DataLakeFileClient.append_data.**

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

## <a name="manage-file-permissions"></a>Gerenciar permissões de arquivos

Obtenha a lista de controle de acesso (ACL) de um arquivo ligando para o método **DataLakeFileClient.get_access_control** e defina a ACL ligando para o método **DataLakeFileClient.set_access_control.**

> [!NOTE]
> Se o aplicativo autorizar o acesso usando o Azure Active Directory (Azure AD), certifique-se de que o principal de segurança que seu aplicativo usa para autorizar o acesso tenha sido atribuído à [função Deproprietário de dados do Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte o controle de [acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Este exemplo obtém e define a `my-file.txt`ACL de um arquivo chamado . A `rwxr-xrw-` seqüência dá ao usuário possuinte permissões de leitura, gravação e execução, dá ao grupo possuinte apenas permissões de leitura e execução e dá a todos os outros permissão para ler e escrever.

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

Abra um arquivo local para escrever. Em seguida, crie uma instância **DataLakeFileClient** que represente o arquivo que você deseja baixar. Ligue para o **DataLakeFileClient.read_file** para ler bytes do arquivo e, em seguida, escrever esses bytes para o arquivo local. 

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

Liste o conteúdo do diretório ligando para o método **FileSystemClient.get_paths** e, em seguida, enumerando os resultados.

Este exemplo, imprime o caminho de cada subdiretório e arquivo `my-directory`localizado em um diretório chamado .

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

* [Documentação da referência de API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pacote (Índice do pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dê feedback](https://github.com/Azure/azure-sdk-for-python/issues)
