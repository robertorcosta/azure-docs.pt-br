---
title: 'Início Rápido: biblioteca do armazenamento de blobs do Azure v12 – JavaScript'
description: Neste início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 para JavaScript para criar um contêiner e um blob no armazenamento de blobs (objeto). Em seguida, você aprenderá como baixar o blob para seu computador local e como listar todos os blobs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0a6d7ce8f1f6b81c3dbae3d41842345be5d2e551
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422027"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Início Rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure v12 para JavaScript

Comece a usar a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para JavaScript. O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. Siga as etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O Armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, confira [Início Rápido: biblioteca de clientes do Armazenamento de Blobs do Azure para JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Use a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para JavaScript para:

* Criar um contêiner
* Carregar um blob para o Armazenamento do Azure
* Listar todos os blobs em um contêiner
* Baixar o blob em seu computador local
* Excluir um contêiner

[Documentação de referência da API](/javascript/api/@azure/storage-blob) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Pacote (Gerenciador de pacotes de nós)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Node.js](https://nodejs.org/en/download/) atual para o seu sistema operacional.

## <a name="setting-up"></a>Configurando

Esta seção o orienta na preparação de um projeto para trabalhar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para JavaScript.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo JavaScript chamado *blob-quickstart-v12*.

1. Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Alterne para o diretório *blob-quickstart-v12* recém-criado.

    ```console
    cd blob-quickstart-v12
    ```

1. Crie um novo arquivo de texto chamado *package.JSON*. Esse arquivo define o projeto Node.js. Salve esse arquivo no diretório *blob-quickstart-V12*. Este é o conteúdo do arquivo:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Você pode colocar seu próprio nome no campo `author`, se desejar.
   
### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório *blob-quickstart-v12*, instale a biblioteca de clientes do Armazenamento de Blobs do Azure para JavaScript usando o comando `npm install`. Esse comando lê o arquivo *package.json* e instala a biblioteca de clientes do armazenamento de blobs do Azure V12 para o pacote JavaScript e todas as bibliotecas das quais ela depende.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra outro arquivo de texto novo no editor de código
1. Adicione chamadas `require` para carregar os módulos do Azure e do Node.js
1. Crie a estrutura para o programa, incluindo uma manipulação de exceção muito básica

    O código é o seguinte:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Salve o novo arquivo como *blob-quickstart-v12.js* no diretório *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-connection-string-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blob-introduction/blob1.png)

Use as seguintes classes de JavaScript para interagir com esses recursos:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): a classe `ContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): a classe `BlobClient` permite manipular os blobs do Armazenamento do Azure.

## <a name="code-examples"></a>Exemplos de código

Esses exemplos de trechos de código mostram como executar o seguinte com a biblioteca de clientes do Armazenamento de Blobs do Azure para JavaScript:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
* [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar blobs](#download-blobs)
* [Excluir um contêiner](#delete-a-container)

### <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento a partir da variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código à função `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Criar um contêiner

Escolha um nome para o novo contêiner. O código abaixo anexa um valor de UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) chamando o método [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-). Em seguida, chame o método [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) para obter uma referência a um contêiner. Por fim, chame [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) para realmente criar o contêiner em sua conta de armazenamento.

Adicione este código ao final da função `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Cria uma cadeia de texto a ser carregada em um blob.
1. Obtém uma referência para um objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) chamando o método [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) em [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) da seção [Criar um contêiner](#create-a-container).
1. Carrega os dados da cadeia de texto para o blob chamando o método [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-).

Adicione este código ao final da função `main`:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs no contêiner chamando o método [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-). Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas esse blob.

Adicione este código ao final da função `main`:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Baixar blobs

Baixe o blob criado anteriormente chamando o método [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-). O exemplo de código inclui uma função auxiliar chamada `streamToString`, que é usada para ler um fluxo legível do Node.js em uma cadeia de caracteres.

Adicione este código ao final da função `main`:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Adicione essa função auxiliar *depois* da função `main`:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao remover todo o contêiner usando o método [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-). Você também pode excluir arquivos locais se desejar.

Adicione este código ao final da função `main`:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria uma cadeia de texto e a carrega no armazenamento de blobs. Em seguida, lista os blobs no contêiner, baixa o blob e exibe os dados baixados.

Em um prompt de console, navegue até o diretório que contém o arquivo *blob-quickstart-v12.py* e execute o comando `node` para executar o aplicativo.

```console
node blob-quickstart-v12.js
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Percorra o código em seu depurador e verifique o portal do Azure durante todo o processo. Verifique se o contêiner foi criado. Você pode abrir o blob dentro do contêiner e exibir o conteúdo.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando JavaScript.

Para ver os aplicativos de exemplo de armazenamento de blobs, acesse:

> [!div class="nextstepaction"]
> [Exemplos do SDK do Armazenamento de Blobs do Azure v12 JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Para saber mais, confira o [SDK do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite [Documentação do SDK do Azure para JavaScript](/azure/javascript/).
