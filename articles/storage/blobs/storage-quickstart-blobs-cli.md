---
title: Início Rápido do Azure – Criar um blob no armazenamento de objeto usando a CLI do Azure | Microsoft Docs
description: Neste início rápido, você verá como usar a CLI do Azure para carregar um blob no Armazenamento do Microsoft Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747936"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Início Rápido: carregar, baixar e listar blobs usando a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode usá-la em seu navegador com o Azure Cloud Shell. Você também pode instalá-lo no Windows, no Linux ou no macOS e executá-lo na linha de comando. Neste guia de início rápido, você aprende a usar a CLI do Azure para carregar e baixar dados para e do Armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para determinar sua versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. É possível organizar grupos de blobs de forma similar àquela em que você organiza os arquivos em pastas no seu computador.

Crie um contêiner para armazenar os blobs com o comando [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Carregar um blob

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os exemplos deste início rápido mostram como trabalhar com blobs de blocos.

Primeiro, crie um arquivo no qual carregar um blob de blocos. Se você está usando o Azure Cloud Shell, use o seguinte comando para criar um arquivo:

```bash
vi helloworld
```

Quando o arquivo abrir, pressione **insert**. Digite *Olá mundo* e pressione **Esc**. Em seguida, digite *:x* e pressione **Enter**.

Nesse exemplo, você carregou um blob no contêiner criado na etapa anterior usando o comando [az storage blob upload](/cli/azure/storage/blob). Não é necessário especificar um caminho de arquivo, pois o arquivo foi criado no diretório raiz:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Essa operação criará o blob se ele ainda não existir e o substituirá se já existir. Carregue quantos arquivos desejar antes de continuar.

Para carregar vários arquivos ao mesmo tempo, você pode usar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs em um contêiner com o comando [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Baixar um blob

Use o comando [az storage blob download](/cli/azure/storage/blob) para baixar o blob que você carregou anteriormente.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com AzCopy

O utilitário [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é outra opção para a transferência de dados programável por script de alto desempenho para o Armazenamento do Azure. Você pode usar o AzCopy para transferir dados para e do armazenamento de Blobs, Arquivo e Tabela.

O exemplo a seguir usa o AzCopy para carregar um arquivo chamado *myfile.txt* para o *contêiner de amostra*. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos no seu grupo de recursos, incluindo a conta de armazenamento que você criou neste guia de início rápido, exclua-o usando o comando [az group delete](/cli/azure/group). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como transferir arquivos entre um sistema de arquivos local e um contêiner no armazenamento do Blob do Azure. Para saber mais sobre como trabalhar com blobs no Armazenamento do Azure, continue o tutorial para trabalhar com Armazenamento de Blobs do Azure.

> [!div class="nextstepaction"]
> [Como: operações de armazenamento de blobs com a CLI do Azure](storage-how-to-use-blobs-cli.md)
