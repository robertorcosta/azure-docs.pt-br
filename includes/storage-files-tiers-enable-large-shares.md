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
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597756"
---
Por padrão, as ações de arquivo padrão podem se estender apenas até 5 TiB, embora o limite de ação possa ser aumentado para 100 TiB. Para fazer isso, um grande recurso *de compartilhamento de arquivos* deve ser habilitado no nível da conta de armazenamento. Contas de armazenamento premium (contas de armazenamento*de armazenamento de arquivos)* não têm o grande sinalizador de recurso de compartilhamento de arquivos, pois todas as partes de arquivos premium já estão habilitadas para provisionamento até a capacidade total de 100 TiB.

Você só pode habilitar grandes compartilhamentos de arquivos em contas de armazenamento padrão redundantes localmente redundantes ou redundantes de zona. Depois de habilitar o sinalizador de recurso de compartilhamento de arquivos grande, você não pode alterar o nível de redundância para armazenamento geo-redundante ou geo-zona-redundante.

Para habilitar grandes compartilhamentos de arquivos em uma conta de armazenamento existente, navegue até a **exibição configuração** na tabela de conteúdo da conta de armazenamento e alterne o switch roqueiro de compartilhamento de arquivos grande para habilitado:

![Uma captura de tela do switch roqueiro de compartilhamento de arquivos grandes no portal Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Você também pode habilitar 100 [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) compartilhamentos de arquivos [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) TiB através do cmdlet PowerShell e do comando Azure CLI.

Para saber mais sobre como ativar grandes compartilhamentos de arquivos em novas contas de armazenamento, consulte [a criação de um compartilhamento de arquivos Do Zure](../articles/storage/files/storage-how-to-create-file-share.md).