---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536465"
---
Por padrão, as ações de arquivo padrão podem se estender apenas até 5 TiB, embora o limite de ação possa ser aumentado para 100 TiB. Para fazer isso, um grande recurso *de compartilhamento de arquivos* deve ser habilitado no nível da conta de armazenamento. Contas de armazenamento premium (contas de armazenamento*de armazenamento de arquivos)* não têm o grande sinalizador de recurso de compartilhamento de arquivos, pois todas as partes de arquivos premium já estão habilitadas para provisionamento até a capacidade total de 100 TiB.

Você só pode habilitar grandes compartilhamentos de arquivos em contas de armazenamento padrão redundantes localmente redundantes ou redundantes de zona. Depois de habilitar o sinalizador de recurso de compartilhamento de arquivos grande, você não pode alterar o nível de redundância para armazenamento geo-redundante ou geo-zona-redundante.

Para habilitar grandes compartilhamentos de arquivos em uma conta de armazenamento existente, navegue até a **exibição configuração** na tabela de conteúdo da conta de armazenamento e alterne o switch roqueiro de compartilhamento de arquivos grande para habilitado:

![Uma captura de tela do switch roqueiro de compartilhamento de arquivos grandes no portal Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Você também pode habilitar 100 [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) compartilhamentos de arquivos [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) TiB através do cmdlet PowerShell e do comando Azure CLI. Para obter instruções detalhadas sobre como habilitar grandes compartilhamentos de arquivos, consulte [ativar e criar grandes compartilhamentos de arquivos](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Para saber mais sobre como criar compartilhamentos de arquivos em novas contas de armazenamento, consulte [a criação de um compartilhamento de arquivos Do Zure](../articles/storage/files/storage-how-to-create-file-share.md).
