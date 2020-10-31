---
title: Habilitar exclusão reversível – Compartilhamentos de arquivo do Azure
description: Saiba como habilitar a exclusão reversível nos compartilhamentos de arquivo do Azure para recuperação de dados e impedir a exclusão acidental.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126134"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Habilitar exclusão reversível nos compartilhamentos de arquivo do Azure

O Armazenamento do Azure agora oferece a exclusão reversível para compartilhamentos de arquivo para que você possa recuperar os dados mais facilmente quando eles forem excluídos erroneamente por outro usuário de conta de armazenamento ou um aplicativo. Para saber mais sobre a exclusão reversível, confira [Como impedir a exclusão acidental de compartilhamentos de arquivo do Azure](storage-files-prevent-file-share-deletion.md).

As seguintes seções mostram como habilitar e usar a exclusão reversível para compartilhamentos de arquivo do Azure em uma conta de armazenamento existente:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Introdução

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Navegue até sua conta de armazenamento e selecione **Exclusão reversível** em **Serviço de arquivo** .
1. Selecione **Habilitado** para **exclusão reversível de compartilhamento de arquivo** .
1. Selecione **Período de retenção do compartilhamento de arquivo em dias** e insira um número de sua escolha.
1. Selecione **Salvar** para confirmar suas configurações de retenção de dados.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Captura de tela do painel de configurações de exclusão reversível da conta de armazenamento. Realçando a seção compartilhamentos de arquivo, habilite a alternância, defina um período de retenção e salve. Isso habilitará a exclusão reversível para todos os compartilhamentos de arquivos em sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Pré-requisito

Os cmdlets de exclusão reversível estão disponíveis na versão [3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0) do módulo AZ. Storage. 

## <a name="getting-started-with-powershell"></a>Introdução ao PowerShell

Para habilitar a exclusão reversível, você precisa atualizar as propriedades do serviço do cliente de um arquivo. O seguinte exemplo habilita a exclusão reversível para todos os compartilhamentos de arquivo em uma conta de armazenamento:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Você pode verificar se a exclusão reversível está habilitada e ver a respectiva política de retenção com o seguinte comando:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Restaurar compartilhamento de arquivo com exclusão reversível

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para restaurar um compartilhamento de arquivo com exclusão reversível:

1. Navegue até sua conta de armazenamento e selecione **Compartilhamentos de arquivo** .
1. Na folha do compartilhamento de arquivo, habilite **Mostrar compartilhamentos excluídos** para exibir os compartilhamentos que foram excluídos de modo reversível.

    Isso exibirá todos os compartilhamentos atualmente em um estado **Excluído** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Captura de tela do painel de configurações de exclusão reversível da conta de armazenamento. Realçando a seção compartilhamentos de arquivo, habilite a alternância, defina um período de retenção e salve. Isso habilitará a exclusão reversível para todos os compartilhamentos de arquivos em sua conta de armazenamento.":::

1. Selecione o compartilhamento e selecione **cancelar exclusão** , o que vai restaurá-lo.

    Você pode confirmar se o compartilhamento foi restaurado, pois o status dele muda para **Ativo** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Captura de tela do painel de configurações de exclusão reversível da conta de armazenamento. Realçando a seção compartilhamentos de arquivo, habilite a alternância, defina um período de retenção e salve. Isso habilitará a exclusão reversível para todos os compartilhamentos de arquivos em sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de exclusão reversível estão disponíveis na versão 3.0.0 do módulo AZ. Storage. Para restaurar um compartilhamento de arquivo excluído de modo reversível, use o seguinte comando:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Desabilitar exclusão reversível

Se você quiser parar de usar a exclusão reversível ou excluir permanentemente um compartilhamento de arquivo, siga estas instruções:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até sua conta de armazenamento e selecione **Exclusão reversível** em **Configurações** .
1. Em **Compartilhamentos de arquivo** , selecione **Desabilitado** para **Exclusão reversível para compartilhamentos de arquivo** .
1. Selecione **Salvar** para confirmar suas configurações de retenção de dados.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Captura de tela do painel de configurações de exclusão reversível da conta de armazenamento. Realçando a seção compartilhamentos de arquivo, habilite a alternância, defina um período de retenção e salve. Isso habilitará a exclusão reversível para todos os compartilhamentos de arquivos em sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de exclusão reversível estão disponíveis na versão 3.0.0 do módulo AZ. Storage. Você pode usar o seguinte comando para desabilitar a exclusão reversível em sua conta de armazenamento:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre uma outra forma de proteção e recuperação de dados, consulte nosso artigo [visão geral de instantâneos de compartilhamento para arquivos do Azure](storage-snapshots-files.md).
