---
title: Implantar um serviço de sincronização de armazenamento
description: Implantando o recurso de nuvem Azure File Sync. Um serviço de sincronização de armazenamento. Um bloco de texto comum, compartilhado entre os docs de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124740"
---
Nesta etapa, você precisa de suas credenciais de assinatura do Azure.

O recurso principal para configurar o Azure File Sync é chamado de "Serviço de sincronização de armazenamento".
Recomendamos que você implante apenas um para todos os servidores da empresa que sincronizar o mesmo conjunto de arquivos agora ou no futuro. Crie apenas vários Serviços de Sincronização de Armazenamento se você tiver conjuntos distintos de servidores que nunca devem trocar dados. (por exemplo: sincronizar o mesmo compartilhamento de arquivos Do Zure). Caso contrário, um único Serviço de Sincronização de Armazenamento é a melhor prática.

Escolha uma região Azure para o serviço de sincronização de armazenamento que esteja perto da localização do seu escritório. Todos os outros recursos em nuvem devem ser implantados na mesma região.
Para simplificar o gerenciamento, crie um novo grupo de recursos em sua assinatura que abriga recursos de sincronização e armazenamento.

O artigo a seguir descreve como implantar um Serviço de Sincronização de Armazenamento. Só siga esta parte do doutor. Haverá links para outras subseções deste doc em etapas posteriores.

[Saiba como implantar um Serviço de Sincronização de Armazenamento.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
