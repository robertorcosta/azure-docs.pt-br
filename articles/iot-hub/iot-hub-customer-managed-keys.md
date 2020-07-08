---
title: Criptografia de dados do Hub IoT do Azure em repouso por meio de chaves gerenciadas pelo cliente | Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976567"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT

O Hub IoT dá suporte à criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecida como traga sua própria chave (BYOK). O Hub IoT do Azure fornece criptografia de dados em repouso e em trânsito conforme eles são gravados em nossos data centers e descriptografa-os para você à medida que você o acessa. Por padrão, o Hub IoT usa chaves gerenciadas pela Microsoft para criptografar os dados em repouso. Com o CMK, você pode obter outra camada de criptografia na parte superior da criptografia padrão e pode optar por criptografar dados em repouso com uma chave de criptografia de chave, gerenciada por meio de seu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Isso oferece a flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Se o BYOK estiver configurado para o Hub IoT, também forneceremos a criptografia dupla, que oferece uma segunda camada de proteção, enquanto permite que você controle a chave de criptografia por meio de seu Azure Key Vault.

Esse recurso requer a criação de um novo hub IoT (camada básica ou Standard). Para experimentar esse recurso, entre em contato conosco por meio [do suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Compartilhe o nome da sua empresa e a ID da assinatura ao entrar em contato com o suporte da Microsoft.


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Hub IoT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
