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
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202266"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Visão Personalizada criptografia de dados em repouso

O Azure Visão Personalizada criptografa automaticamente seus dados quando persistidos na nuvem. Visão Personalizada criptografia protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só são recursos disponíveis criados após 11 de maio de 2020. Para usar o CMK com Visão Personalizada, será necessário criar um novo recurso de Visão Personalizada. Depois que o recurso for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

### <a name="regional-availability"></a>Disponibilidade regional

Atualmente, as chaves gerenciadas pelo cliente estão disponíveis nestas regiões:

* Centro-Sul dos EUA
* Oeste dos EUA 2
* Leste dos EUA
* Gov. dos EUA – Virgínia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciada pelo cliente Visão Personalizada](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


