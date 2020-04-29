---
title: Criptografia de serviço de face de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia de serviço de face de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372211"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Criptografia de serviço de face de dados em repouso

O serviço de face criptografa automaticamente seus dados quando persistidos na nuvem. A criptografia do serviço de face protege seus dados e para ajudá-lo a atender aos compromissos de conformidade e segurança da organização.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [formulário de solicitação de chave gerenciada pelo cliente do serviço de rosto](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço de face, você precisará criar um novo recurso de face e selecionar E0 como o tipo de preço. Depois que o recurso de rosto com o tipo de preço E0 for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciada pelo cliente do serviço de rosto](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


