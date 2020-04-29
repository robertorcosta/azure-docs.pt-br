---
title: Criptografia de dados do serviço de provisionamento de dispositivos do Azure em repouso por meio de chaves gerenciadas pelo cliente | Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o serviço de provisionamento de dispositivos
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77675137"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o serviço de provisionamento de dispositivos

## <a name="overview"></a>Visão geral

O DPS (serviço de provisionamento de dispositivos) dá suporte à criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecida como traga sua própria chave (BYOK). O DPS fornece criptografia de dados em repouso e em trânsito. Por padrão, o DPS usa chaves gerenciadas pela Microsoft para criptografar os dados. Com o suporte do CMK, os clientes agora têm a opção de criptografar os dados em repouso com uma chave de criptografia de chave, gerenciada pelos clientes, usando o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Esse recurso requer a criação de um novo DPS, em uma das seguintes regiões: leste dos EUA, oeste dos EUA 2 ou EUA Central do Sul. Para experimentar esse recurso, entre em contato conosco por meio [do suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Compartilhe o nome da sua empresa e a ID da assinatura ao entrar em contato com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o serviço de provisionamento de dispositivos](https://docs.microsoft.com/azure/iot-dps/)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)