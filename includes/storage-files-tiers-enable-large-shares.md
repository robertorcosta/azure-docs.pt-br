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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536465"
---
Por padrão, os compartilhamentos de arquivos padrão podem abranger até 5 TiB, embora o limite de compartilhamento possa ser aumentado para 100 TiB. Para fazer isso, o recurso de *compartilhamento de arquivos grandes* deve ser habilitado no nível da conta de armazenamento. As contas de armazenamento Premium (contas de armazenamento de*armazenamento* de arquivo) não têm o sinalizador de recurso de compartilhamento de arquivos grandes, já que todos os compartilhamentos de arquivos premium estão habilitados para provisionamento na capacidade total de 100 Tib.

Você só pode habilitar compartilhamentos de arquivos grandes em contas de armazenamento padrão com redundância local ou de zona. Depois de habilitar o sinalizador de recurso de compartilhamento de arquivos grandes, não é possível alterar o nível de redundância para armazenamento com redundância geográfica ou com redundância geográfica.

Para habilitar compartilhamentos de arquivos grandes em uma conta de armazenamento existente, navegue até o modo de exibição de **configuração** no Sumário da conta de armazenamento e alterne a opção de tentativa de compartilhamento de arquivo grande para habilitado:

![Uma captura de tela da opção Habilitar a pedra do compartilhamento de arquivos grandes no portal do Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Você também pode habilitar compartilhamentos de arquivos 100 [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) Tib por meio do [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) cmdlet do PowerShell e o comando CLI do Azure. Para obter instruções detalhadas sobre como habilitar compartilhamentos de arquivos grandes, consulte [habilitar e criar compartilhamentos de arquivos grandes](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Para saber mais sobre como criar compartilhamentos de arquivos em novas contas de armazenamento, consulte [criando um compartilhamento de arquivos do Azure](../articles/storage/files/storage-how-to-create-file-share.md).
