---
title: Criptografia moderador de conteúdo de dados em repouso
titleSuffix: Azure Cognitive Services
description: Conteúdo Moderador de criptografia de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372156"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Criptografia moderador de conteúdo de dados em repouso

O Moderador de Conteúdo criptografa automaticamente seus dados quando eles são perscisos na nuvem, ajudando a cumprir suas metas de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só estão disponíveis na camada de preços E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [Formulário de Solicitação de Chave Gerenciado pelo Cliente do Moderador de Conteúdo](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para ouvir de volta sobre o status de sua solicitação. Dependendo da demanda, você pode ser colocado em uma fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para usar cmk com o serviço Moderador de conteúdo, você precisará criar um novo recurso moderador de conteúdo e selecionar E0 como o Nível de Precificação. Uma vez que o recurso moderador de conteúdo com o nível de preços E0 seja criado, você pode usar o Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Habilite a criptografia de dados para sua equipe de moderadores de conteúdo

Para habilitar a criptografia de dados para sua equipe de revisão de moderador esporártico, consulte o [Quickstart: Experimente o Moderador de Conteúdo na Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Você precisará fornecer um _ID de recurso_ com o nível de preços do Moderador de Conteúdo E0.


## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciado pelo cliente pelo moderador de conteúdo](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
