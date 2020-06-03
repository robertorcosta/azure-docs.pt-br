---
title: Criptografia do serviço reconhecedor de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia do reconhecedor de formulário de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310709"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Criptografia do reconhecedor de formulário de dados em repouso

O reconhecedor do Azure Form criptografa automaticamente os dados quando persistidos na nuvem. A criptografia do reconhecedor de formulário protege seus dados e para ajudá-lo a atender aos compromissos de conformidade e segurança da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só são recursos disponíveis criados após 11 de maio de 2020. Para usar o CMK com o reconhecedor de formulário, você precisará criar um novo recurso de reconhecedor de formulário. Depois que o recurso for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciada pelo cliente do reconhecedor de formulário](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


