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
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309026"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Criptografia de serviço de face de dados em repouso

O serviço de face criptografa automaticamente seus dados quando persistidos na nuvem. A criptografia do serviço de face protege seus dados e para ajudá-lo a atender aos compromissos de conformidade e segurança da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [formulário de solicitação de chave gerenciada pelo cliente do serviço de rosto](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço de face, você precisará criar um novo recurso de face e selecionar E0 como o tipo de preço. Depois que o recurso de rosto com o tipo de preço E0 for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

## <a name="regional-availability"></a>Disponibilidade regional

Atualmente, as chaves gerenciadas pelo cliente estão disponíveis nestas regiões:

* Centro-Sul dos EUA
* Oeste dos EUA 2
* Leste dos EUA
* Gov. dos EUA – Virgínia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa de serviços que dão suporte a CMK, consulte [chaves gerenciadas pelo cliente para serviços cognitivas](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulário de solicitação de chave gerenciada pelo cliente de serviços cognitivas](https://aka.ms/cogsvc-cmk)
