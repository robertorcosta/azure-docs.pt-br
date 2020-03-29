---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372167"
---
## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de Serviços Cognitivos

Os dados são criptografados e descriptografados usando criptografia [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Criptografia e descriptografia são transparentes, o que significa que criptografia e acesso são gerenciados para você. Seus dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Se você estiver usando um nível de preços que suporte as chaves gerenciadas pelo Cliente, você poderá ver as configurações de criptografia do seu recurso na seção **Criptografia** do [portal Azure,](https://portal.azure.com)conforme mostrado na imagem a seguir.

![Exibir configurações de criptografia](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Para assinaturas que só suportam chaves de criptografia gerenciadas pela Microsoft, você não terá uma seção **Criptografia.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Há também uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK), oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.
