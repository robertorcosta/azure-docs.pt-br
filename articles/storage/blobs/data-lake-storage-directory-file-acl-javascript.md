---
title: Use JavaScript para arquivos & ACLs no Azure Data Lake Storage Gen2
description: Use a biblioteca cliente do Azure Storage Data Lake para JavaScript para gerenciar diretórios e listas de controle de acesso de arquivos e diretórios (ACL) em contas de armazenamento que tenham hns (names) hierárquico ativado.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061552"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use JavaScript para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o JavaScript para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm hns (namespace) hierárquico ativado. 

[Amostras do pacote (Gerenciador de pacotes de nó)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [dão feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tenha hns (namespace) hierárquico habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Se você estiver usando este pacote em um aplicativo Node.js, você precisará do Node.js 8.0.0.0 ou superior.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca cliente data lake para JavaScript abrindo uma janela de terminal e, em seguida, digitando o seguinte comando.

```javascript
npm install @azure/storage-file-datalake
```

Importe `storage-file-datalake` o pacote colocando esta declaração na parte superior do seu arquivo de código. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Conecte-se à conta 

Para usar os trechos deste artigo, você precisará criar uma instância **dataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** usando uma chave de conta.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Este método de autorização funciona apenas para aplicações node.js. Se você planeja executar seu código em um navegador, você pode autorizar usando o Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se usando o Azure Active Directory (AD)

Você pode usar a [biblioteca cliente de identidade do Azure para JS](https://www.npmjs.com/package/@azure/identity) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância **DataLakeServiceClient** usando um ID do cliente, um segredo de cliente e um ID de inquilino.  Para obter esses valores, consulte [Adquirir um token do Azure AD para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Para obter mais exemplos, consulte a [biblioteca de clientes de identidade do Azure para obter](https://www.npmjs.com/package/@azure/identity) documentação JS.

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos funciona como um recipiente para seus arquivos. Você pode criar um recebendo uma instância **FileSystemClient** e, em seguida, chamando o método **FileSystemClient.Create.**

Este exemplo cria um `my-file-system`sistema de arquivos chamado . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório recebendo uma instância **DirectoryClient** e, em seguida, chamando o método **DirectoryClient.create.**

Este exemplo adiciona um `my-directory` diretório nomeado a um sistema de arquivos. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomear ou mover um diretório chamando o método **DirectoryClient.rename.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomeia um subdiretório `my-directory-renamed`para o nome .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Este exemplo move um `my-directory-renamed` diretório nomeado para um subdiretório `my-directory-2`de um diretório chamado . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método **DirectoryClient.delete.**

Este exemplo exclui um `my-directory`diretório chamado .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Gerencie um ACL de diretório

Este exemplo recebe e, em seguida, `my-directory`define a ACL de um diretório chamado . Este exemplo dá ao usuário possuidores permissões de leitura, gravação e execução, dá ao grupo possuinte apenas permissões de leitura e execução e dá a todos os outros acesso de leitura.

> [!NOTE]
> Se o aplicativo autorizar o acesso usando o Azure Active Directory (Azure AD), certifique-se de que o principal de segurança que seu aplicativo usa para autorizar o acesso tenha sido atribuído à [função Deproprietário de dados do Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte o controle de [acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um arquivo para um diretório

Primeiro, leia um arquivo. Este exemplo usa o módulo `fs` Node.js. Em seguida, crie uma referência de arquivo no diretório de destino criando uma instância **FileClient** e, em seguida, chamando o método **FileClient.create.** Faça upload de um arquivo chamando o método **FileClient.append.** Certifique-se de concluir o upload ligando para o método **FileClient.flush.**

Este exemplo envia um arquivo de `my-directory`texto para um diretório chamado .'

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Gerenciar um ACL de arquivo

Este exemplo recebe e, em seguida, `upload-file.txt`define a ACL de um arquivo chamado . Este exemplo dá ao usuário possuidores permissões de leitura, gravação e execução, dá ao grupo possuinte apenas permissões de leitura e execução e dá a todos os outros acesso de leitura.

> [!NOTE]
> Se o aplicativo autorizar o acesso usando o Azure Active Directory (Azure AD), certifique-se de que o principal de segurança que seu aplicativo usa para autorizar o acesso tenha sido atribuído à [função Deproprietário de dados do Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte o controle de [acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Primeiro, crie uma instância **FileSystemClient** que represente o arquivo que você deseja baixar. Use o método **FileSystemClient.read** para ler o arquivo. Então, escreva o arquivo. Este exemplo usa o módulo `fs` Node.js para fazer isso. 

> [!NOTE]
> Este método de baixar um arquivo funciona apenas para aplicativos Node.js. Se você planeja executar seu código em um navegador, consulte a [biblioteca cliente do Azure Storage File Data Lake para arquivo JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) readme para um exemplo de como fazer isso em um navegador. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
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
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada diretório e arquivo `my-directory`que está localizado em um diretório chamado .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Confira também

* [Pacote (Gerenciador de pacotes de nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Dê feedback](https://github.com/Azure/azure-sdk-for-java/issues)