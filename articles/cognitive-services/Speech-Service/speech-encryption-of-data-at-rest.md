---
title: Criptografia de serviço de fala de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia de serviço de fala de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372354"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Criptografia de serviço de fala de dados em repouso

O Serviço de Fala criptografa automaticamente seus dados quando ele é persistido na nuvem. A criptografia do serviço de fala protege seus dados e para ajudá-lo a cumprir seus compromissos de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de Serviços Cognitivos

Os dados são criptografados e descriptografados usando criptografia [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Criptografia e descriptografia são transparentes, o que significa que criptografia e acesso são gerenciados para você. Seus dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Quando você usa voz personalizada e voz personalizada, o serviço de fala pode armazenar os seguintes dados na nuvem:  

* Dados de rastreamento de fala - somente se você ativar o rastreamento para o seu ponto final personalizado
* Dados de treinamento e teste enviados

Por padrão, seus dados são armazenados no armazenamento da Microsoft e sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Você também tem uma opção para preparar sua própria conta de armazenamento. O acesso à loja é gerenciado pelo serviço De identidade gerenciada e o serviço de fala não pode acessar diretamente seus próprios dados, como dados de rastreamento de fala, dados de treinamento de personalização e modelos personalizados.

Para obter mais informações sobre identidade gerenciada, consulte [Quais são as identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Traga seu próprio armazenamento (BYOS) para personalização e registro

Para solicitar acesso para trazer seu próprio armazenamento, preencha e envie o [serviço Speech - traga seu próprio formulário de solicitação de armazenamento (BYOS).](https://aka.ms/cogsvc-cmk) Uma vez aprovado, você precisará criar sua própria conta de armazenamento para armazenar os dados necessários para personalização e registro. Ao adicionar uma conta de armazenamento, o recurso de serviço Speech habilitará uma identidade gerenciada atribuída ao sistema. Depois que a identidade gerenciada atribuída ao sistema estiver habilitada, esse recurso será registrado no AAD (AAD). Após o registro, a identidade gerenciada terá acesso à conta de armazenamento. Você pode aprender mais sobre Identidades Gerenciadas aqui. Para obter mais informações sobre identidade gerenciada, consulte [Quais são as identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desativar as identidades gerenciadas do sistema, o acesso à conta de armazenamento será removido. Isso fará com que as partes do serviço Speech que requerem acesso à conta de armazenamento parem de funcionar.  

## <a name="regional-availability"></a>Disponibilidade regional

O BYOS está disponível atualmente nessas regiões:

* Centro-Sul dos EUA
* Oeste dos EUA 2
* Leste dos EUA

## <a name="next-steps"></a>Próximas etapas

* [Serviço de fala - traga seu próprio formulário de solicitação de armazenamento (BYOS)](https://aka.ms/cogsvc-cmk)
* [Quais são as identidades gerenciadas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
