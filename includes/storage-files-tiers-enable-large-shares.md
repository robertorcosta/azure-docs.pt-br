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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597756"
---
Por padrão, os compartilhamentos de arquivos padrão podem abranger até 5 TiB, embora o limite de compartilhamento possa ser aumentado para 100 TiB. Para fazer isso, o recurso de *compartilhamento de arquivos grandes* deve ser habilitado no nível da conta de armazenamento. As contas de armazenamento Premium (contas de armazenamento de*armazenamento* de arquivo) não têm o sinalizador de recurso de compartilhamento de arquivos grandes, já que todos os compartilhamentos de arquivos premium estão habilitados para provisionamento na capacidade total de 100 Tib.

Você só pode habilitar compartilhamentos de arquivos grandes em contas de armazenamento padrão com redundância local ou de zona. Depois de habilitar o sinalizador de recurso de compartilhamento de arquivos grandes, não é possível alterar o nível de redundância para armazenamento com redundância geográfica ou com redundância geográfica.

Para habilitar compartilhamentos de arquivos grandes em uma conta de armazenamento existente, navegue até o modo de exibição de **configuração** no Sumário da conta de armazenamento e alterne a opção de tentativa de compartilhamento de arquivo grande para habilitado:

![Uma captura de tela da opção Habilitar a pedra do compartilhamento de arquivos grandes no portal do Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Você também pode habilitar compartilhamentos de arquivos 100 TiB por meio do cmdlet [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell e o comando [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) CLI do Azure.

Para saber mais sobre como habilitar grandes compartilhamentos de arquivos em novas contas de armazenamento, consulte [criando um compartilhamento de arquivos do Azure](../articles/storage/files/storage-how-to-create-file-share.md).