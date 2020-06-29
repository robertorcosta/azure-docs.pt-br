---
title: 'Início Rápido: biblioteca do armazenamento de Blobs do Azure v12 – Python'
description: Neste início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 para Python para criar um contêiner e um blob no Armazenamento de blobs (objeto). Em seguida, você aprenderá como baixar o blob para seu computador local e como listar todos os blobs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: c24b934af17dab4f263de504da81d5ccd22d7fb9
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84975305"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Início Rápido: Gerenciar blobs com o SDK do Python v12

Neste início rápido, você aprenderá a gerenciar blobs usando o Python. Os blobs são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, mídia de streaming e dados de arquivos. Você carregará, baixará e listará os blobs e criará e excluirá contêineres.

[Documentação de referência da API](/python/api/azure-storage-blob) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [Pacote (Índice de Pacote do Python)](https://pypi.org/project/azure-storage-blob/) | [Exemplos](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Uma conta de armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 ou superior.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, confira [Início Rápido: Gerenciar blobs com o SDK do Python v2.1](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurando

Esta seção fornece instruções sobre como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Python chamado *blob-quickstart-v12*.

1. Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Alterne para o diretório *blob-quickstart-v12* recém-criado.

    ```console
    cd blob-quickstart-v12
    ```

1. No diretório *blob-quickstart-v12*, crie outro diretório chamado *dados*. É nele que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale a biblioteca de clientes do Armazenamento de Blobs do Azure para Python usando o comando `pip install`.

```console
pip install azure-storage-blob
```

Esse comando instala a biblioteca de clientes do Armazenamento de Blobs do Azure para o pacote Python e todas as bibliotecas das quais ela depende. Nesse caso, essa é apenas a biblioteca principal do Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra um arquivo de texto novo no editor de código
1. Adicione instruções `import`
1. Crie a estrutura do programa, incluindo um tratamento de exceções básico

    O código é o seguinte:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Salve o novo arquivo como *blob-quickstart-v12.py* no diretório *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blobs-introduction/blob1.png)

Use as classes Python a seguir para interagir com esses recursos:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): a classe `ContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): a classe `BlobClient` permite manipular os blobs do Armazenamento do Azure.

## <a name="code-examples"></a>Exemplos de código

Esses exemplos de trechos de código mostram como executar o seguinte com a biblioteca de clientes do Armazenamento de Blobs do Azure para Python:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
* [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar blobs](#download-blobs)
* [Excluir um contêiner](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento a partir da variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código ao bloco `try`:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Criar um contêiner

Escolha um nome para o novo contêiner. O código abaixo anexa um valor de UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-). Depois chame o método [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o contêiner em sua conta de armazenamento.

Adicione este código ao final do bloco `try`:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Cria um arquivo de texto no diretório local.
1. Obtém a referência para um objeto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) em [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) da seção [Criar um contêiner](#create-a-container).
1. Carrega o arquivo de texto local para o blob chamando o método [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-).

Adicione este código ao final do bloco `try`:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Lista os blobs no contêiner chamando o método [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-). Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas esse blob.

Adicione este código ao final do bloco `try`:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Baixar blobs

Baixa o blob criado anteriormente chamando o método [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-). O código de exemplo adiciona o sufixo "DOWNLOAD" ao nome do blob para que você possa ver os dois arquivos no sistema de arquivos local.

Adicione este código ao final do bloco `try`:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao remover todo o contêiner usando o método [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). Você também pode excluir arquivos locais se desejar.

O aplicativo pausa a entrada do usuário chamando `input()` antes de excluir o blob, o contêiner e os arquivos locais. Essa é uma boa oportunidade de verificar se os recursos foram criados corretamente antes de serem excluídos.

Adicione este código ao final do bloco `try`:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Executar o código

Este aplicativo cria um arquivo de teste na pasta local e o carrega no armazenamento de blobs. Em seguida, o exemplo lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até o diretório que contém o arquivo *blob-quickstart-v12.py* e execute o comando `python` a seguir para executar o aplicativo.

```console
python blob-quickstart-v12.py
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se os dois arquivos estão na pasta *dados*. Você pode abri-los e ver se eles são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando Python.

Para ver os aplicativos de exemplo de armazenamento de blobs, acesse:

> [!div class="nextstepaction"]
> [Exemplos do SDK do Armazenamento de Blobs do Azure v12 Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Para saber mais, confira as [bibliotecas de cliente do Armazenamento do Azure para Python](/azure/developer/python/sdk/storage/overview?view=storage-py-v12).
* Para acessar tutoriais, exemplos, inícios rápidos e outras documentações, acesse [Azure para Desenvolvedores de Python](/azure/python/).
