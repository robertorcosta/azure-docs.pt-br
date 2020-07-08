---
title: Habilitar exclusão reversível – Compartilhamentos de arquivo do Azure
description: Saiba como habilitar a exclusão reversível (visualização) em compartilhamentos de arquivos do Azure para recuperação de dados e impedir a exclusão acidental.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 84da8993bd1b0a61ef885d03f7c2bc80b92d47cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510822"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Habilitar exclusão reversível nos compartilhamentos de arquivo do Azure

O armazenamento do Azure oferece exclusão reversível para compartilhamentos de arquivos (versão prévia) para que você possa recuperar seus dados com mais facilidade quando eles forem excluídos erroneamente por um aplicativo ou outro usuário da conta de armazenamento. Para saber mais sobre a exclusão reversível, confira [Como impedir a exclusão acidental de compartilhamentos de arquivo do Azure](storage-files-prevent-file-share-deletion.md).

As seguintes seções mostram como habilitar e usar a exclusão reversível para compartilhamentos de arquivo do Azure em uma conta de armazenamento existente:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Navegue até sua conta de armazenamento e selecione **Exclusão reversível** em **Serviço de arquivo**.
1. Selecione **Habilitado** para **exclusão reversível de compartilhamento de arquivo**.
1. Selecione **Período de retenção do compartilhamento de arquivo em dias** e insira um número de sua escolha.
1. Selecione **Salvar** para confirmar suas configurações de retenção de dados.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Captura de tela do painel de configurações de exclusão reversível da conta de armazenamento. Realçando a seção compartilhamentos de arquivo, habilite a alternância, defina um período de retenção e salve. Isso habilitará a exclusão reversível para todos os compartilhamentos de arquivos em sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

1. Navegue até sua conta de armazenamento e selecione **Compartilhamentos de arquivo**.
1. Na folha do compartilhamento de arquivo, habilite **Mostrar compartilhamentos excluídos** para exibir os compartilhamentos que foram excluídos de modo reversível.

    Isso exibirá todos os compartilhamentos atualmente em um estado **Excluído**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Se a coluna status, a coluna ao lado da coluna nome, estiver definida como Excluído, o compartilhamento de arquivo estará em um estado excluído de modo reversível. Ele será excluído permanentemente após o período de retenção especificado por você.":::

1. Selecione o compartilhamento e selecione **cancelar exclusão**, o que vai restaurá-lo.

    Você pode confirmar se o compartilhamento foi restaurado, pois o status dele muda para **Ativo**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Se a coluna status, a coluna ao lado da coluna nome, estiver definida como Ativo, o compartilhamento de arquivo terá sido restaurado.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para restaurar um compartilhamento de arquivo excluído de modo reversível, use o seguinte comando:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Desabilitar exclusão reversível

Se você quiser parar de usar a exclusão reversível ou excluir permanentemente um compartilhamento de arquivo, siga estas instruções:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até sua conta de armazenamento e selecione **Exclusão reversível** em **Configurações**.
1. Em **Compartilhamentos de arquivo**, selecione **Desabilitado** para **Exclusão reversível para compartilhamentos de arquivo**.
1. Selecione **Salvar** para confirmar suas configurações de retenção de dados.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Desabilitar a exclusão reversível permitirá que você exclua imediatamente e permanentemente todos os compartilhamentos de arquivo em sua conta de armazenamento sempre que quiser.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Você pode usar o seguinte comando para desabilitar a exclusão reversível em sua conta de armazenamento:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre uma outra forma de proteção e recuperação de dados, consulte nosso artigo [visão geral de instantâneos de compartilhamento para arquivos do Azure](storage-snapshots-files.md).
