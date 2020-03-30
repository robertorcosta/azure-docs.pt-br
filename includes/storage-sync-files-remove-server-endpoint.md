---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391550"
---
Não: remover um ponto final do servidor não é como reiniciar um servidor! Remover e recriar o ponto final do servidor quase nunca é uma solução apropriada para corrigir problemas com sincronização, hierarquamento na nuvem ou outros aspectos do Azure File Sync. Remover um ponto final do servidor é uma operação destrutiva. Isso pode resultar em perda de dados no caso de que os arquivos hierárquicos existem fora do espaço de nome do ponto final do servidor. Veja [por que existem arquivos hierárquicos fora do espaço de nome](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) do ponto final do servidor para obter mais informações. Ou pode resultar em arquivos inacessíveis para arquivos hierárquicos que existem dentro do espaço de nome do ponto final do servidor. Esses problemas não serão resolvidos quando o ponto final do servidor for recriado. Arquivos em camadas podem existir em seu ponto de extremidade do servidor, mesmo se você nunca tiver habilitado nuvem em camadas. É por isso que recomenda que você não remova o ponto final do servidor, a menos que você gostaria de parar de usar o Azure File Sync com esta pasta em particular ou ter sido explicitamente instruído a fazê-lo por um engenheiro da Microsoft. Para obter mais informações sobre remover pontos de extremidade do servidor, consulte [remover um ponto de extremidade do servidor](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
