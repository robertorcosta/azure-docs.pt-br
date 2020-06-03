---
title: Visão Personalizada criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: Visão Personalizada criptografia de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310284"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Visão Personalizada criptografia de dados em repouso

O Azure Visão Personalizada criptografa automaticamente seus dados quando persistidos na nuvem. Visão Personalizada criptografia protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só são recursos disponíveis criados após 11 de maio de 2020. Para usar o CMK com Visão Personalizada, será necessário criar um novo recurso de Visão Personalizada. Depois que o recurso for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

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
