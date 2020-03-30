---
title: Verifique o status de criptografia de uma bolha - Armazenamento Azure
description: Saiba como usar o portal Azure, o PowerShell ou o Azure CLI para verificar se uma determinada bolha está criptografada. Se uma bolha não estiver criptografada, aprenda a usar o AzCopy para forçar a criptografia baixando e recarregando o blob.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707590"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Verifique o status de criptografia de uma bolha

Cada bolha de bloco, bolha de apêndice ou bolha de página que foi escrita no Azure Storage após 20 de outubro de 2017 é criptografada com criptografia de armazenamento Azure. Blobs criados antes desta data continuam a ser criptografados por um processo em segundo plano.

Este artigo mostra como determinar se uma determinada bolha foi criptografada.

## <a name="check-a-blobs-encryption-status"></a>Verifique o status de criptografia de uma bolha

Use o portal Azure, PowerShell ou Azure CLI para determinar se uma bolha é criptografada sem código.

### <a name="azure-portal"></a>[Portal Azure](#tab/portal)

Para usar o portal Azure para verificar se uma bolha foi criptografada, siga estas etapas:

1. No portal do Azure, navegue até sua conta de armazenamento.
1. Selecione **Contêineres** para navegar até uma lista de contêineres na conta.
1. Localize a bolha e exiba sua **guia Visão Geral.**
1. Exibir a propriedade **Server Encrypted.** Se **True**, como mostrado na imagem a seguir, então a bolha é criptografada. Observe que as propriedades da bolha também incluem a data e a hora em que a bolha foi criada.

    ![Captura de tela mostrando como verificar a propriedade criptografada do Servidor no portal Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

Para usar o PowerShell para verificar se uma bolha foi criptografada, verifique a propriedade **IsServerEncrypted** da blob. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Para determinar quando a bolha foi criada, verifique o valor da propriedade **Criada:**

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Para usar o Azure CLI para verificar se uma bolha foi criptografada, verifique a propriedade **IsServerEncrypted** da blob. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Para determinar quando a bolha foi criada, verifique o valor da propriedade **criada.**

---

## <a name="force-encryption-of-a-blob"></a>Forçar a criptografia de uma bolha

Se uma bolha que foi criada antes de 20 de outubro de 2017 ainda não foi criptografada pelo processo de fundo, você pode forçar a criptografia a ocorrer imediatamente baixando e recarregando a bolha. Uma maneira simples de fazer isso é com o AzCopy.

Para baixar uma bolha no sistema de arquivos local com o AzCopy, use a seguinte sintaxe:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Para recarregar a bolha no Azure Storage com o AzCopy, use a seguinte sintaxe:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Para obter mais informações sobre o uso do AzCopy para copiar dados blob, consulte [Transferir dados com armazenamento AzCopy e Blob](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Próximas etapas

[Criptografia de armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
