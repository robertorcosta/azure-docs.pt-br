---
title: Configurando Sincronização de Arquivos do Azure
description: Configurar Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209578"
---
Esta etapa reúne todos os recursos e pastas que você configurou no Windows Server durante as etapas anteriores.

* Faça logon no [Portal do Azure](https://portal.azure.com).
* Localize o recurso do serviço de sincronização de armazenamento.
* Crie um novo *grupo de sincronização* dentro do recurso de serviço de sincronização de armazenamento para cada compartilhamento de arquivos do Azure. Na terminologia Sincronização de Arquivos do Azure, o compartilhamento de arquivos do Azure se tornará um *ponto de extremidade de nuvem* na topologia de sincronização que você está descrevendo com a criação de um grupo de sincronização. Ao criar o grupo de sincronização, dê a ele um nome familiar, de modo que você reconheça qual conjunto de arquivos é sincronizado aqui. Certifique-se de fazer referência ao compartilhamento de arquivos do Azure com um nome correspondente.
* Depois que o grupo de sincronização for criado, você verá uma linha para ele aparecer na lista de grupos de sincronização. Clique no nome (um link) para exibir o conteúdo do grupo de sincronização. Você verá o compartilhamento de arquivos do Azure em "pontos de extremidade de nuvem".
* Localize o botão de comando para *+ Adicionar ponto de extremidade do servidor*. A pasta no servidor local que você provisionou se tornará o caminho para esse *ponto de extremidade do servidor*.
