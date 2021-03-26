---
title: Recomendações de segurança e configuração do firewall Percept do Azure
description: Saiba mais sobre a configuração do firewall Percept do Azure e recomendações de segurança
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 086d7ec9d2bcae96ee64745a4382c4748aea291e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572317"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Recomendações de segurança e configuração do firewall Percept do Azure

Examine as diretrizes abaixo para obter informações sobre como configurar firewalls e práticas recomendadas de segurança geral com o Azure Percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configurando firewalls para o Azure Percept DK

Se sua configuração de rede exigir que você explicitamente permita conexões feitas de dispositivos do Azure Percept DK, examine a lista de componentes a seguir.

Esta lista de verificação é um ponto de partida para regras de firewall:

|URL (* = curinga)|Portas TCP de Saída|Uso|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Autenticação e autorização de SOM do Azure DK|
|*. auth.projectsantacruz.azure.net|443|Autenticação e autorização de SOM do Azure DK|

Além disso, examine a lista de [conexões usadas pelo Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Recomendações adicionais para a implantação na produção

O Azure Percept DK oferece uma grande variedade de recursos de segurança prontos para uso. Além desses recursos de segurança avançados incluídos na versão atual, a Microsoft também sugere as seguintes diretrizes ao considerar as implantações de produção:

- Forte proteção física do próprio dispositivo
- Verifique se a criptografia de dados em repouso está habilitada
- Monitorar continuamente a postura do dispositivo e responder rapidamente aos alertas
- Limitar o número de administradores que têm acesso ao dispositivo
