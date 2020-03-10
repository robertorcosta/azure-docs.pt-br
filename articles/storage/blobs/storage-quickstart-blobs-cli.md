---
title: Início rápido – Criar um blob com a CLI do Azure
titleSuffix: Azure Storage
description: Neste início rápido, você verá como usar a CLI do Azure para carregar um blob no Armazenamento do Microsoft Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 58532c6eb6e01a993bce7d6e22a7b5274a465963
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298126"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Início Rápido: Criar, baixar e listar blobs com a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode usá-la em seu navegador com o Azure Cloud Shell. Você também pode instalá-lo no Windows, no Linux ou no macOS e executá-lo na linha de comando. Neste guia de início rápido, você aprende a usar a CLI do Azure para carregar e baixar dados para e do Armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instalar a CLI do Azure localmente

Se você optar por instalar e usar a CLI do Azure localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para determinar sua versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Se você estiver executando a CLI do Azure localmente, precisará fazer logon e se autenticar. Esta etapa não será necessária se você estiver usando o Azure Cloud Shell. Para fazer logon na CLI do Azure, execute `az login` e autentique-se na janela do navegador:

```azurecli
az login
```

Para obter mais informações sobre a autenticação com a CLI do Azure, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Autorizar o acesso ao Armazenamento de Blobs

Você pode autorizar o acesso ao Armazenamento de Blobs por meio da CLI do Azure com as credenciais do Azure AD ou usando a chave de acesso da conta de armazenamento. Recomendamos usar as credenciais do Azure AD. Este artigo mostra como autorizar operações do Armazenamento de Blobs usando o Azure AD.

Os comandos da CLI do Azure para operações de dados no Armazenamento de Blobs dão suporte ao parâmetro `--auth-mode`, que permite especificar como autorizar determinada operação. Defina o parâmetro `--auth-mode` como `login` para autorização com as credenciais do Azure AD. Para obter mais informações, confira [Autorizar o acesso a dados de blob ou de filas com a CLI do Azure](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Somente as operações de dados do Armazenamento de Blobs dão suporte ao parâmetro `--auth-mode`. As operações de gerenciamento, como a criação de um grupo de recursos ou uma conta de armazenamento, usam automaticamente as credenciais do Azure AD para autorização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento de uso geral com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento de uso geral pode ser usada para todos os quatro serviços: blobs, arquivos, tabelas e filas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. Você pode organizar grupos de blobs em contêineres de modo similar à maneira como organiza arquivos do computador em pastas.

Crie um contêiner para armazenar os blobs com o comando [az storage container create](/cli/azure/storage/container). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Carregar um blob

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os exemplos deste início rápido mostram como trabalhar com blobs de blocos.

Primeiro, crie um arquivo no qual carregar um blob de blocos. Se você está usando o Azure Cloud Shell, use o seguinte comando para criar um arquivo:

```bash
vi helloworld
```

Quando o arquivo abrir, pressione **insert**. Digite *Olá mundo* e pressione **Esc**. Em seguida, digite *:x* e pressione **Enter**.

Nesse exemplo, você carregou um blob no contêiner criado na etapa anterior usando o comando [az storage blob upload](/cli/azure/storage/blob). Não é necessário especificar um caminho de arquivo, pois o arquivo foi criado no diretório raiz. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Essa operação criará o blob se ele ainda não existir e o substituirá se já existir. Carregue quantos arquivos desejar antes de continuar.

Para carregar vários arquivos ao mesmo tempo, você pode usar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs em um contêiner com o comando [az storage blob list](/cli/azure/storage/blob). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Baixar um blob

Use o comando [az storage blob download](/cli/azure/storage/blob) para baixar o blob que você carregou anteriormente. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com AzCopy

O utilitário de linha de comando AzCopy oferece transferência de dados de alto desempenho e programável para o Armazenamento do Azure. Use o AzCopy para transferir dados bidirecionalmente no Armazenamento de Blobs e nos Arquivos do Azure. Para obter mais informações sobre o AzCopy v10, a última versão do AzCopy, confira [Introdução ao AzCopy](../common/storage-use-azcopy-v10.md). Para saber mais sobre como usar o AzCopy v10 com o Armazenamento de Blobs, confira [Transferir dados com o AzCopy e o Armazenamento de Blobs](../common/storage-use-azcopy-blobs.md).

O exemplo a seguir usa o AzCopy para carregar um arquivo local em um blob. Lembre-se de substituir os valores de exemplo pelos próprios valores:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje excluir os recursos criados como parte deste início rápido, incluindo a conta de armazenamento, exclua o grupo de recursos usando o comando [az group delete](/cli/azure/group). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como transferir arquivos entre um sistema de arquivos local e um contêiner no armazenamento do Blob do Azure. Para saber mais sobre como trabalhar com blobs no Armazenamento do Azure, continue o tutorial para trabalhar com Armazenamento de Blobs do Azure.

> [!div class="nextstepaction"]
> [Como: operações de armazenamento de blobs com a CLI do Azure](storage-how-to-use-blobs-cli.md)
