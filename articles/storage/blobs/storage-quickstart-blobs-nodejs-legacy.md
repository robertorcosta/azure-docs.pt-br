---
title: 'Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure v2 para JavaScript'
description: Crie uma conta de armazenamento e um contêiner no armazenamento de objeto (Blob). Em seguida, use a biblioteca cliente do Armazenamento do Azure para o Node.js v2 para carregar um blob no Armazenamento do Microsoft Azure, fazer o download de um blob e listar os blobs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 12078300b36412a1507991507a39654acdeb7c69
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825338"
---
# <a name="quickstart-azure-blob-storage-client-library-v2-for-javascript"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure v2 para JavaScript

Neste guia prático, você aprende como usar a biblioteca de clientes para Node.js v2 para carregar, baixar e listar blobs com o armazenamento do Azure Blob.

> [!TIP]
> A versão mais recente da biblioteca de clientes do Armazenamento do Azure para Node.js é v10. A Microsoft recomenda que você use a versão mais recente da biblioteca de clientes, quando possível. Para começar a usar o v10, consulte [início rápido: carregar, baixar, listar e excluir BLOBs usando a biblioteca de cliente de armazenamento do Azure para JavaScript v10 (versão prévia)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Crie uma conta de armazenamento do Azure no [portal do Microsoft Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Para obter ajuda sobre como criar a conta, confira [Criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [Aplicativo de amostra](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) é um aplicativo de console Node.js simples. Para começar, clone o repositório para seu computador usando o comando a seguir:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Para abrir o aplicativo, procure a pasta *storage-blobs-node-quickstart*, e abra-a no seu ambiente de edição de código favorito.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Antes de executar o aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. O repositório de exemplo inclui um arquivo chamado *.env.example*. É possível renomear esse arquivo, removendo a extensão *.example*, resultando em um arquivo chamado *.env*. Dentro do arquivo *.env*, adicione o valor da cadeia de conexão após a chave *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Instalar os pacotes necessários

No diretório de aplicativo, execute *npm install* para instalar os pacotes necessários para o aplicativo.

```bash
npm install
```

## <a name="run-the-sample"></a>Execute o exemplo
Agora que as dependências estão instaladas, você pode executar o exemplo emitindo o comando a seguir:

```bash
npm start
```

A saída do script será semelhante ao seguinte:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Se você estiver usando uma nova conta de armazenamento para este exemplo, talvez não veja nenhum nome de contêiner listado sob o rótulo "*Contêineres*".

## <a name="understanding-the-code"></a>Compreender o código
A primeira expressão é usada para carregar valores em variáveis de ambiente.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

O módulo *dotenv* carrega variáveis de ambiente ao executar o aplicativo localmente para depuração. Valores são definidos em um arquivo chamado *.env* e carregados no contexto de execução atual. Em contextos de produção, a configuração do servidor fornece esses valores, e é por isso que esse código é executado somente quando o script não está em execução em um contexto de "produção".

```javascript
const path = require('path');
const storage = require('azure-storage');
```

O propósito desses módulos é o seguinte: 

arquivo denominado *.env* no contexto de execução atual
- *path* é necessário para determinar o caminho absoluto de arquivo do arquivo a ser carregado no armazenamento de blobs
- *azure-storage* é o módulo da [biblioteca de clientes do Armazenamento do Azure](https://docs.microsoft.com/javascript/api/azure-storage) para Node.js

Em seguida, a variável **blobService** é inicializada como uma nova instância do serviço Blob do Azure.

```javascript
const blobService = storage.createBlobService();
```

Na implementação a seguir, cada uma das funções *blobService* é encapsulada em uma *Promise*, que permite o acesso à função *async* do JavaScript e ao operador *await* para simplificar a natureza de retorno de chamada da [API do Armazenamento do Azure](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Quando uma resposta bem-sucedida é retornada para cada função, a promessa é resolvida com dados relevantes, juntamente com uma mensagem específica para a ação.

### <a name="list-containers"></a>Listar contêineres

A função *listContainers* chama [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), que retorna coleções de contêineres em grupos.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

O tamanho dos grupos é configurável por meio de [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Chamar *listContainersSegmented* retorna metadados de blob como uma matriz de instâncias de [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest). Os resultados são retornados em 5.000 lotes de incremento (segmentos). Se houver mais de 5.000 blobs em um contêiner, os resultados incluirão um valor para *continuationToken*. Para listar segmentos subsequentes do contêiner de blob, você pode passar o token de continuação de volta para *listContainersSegment* como o segundo argumento.

### <a name="create-a-container"></a>Criar um contêiner

A função *createContainer* chama [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) e define o nível de acesso apropriado para o blob.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

O segundo parâmetro (*options*) para **createContainerIfNotExists** aceita um valor para [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). O valor *blob* para *publicAccessLevel* especifica que dados de blob específicos são expostos ao público. Essa configuração está em oposição ao nível de acesso de *container*, que oferece a capacidade de listar o conteúdo do contêiner.

O uso de **createContainerIfNotExists** permite que o aplicativo execute o comando *createContainer* várias vezes sem retornar erros quando o contêiner já existir. Em um ambiente de produção, geralmente você só chama **createContainerIfNotExists** uma vez, isso porque o mesmo contêiner é usado em todo o aplicativo. Nesses casos, é possível criar o contêiner antecipadamente por meio do portal ou através da CLI do Azure.

### <a name="upload-text"></a>Carregar texto

A função *uploadString* chama [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) para gravar (ou substituir) uma cadeia de caracteres arbitrária para o contêiner de blob.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Carregar um arquivo local

A função *uploadLocalFile* usa [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) para carregar e gravar (ou substituir) um arquivo do sistema de arquivos no armazenamento de blobs. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Outras abordagens disponíveis para carregar conteúdo em blobs incluem trabalhar com [textos](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) e [transmissões](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Para verificar se o arquivo foi carregado ao seu armazenamento de blobs, você pode usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para exibir os dados em sua conta.

### <a name="list-the-blobs"></a>Listar os blobs

A função *listBlobs* chama o método [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) para retornar uma lista de metadados de blob em um contêiner. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Chamar *listBlobsSegmented* retorna metadados de blob como uma matriz de instâncias [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Os resultados são retornados em 5.000 lotes de incremento (segmentos). Se houver mais de 5.000 blobs em um contêiner, os resultados incluirão um valor para **continuationToken**. Para listar segmentos subsequentes do contêiner de blob, você pode passar o token de continuação de volta para **listBlobSegmented** como o segundo argumento.

### <a name="download-a-blob"></a>Baixar um blob

A função *downloadBlob* usa [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) para baixar o conteúdo do blob para o caminho de arquivo absoluto fornecido.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
A implementação mostrada aqui altera a origem e retorna o conteúdo do blob como uma cadeia de caracteres. Você também pode baixar o blob como um [fluxo](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), bem como diretamente para um [arquivo local](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Excluir um blob

A função *deleteBlob* chama a função [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Como o nome implica, essa função não retorna um erro se o blob já tiver sido excluído.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Excluir um contêiner

Contêineres são excluídos chamando o método *deleteContainer* do serviço Blob e passando o nome do contêiner.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Código de chamada

Para dar suporte à sintaxe *async/await* JavaScript, todo o código de chamada é encapsulado em uma função denominada *execute*. Em seguida, execute é chamado e tratado como uma promessa.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Todo o código a seguir é executado dentro da função execute em que o comentário `// commands` está inserido.

Em primeiro lugar, as variáveis relevantes são declaradas para atribuir nomes e conteúdo de exemplo e para apontar para o arquivo local a ser carregado no armazenamento de Blobs.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Para listar os contêineres na conta de armazenamento, a função listContainers é chamada e a lista retornada de contêineres é registrada para a janela de saída.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Depois que a lista de contêineres estiver disponível, você poderá usar o método *findIndex* de Matriz para ver se o contêiner que você deseja criar já existe. Se o contêiner não existir, ele será criado.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Em seguida, uma cadeia de caracteres e um arquivo local serão carregados para o armazenamento de Blobs.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
O processo para listar os blobs é o mesmo que para listar contêineres. A chamada para *listBlobs* retorna uma matriz de blobs no contêiner e é registrada na janela de saída.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Para baixar um blob, a resposta é capturada e usada para acessar o valor do blob. Da resposta, readableStreamBody é convertido em uma cadeia de caracteres e desconectado da janela de saída.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Por fim, o contêiner e o blob são excluídos da conta de armazenamento.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Limpar recursos
Todos os dados gravados para a conta de armazenamento são excluídos automaticamente no final do exemplo de código. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos Node.js com blobs

Confira estes recursos adicionais para o desenvolvimento em Node.js com armazenamento de Blobs:

### <a name="binaries-and-source-code"></a>Binários e código-fonte

- Exiba e instale o [código-fonte da biblioteca do cliente Node.js](https://github.com/Azure/azure-storage-node) para o Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Exemplos e referência da biblioteca de clientes

- Confira a [referência da API Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage) para obter mais informações sobre a biblioteca de clientes do Node.js.
- Explore [exemplos de armazenamento de Blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) gravados usando a biblioteca de clientes do Node.js.

## <a name="next-steps"></a>Próximas etapas

Este artigo demonstra como carregar um arquivo entre um disco local e o armazenamento de Blob do Azure usando o Node.js. Para saber mais sobre como trabalhar com o armazenamento de Blobs, prossiga para o repositório do GitHub.

> [!div class="nextstepaction"]
> [SDK do Armazenamento do Microsoft Azure para Node.js e JavaScript para navegadores](https://github.com/Azure/azure-storage-node)
