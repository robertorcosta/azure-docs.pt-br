---
title: Como criar um compartilhamento de Arquivos do Azure | Microsoft Docs
description: Como criar um compartilhamento de arquivos do Azure nos Arquivos do Azure usando o portal do Azure, PowerShell e a CLI do Azure.
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
# <a name="create-a-file-share-in-azure-files"></a>Criar um compartilhamento de arquivos nos Arquivos do Azure
Você pode criar compartilhamentos de Arquivos do Azure usando o  [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell do Armazenamento do Azure, as bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Neste tutorial, você irá aprender:
* Como criar um compartilhamento de arquivo do Azure usando o portal do Azure
* [Como criar um compartilhamento de arquivos do Azure usando o Powershell](#create-file-share-through-powershell)
* [Como criar um compartilhamento de arquivos do Azure usando a CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Pré-requisitos
Para criar um compartilhamento de arquivos do Azure, você pode usar uma Conta de Armazenamento que já existe ou [criar uma nova Conta de Armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar um compartilhamento de arquivos do Azure com o PowerShell, serão necessários a chave da conta e o nome de sua conta de armazenamento. Você precisará da Chave da conta de armazenamento caso planeje usar o Powershell ou a CLI.

> [!NOTE]
> Se você quiser criar compartilhamentos de arquivos maiores que 5 TiB, consulte nosso artigo [habilitar compartilhamentos de arquivos grandes](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Criar um compartilhamento de arquivos por meio do portal do Azure
1. **Vá até a folha Conta de Armazenamento no portal do Azure**:    
    ![Folha Conta de Armazenamento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Clique no botão Adicionar Compartilhamento de arquivos**:    
    ![Clique no botão adicionar compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Forneça o nome e a cota**:    
    ![Forneça um nome e uma cota desejada para o novo compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Exiba o novo compartilhamento de arquivos**: ![Exiba o novo compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Carregue um arquivo**: ![Carregue um arquivo](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navegue o compartilhamento de arquivos e gerencie seus diretórios e arquivos**: ![Navegue o compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Criar um compartilhamento de arquivos com o PowerShell
Para se preparar para usar o PowerShell, baixe e instale os cmdlets do PowerShell do Azure. Confira  [Como instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)  para obter o ponto e as instruções de instalação.

> [!Note]  
> É recomendável baixar e instalar ou atualizar para o módulo mais recente do PowerShell do Azure.

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
> O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira  [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Criar um compartilhamento de arquivos com a Interface da Linha de Comando (CLI)
1. **Para se preparar para usar a Interface da Linha de Comando (CLI), baixe e instale a CLI do Azure.**  
    Confira  [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Crie uma cadeia de conexão para a conta de armazenamento na qual você deseja criar o compartilhamento.**  
    Substitua ```<storage-account>``` e ```<resource_group>``` pelo nome da conta de armazenamento e o grupo de recursos no exemplo a seguir:

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

## <a name="next-steps"></a>Próximas etapas
* [Conecte e Monte o Compartilhamento de Arquivos - Windows](storage-how-to-use-files-windows.md)
* [Conecte e Monte o Compartilhamento de Arquivos - Linux](../storage-how-to-use-files-linux.md)
* [Conecte e Monte o Compartilhamento de Arquivos - macOS](storage-how-to-use-files-mac.md)

Veja estes links para obter mais informações sobre o Arquivos do Azure.

* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solução de problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)   
