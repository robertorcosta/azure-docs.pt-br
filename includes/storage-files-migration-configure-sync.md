---
title: Configuração do Azure File Sync
description: Configure Azure File Sync. Um bloco de texto comum, compartilhado entre os docs de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209578"
---
Esta etapa une todos os recursos e pastas que você configurou no seu Windows Server durante as etapas anteriores.

* Inscreva-se no [portal Azure](https://portal.azure.com).
* Localize o recurso Storage Sync Service.
* Crie um novo *grupo de sincronização* no recurso Storage Sync Service para cada compartilhamento de arquivos do Azure. Na terminologia do Azure File Sync, o compartilhamento de arquivos Do Zure se tornará um *ponto final* na topologia de sincronização que você está descrevendo com a criação de um grupo de sincronização. Como você está criando o grupo de sincronização, dê-lhe um nome familiar, de tal forma que você reconheça qual conjunto de arquivos sincroniza aqui. Certifique-se de fazer referência ao compartilhamento de arquivos Azure com um nome correspondente.
* Uma vez que o grupo de sincronização é criado, você verá uma linha para ele aparecer na lista de grupos de sincronização. Clique no nome (um link) para exibir o conteúdo do grupo de sincronização. Você verá o compartilhamento de arquivos do Azure em "Pontos finais da nuvem".
* Localize o botão de comando para *+ Adicionar ponto final do servidor*. A pasta no servidor local que você provisionou, se tornará o caminho para este *ponto final do servidor*.
