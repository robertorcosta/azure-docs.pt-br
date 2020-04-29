---
title: Content Moderator criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: Content Moderator criptografia de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372156"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator criptografia de dados em repouso

Content Moderator criptografa automaticamente seus dados quando eles são persistidos na nuvem, ajudando a atender às suas metas organizacionais de segurança e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [formulário Content moderator solicitação de chave gerenciada pelo cliente](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço Content Moderator, você precisará criar um novo recurso de Content Moderator e selecionar E0 como o tipo de preço. Depois que o recurso de Content Moderator com o tipo de preço E0 for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Habilitar a criptografia de dados para sua equipe de Content Moderator

Para habilitar a criptografia de dados para sua equipe de revisão de Content Moderator, consulte o guia de [início rápido: tentar Content moderator na Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Você precisará fornecer uma _ID de recurso_ com o tipo de preço Content moderator E0.


## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciada pelo cliente Content Moderator](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
