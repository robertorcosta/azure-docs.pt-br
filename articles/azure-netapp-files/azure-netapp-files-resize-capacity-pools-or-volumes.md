---
title: Redimensionar o pool de capacidade ou um volume para o Azure NetApp Files | Microsoft Docs
description: Descreve como alterar o tamanho de um volume ou de um pool de capacidade.
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
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794625"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um volume ou um pool de capacidade
Você pode alterar o tamanho de um pool de capacidade ou de um volume, conforme necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensionar o pool de capacidade 

Você pode alterar o tamanho da piscina de capacidade em incrementos ou decrésões de 1-TiB. No entanto, o tamanho da piscina de capacidade não pode ser menor do que 4 TiB. Redimensionar o pool de capacidade altera a capacidade adquirida do Azure NetApp Files.

1. Na folha Gerenciar Conta do NetApp, clique no pool de capacidade que você deseja redimensionar. 
2. Clique com o botão direito do mouse no nome do pool de capacidade ou clique no ícone "..." no final da linha do pool de capacidade para exibir o menu de contexto. 
3. Use as opções de menu de contexto para redimensionar ou excluir o pool de capacidade.

## <a name="resize-a-volume"></a>Redimensionar um volume

Você pode alterar o tamanho de um volume conforme necessário. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume.

1. Na folha Gerenciar Conta do NetApp, clique em **Volumes**. 
2. Clique com o botão direito do mouse no nome do volume que você deseja redimensionar ou clique no ícone "..." no final da linha do volume para exibir o menu de contexto.
3. Use as opções de menu de contexto para redimensionar ou excluir o volume.

