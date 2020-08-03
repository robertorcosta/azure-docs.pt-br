---
title: Redimensionar o pool de capacidade ou um volume para o Azure NetApp Files | Microsoft Docs
description: Saiba como alterar o tamanho de um pool de capacidade ou um volume. Redimensionar o pool de capacidade altera a capacidade adquirida do Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7d1c54d05430600e9fffa2659dbaaf6db0b086b5
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512866"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um volume ou um pool de capacidade
Você pode alterar o tamanho de um pool de capacidade ou de um volume, conforme necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensionar o pool de capacidade 

Você pode alterar o tamanho do pool de capacidade em incrementos de 1 TiB ou decrementas. No entanto, o tamanho do pool de capacidade não pode ser inferior a 4 TiB. Redimensionar o pool de capacidade altera a capacidade adquirida do Azure NetApp Files.

1. Na folha Gerenciar Conta do NetApp, clique no pool de capacidade que você deseja redimensionar. 
2. Clique com o botão direito do mouse no nome do pool de capacidade ou clique no ícone "..." no final da linha do pool de capacidade para exibir o menu de contexto. 
3. Use as opções de menu de contexto para redimensionar ou excluir o pool de capacidade.

## <a name="resize-a-volume"></a>Redimensionar um volume

Você pode alterar o tamanho de um volume conforme necessário. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume.

1. Na folha Gerenciar Conta do NetApp, clique em **Volumes**. 
2. Clique com o botão direito do mouse no nome do volume que você deseja redimensionar ou clique no ícone "..." no final da linha do volume para exibir o menu de contexto.
3. Use as opções de menu de contexto para redimensionar ou excluir o volume.

