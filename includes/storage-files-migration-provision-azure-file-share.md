---
title: Considerações para o provisionamento de ações de arquivos Do Zure.
description: Provision Azure compartilha arquivos para uso com o Azure File Sync. Um bloco de texto comum, compartilhado entre os docs de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209422"
---
Um compartilhamento de arquivos Do Zure é armazenado na nuvem em uma conta de armazenamento do Azure.
Há outro nível de considerações de desempenho aqui.

Se você tiver ações altamente ativas - ações usadas por muitos usuários e/ou aplicativos, então duas ações de arquivo do Azure podem atingir o limite de desempenho de uma conta de armazenamento.

A prática recomendada é implantar contas de armazenamento com um compartilhamento de arquivos cada.
Você pode juntar vários compartilhamentos de arquivos do Azure na mesma conta de armazenamento, caso você tenha ações de arquivamento ou espere baixa atividade diária neles.

Essas considerações se aplicam mais ao acesso direto à nuvem (através de uma VM do Azure) do que se aplicam ao Azure File Sync. Se você planeja usar apenas o Azure File Sync nessas ações, então agrupar vários em uma única conta de armazenamento Azure é bom.

Se você fez uma lista de suas ações, então você deve mapear cada ação para a conta de armazenamento em que eles residirão.

Na fase anterior, você determinou o número adequado de ações. Nesta etapa, você criou um mapeamento de contas de armazenamento para arquivar compartilhamentos. Implante o número agora apropriado de contas de armazenamento Do Zure com o número apropriado de compartilhamentos de arquivos Do Azure neles.

Certifique-se de que a região de cada uma de suas contas de armazenamento é a mesma e corresponde à região do recurso Storage Sync Service que você já implantou.

> [!CAUTION]
> Se você criar um compartilhamento de arquivo Azure com limite de 100 TiB, esse compartilhamento só poderá usar opções de redundância de armazenamento redundante localmente redundantes. Considere suas necessidades de redundância de armazenamento antes de usar 100 compartilhamentos de arquivos TiB.

As partes de arquivos do Azure ainda são criadas com um limite de 5 TiB por padrão. Uma vez que você está criando novas contas de armazenamento, certifique-se de seguir a [orientação para criar contas de armazenamento que permitem compartilhamentos de arquivos Do Zure com 100 limites de TiB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Outra consideração ao implantar uma conta de armazenamento é a redundância do seu armazenamento Azure. Veja: [Opções de redundância do Azure Storage](../articles/storage/common/storage-redundancy.md).

Os nomes de seus recursos também são importantes. Por exemplo, se você agrupar várias ações para o departamento de RH em uma conta de armazenamento do Azure, você deve nomear a conta de armazenamento apropriadamente. Da mesma forma, ao nomear seus compartilhamentos de arquivos Do Zure, você deve usar nomes semelhantes aos usados para suas contrapartes no local.