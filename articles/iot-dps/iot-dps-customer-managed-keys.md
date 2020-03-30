---
title: Criptografia de dados do Azure Device Provisioning Service em repouso por meio de chaves gerenciadas pelo cliente| Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para serviço de provisionamento de dispositivos
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675137"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para serviço de provisionamento de dispositivos

## <a name="overview"></a>Visão geral

O DPS (Device Provisioning Service, serviço de provisionamento de dispositivos) suporta criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK). O DPS fornece criptografia de dados em repouso e em trânsito. Por padrão, o DPS usa chaves gerenciadas pela Microsoft para criptografar os dados. Com o suporte ao CMK, os clientes agora têm a opção de criptografar os dados em repouso com uma chave de criptografia chave, gerenciada pelos clientes, usando o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Essa capacidade requer a criação de um novo DPS, em uma das seguintes regiões: Leste dos EUA, US OESTE 2 ou Centro-Sul dos EUA. Para tentar esse recurso, entre em contato conosco através [do suporte da Microsoft.](https://azure.microsoft.com/support/create-ticket/) Compartilhe o nome da sua empresa e o ID de assinatura ao entrar em contato com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Serviço de Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps/)

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)