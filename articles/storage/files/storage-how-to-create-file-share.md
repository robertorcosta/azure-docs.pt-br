---
title: Como criar um compartilhamento de arquivos do Azure | Microsoft Docs
description: Como criar um compartilhamento de arquivos do Azure em arquivos do Azure usando o portal do Azure, o PowerShell e o CLI do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3b30df7410f56c203eb600089cce130a8de23d1b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514611"
---
# <a name="create-a-file-share-in-azure-files"></a>Criar um compartilhamento de arquivos em arquivos do Azure
Você pode criar compartilhamentos de arquivos do Azure usando o [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell de armazenamento do Azure, as bibliotecas de cliente de armazenamento do Azure ou a API REST do armazenamento do Azure. Neste tutorial, você aprenderá a:
* Como criar um compartilhamento de arquivos do Azure usando o portal do Azure
* [Como criar um compartilhamento de arquivos do Azure usando o PowerShell](#create-file-share-through-powershell)
* [Como criar um compartilhamento de arquivos do Azure usando a CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Pré-requisitos
Para criar um compartilhamento de arquivos do Azure, você pode usar uma conta de armazenamento que já existe ou [criar uma nova conta de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar um compartilhamento de arquivos do Azure com o PowerShell, você precisará da chave de conta e do nome da sua conta de armazenamento. Você precisará de uma chave de conta de armazenamento se planeja usar o PowerShell ou a CLI.

> [!NOTE]
> Se você quiser criar compartilhamentos de arquivos maiores que 5 TiB, consulte nosso artigo [habilitar compartilhamentos de arquivos grandes](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Criar um compartilhamento de arquivos por meio do portal do Azure
1. **Acesse a folha da conta de armazenamento no portal do Azure**:    
    folha de conta de ![Storage ](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Clique no botão Adicionar compartilhamento de arquivos**:    
    ![Click botão Adicionar compartilhamento de arquivos ](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Forneça o nome e a cota**:    
    ![Provide um nome e uma cota desejada para o novo compartilhamento de arquivos ](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Exiba seu novo compartilhamento de arquivos**: ![View seu novo compartilhamento de arquivos ](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Carregar um arquivo**: ![Upload um arquivo ](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navegue até o compartilhamento de arquivos e gerencie seus diretórios e arquivos**: ![Browse compartilhamento de arquivos ](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Criar compartilhamento de arquivos por meio do PowerShell
Para se preparar para usar o PowerShell, baixe e instale os cmdlets Azure PowerShell. Consulte [como instalar e configurar Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)  for as instruções de instalação e ponto de instalação.

> [!Note]  
> É recomendável que você baixe e instale ou atualize para o módulo de Azure PowerShell mais recente.

1. **Criar uma nova conta de armazenamento:** Uma conta de armazenamento é um pool compartilhado de armazenamento no qual você pode implantar compartilhamentos de arquivos do Azure, bem como outros recursos de armazenamento, como BLOBs ou filas.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

2. **Criar um novo compartilhamento de arquivos**:    
    
    ```powershell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> O nome do seu compartilhamento de arquivos deve estar em letras minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, consulte [nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Criar compartilhamento de arquivos por meio da CLI (interface de linha de comando)
1. **Para se preparar para usar a CLI (interface de linha de comando), baixe e instale o CLI do Azure.**  
    Consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [introdução ao CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Crie uma cadeia de conexão para a conta de armazenamento na qual você deseja criar o compartilhamento.**  
    Substitua  ```<storage-account>```  and  ```<resource_group>```  with o nome da conta de armazenamento e o grupo de recursos no exemplo a seguir:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Criar o compartilhamento de arquivos**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Próximos passos
* [Conectar e montar o compartilhamento de arquivos-Windows](storage-how-to-use-files-windows.md)
* [Conectar e montar o compartilhamento de arquivos-Linux](../storage-how-to-use-files-linux.md)
* [Conectar e montar compartilhamento de arquivos – macOS](storage-how-to-use-files-mac.md)

Consulte estes links para obter mais informações sobre os arquivos do Azure.

* [PERGUNTAS FREQUENTES](../storage-files-faq.md)
* [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solução de problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)   
