---
title: Criptografia do serviço de fala de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia do serviço de fala de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372354"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Criptografia do serviço de fala de dados em repouso

O serviço de fala criptografa automaticamente os dados quando eles são persistidos para a nuvem. A criptografia do serviço de fala protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de serviços cognitivas

Os dados são criptografados e descriptografados usando [a criptografia AES de 256 bits em](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conformidade com [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para você. Os dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Quando você usa Fala Personalizada e voz personalizada, o serviço de fala pode armazenar os seguintes dados na nuvem:  

* Dados de rastreamento de fala – somente se você ativar o rastreamento para seu ponto de extremidade personalizado
* Dados de treinamento e teste carregados

Por padrão, os dados são armazenados no armazenamento da Microsoft e sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Você também tem uma opção para preparar sua própria conta de armazenamento. O acesso ao repositório é gerenciado pela identidade gerenciada, e o serviço de fala não pode acessar diretamente seus próprios dados, como dados de rastreamento de fala, dados de treinamento de personalização e modelos personalizados.

Para obter mais informações sobre identidade gerenciada, consulte [o que são identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Traga seu próprio armazenamento (BYOS) para personalização e registro em log

Para solicitar acesso para trazer seu próprio armazenamento, preencha e envie o [formulário de solicitação serviço de fala – traga seu próprio armazenamento (BYOS)](https://aka.ms/cogsvc-cmk). Depois de aprovado, você precisará criar sua própria conta de armazenamento para armazenar os dados necessários para a personalização e o registro em log. Ao adicionar uma conta de armazenamento, o recurso do serviço de fala habilitará uma identidade gerenciada atribuída ao sistema. Depois que a identidade gerenciada atribuída pelo sistema estiver habilitada, esse recurso será registrado com Azure Active Directory (AAD). Depois de ser registrado, a identidade gerenciada receberá acesso à conta de armazenamento. Você pode aprender mais sobre identidades gerenciadas aqui. Para obter mais informações sobre identidade gerenciada, consulte [o que são identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas pelo sistema, o acesso à conta de armazenamento será removido. Isso fará com que as partes do serviço de fala que exigem acesso à conta de armazenamento parem de funcionar.  

## <a name="regional-availability"></a>Disponibilidade regional

O BYOS está disponível atualmente nestas regiões:

* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2
* Leste dos EUA

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Serviço de fala – formulário de solicitação de BYOS (Traga seu próprio armazenamento)](https://aka.ms/cogsvc-cmk)
* [O que são identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
