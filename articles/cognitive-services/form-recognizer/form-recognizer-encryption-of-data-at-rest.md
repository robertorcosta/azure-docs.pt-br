---
title: Criptografia do serviço reconhecedor de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e também permite que você gerencie suas assinaturas de serviços cognitivas com suas próprias chaves, chamadas CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados em repouso para o reconhecedor de formulário e como habilitar e gerenciar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: c959231826125349ecd6a62afe529248f7ac2eec
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326789"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Criptografia do reconhecedor de formulário de dados em repouso

O reconhecedor do Azure Form criptografa automaticamente seus dados ao mantê-los para a nuvem. A criptografia do reconhecedor de formulário protege seus dados para ajudá-lo a atender aos compromissos de conformidade e segurança da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só são recursos disponíveis criados após 11 de maio de 2020. Para usar o CMK com o reconhecedor de formulário, você precisará criar um novo recurso de reconhecedor de formulário. Depois que o recurso for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciada pelo cliente do reconhecedor de formulário](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)