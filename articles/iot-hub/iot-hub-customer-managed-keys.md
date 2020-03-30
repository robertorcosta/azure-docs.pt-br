---
title: Criptografia de dados do Azure IoT Hub em repouso por meio de chaves gerenciadas pelo cliente| Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370569"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o IoT Hub

O IoT Hub suporta criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecida como Bring your own key (BYOK), suporte para o Azure IoT Hub. O Azure IoT Hub fornece criptografia de dados em repouso e em trânsito. Por padrão, o IoT Hub usa chaves gerenciadas pela Microsoft para criptografar os dados. Com o suporte ao CMK, os clientes agora têm a opção de criptografar os dados em repouso com uma chave de criptografia chave, gerenciada pelos clientes, usando o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Essa capacidade requer a criação de um novo Hub de IoT (nível básico ou padrão), em uma das seguintes regiões: Leste dos EUA, US$ 2, Centro-Sul dos EUA ou Us Gov. Para tentar esse recurso, entre em contato conosco através [do suporte da Microsoft.](https://azure.microsoft.com/support/create-ticket/) Compartilhe o nome da sua empresa e o ID de assinatura ao entrar em contato com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
