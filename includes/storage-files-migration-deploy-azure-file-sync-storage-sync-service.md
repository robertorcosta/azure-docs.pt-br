---
title: Implantar um serviço de sincronização de armazenamento
description: Implantando o Sincronização de Arquivos do Azure recurso de nuvem, um serviço de sincronização de armazenamento. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143575"
---
Nesta etapa, você precisa de suas credenciais de assinatura do Azure.

O recurso principal a ser configurado para Sincronização de Arquivos do Azure é chamado de *serviço de sincronização de armazenamento*. Recomendamos que você implante apenas um para todos os servidores que estão sincronizando o mesmo conjunto de arquivos agora ou no futuro. Crie vários serviços de sincronização de armazenamento somente se você tiver conjuntos distintos de servidores que nunca devem trocar dados (por exemplo: sincronizar o mesmo compartilhamento de arquivos do Azure). Caso contrário, um único serviço de sincronização de armazenamento é a melhor prática.

Escolha uma região do Azure para o serviço de sincronização de armazenamento que está perto de seu local. Todos os outros recursos de nuvem devem ser implantados na mesma região.
Para simplificar o gerenciamento, crie um novo grupo de recursos em sua assinatura que abriga os recursos de sincronização e armazenamento.

Para obter mais informações, consulte a [seção sobre como implantar o serviço de sincronização de armazenamento](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) no artigo sobre como implantar sincronização de arquivos do Azure. Siga apenas esta parte do artigo. Haverá links para outras seções do artigo em etapas posteriores.