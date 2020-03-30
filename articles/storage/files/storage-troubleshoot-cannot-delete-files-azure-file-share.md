---
title: Compartilhamento de arquivo do Azure – Falha ao excluir arquivos do compartilhamento de arquivo do Azure
description: Identifique e soluciona problemas a falha em excluir arquivos do Azure File Share.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196475"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Compartilhamento de arquivo do Azure – Falha ao excluir arquivos do compartilhamento de arquivo do Azure

A falha na exclusão de arquivos do Azure File Share pode ter vários sintomas:

**Sintoma 1:**

Falha ao excluir um arquivo no compartilhamento de arquivos do Azure devido a um dos dois problemas abaixo:

* O arquivo marcado para exclusão
* O recurso especificado pode estar em uso por um cliente SMB

**Sintoma 2:**

Não há cota suficiente para processar este comando

## <a name="cause"></a>Causa

O erro 1816 ocorre quando você atinge o limite superior de alças abertas simultâneas permitidas para um arquivo, no computador onde o compartilhamento de arquivos está sendo montado. Para obter mais informações, consulte a [lista de verificação de desempenho e escalabilidade do Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Resolução

Reduza o número de alças abertas simultâneas fechando algumas alças.

## <a name="prerequisite"></a>Pré-requisito

### <a name="install-the-latest-azure-powershell-module"></a>Instale o mais recente módulo Azure PowerShell

* [Instale o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Conecte-se ao Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecione a assinatura da conta de armazenamento de destino:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Criar contexto para a conta de armazenamento de destino:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Obtenha as alças abertas atuais do compartilhamento de arquivos:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Resultado de exemplo:

|HandleId|Caminho|ClientIp|ClientPort|Tempo de abertura|LastReconnectTime|FileId|Parentid|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nova pasta/teste.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Feche uma alça aberta:

Para fechar uma alça aberta, use o seguinte comando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)