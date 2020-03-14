---
title: Criptografia de dados do Hub IoT do Azure em repouso por meio de chaves gerenciadas pelo cliente | Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370569"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT

O Hub IoT dá suporte à criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecida como traga sua própria chave (BYOK), suporte para o Hub IoT do Azure. O Hub IoT do Azure fornece criptografia de dados em repouso e em trânsito. Por padrão, o Hub IoT usa chaves gerenciadas pela Microsoft para criptografar os dados. Com o suporte do CMK, os clientes agora têm a opção de criptografar os dados em repouso com uma chave de criptografia de chave, gerenciada pelos clientes, usando o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Esse recurso requer a criação de um novo hub IoT (camada básica ou Standard), em uma das seguintes regiões: leste dos EUA, oeste dos EUA 2, Sul EUA Central ou US Gov. Para experimentar esse recurso, entre em contato conosco por meio [do suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Compartilhe o nome da sua empresa e a ID da assinatura ao entrar em contato com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Hub IoT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
