---
title: Criptografia de serviço personalizador de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia de serviço personalizador de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071084"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Criptografia de serviço personalizador de dados em repouso

O serviço Personalizador criptografa automaticamente seus dados quando persistidos na nuvem. A criptografia de serviço Personalizador protege seus dados e para ajudá-lo a cumprir seus compromissos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só estão disponíveis na camada de preços E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [Formulário de Solicitação de Chave Gerenciado pelo Cliente do Serviço Personalizador](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para ouvir de volta sobre o status de sua solicitação. Dependendo da demanda, você pode ser colocado em uma fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para usar cmk com o serviço Personalr, você precisará criar um novo recurso Personalizador e selecionar E0 como o Nível de Precificação. Uma vez que o recurso Personalizador com o nível de preços E0 seja criado, você pode usar o Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciado pelo cliente do serviço personalizador](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
