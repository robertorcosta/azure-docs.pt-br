---
title: Implantar um serviço de sincronização de armazenamento
description: Implantando o Sincronização de Arquivos do Azure recurso de nuvem. Um serviço de sincronização de armazenamento. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209448"
---
Nesta etapa, você precisa de suas credenciais de assinatura do Azure. A assinatura do Azure que você usa pode ser diferente da que você usa para o StorSimple.

O recurso principal para configurar Sincronização de Arquivos do Azure é chamado de "serviço de sincronização de armazenamento".
Recomendamos que você implante apenas um para todos os servidores da empresa que sincronizam o mesmo conjunto de arquivos agora ou no futuro. Se você tiver mais de um dispositivo StorSimple, você pode considerar a criação de um recurso de serviço de sincronização de armazenamento para cada um deles. No entanto, você só deve criar vários serviços de sincronização de armazenamento se tiver conjuntos distintos de servidores que nunca devem trocar dados. Caso contrário, um único serviço de sincronização de armazenamento é a melhor prática.

Escolha uma região do Azure para o serviço de sincronização de armazenamento que está perto do seu local de escritório. Todos os outros recursos de nuvem devem ser implantados na mesma região.
A prática recomendada é criar um novo grupo de recursos em sua assinatura para abrigar recursos de sincronização e armazenamento para facilitar o gerenciamento.

O artigo a seguir descreve como implantar um serviço de sincronização de armazenamento. Só siga esta parte do documento. Haverá links para outras subseções deste documento em etapas posteriores.

[Saiba como implantar um serviço de sincronização de armazenamento.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
