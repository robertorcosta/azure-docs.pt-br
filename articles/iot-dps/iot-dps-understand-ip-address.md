---
title: Entendendo o endereço IP da instância do DPS (IoT Device Provisioning Service, serviço de provisionamento de dispositivos IoT) | Microsoft Docs
description: Entenda como consultar seu endereço DPS (IoT Device Provisioning Service, serviço de provisionamento de dispositivos IoT) e suas propriedades. O endereço IP da sua instância DPS pode mudar durante certos cenários, como recuperação de desastres ou failover regional.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284922"
---
# <a name="iot-hub-dps-ip-addresses"></a>Endereços IP Do IoT Hub DPS

Os prefixos de endereço IP para os pontos finais públicos de um DPS (IoT Hub Device Provisioning Service, serviço de provisionamento de dispositivos hub) são publicados periodicamente sob a [tag de serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Você pode usar esses prefixos de endereço IP para controlar a conectividade entre uma instância de DPS IoT e dispositivos ou ativos de rede, a fim de implementar uma variedade de metas de isolamento de rede:

| Goal | Abordagem |
|------|----------|
| Certifique-se de que seus dispositivos e serviços se comuniquem apenas com pontos finais do IoT Hub DPS | Use a tag de serviço _AzureIoTHub_ para descobrir instâncias do IoT Hub DPS. Configure as regras ALLOW na configuração de firewall de seus dispositivos e serviços para esses prefixos de endereço IP de acordo. Configure regras para soltar tráfego para outros endereços IP de destino com os quais você não deseja que os dispositivos ou serviços se comuniquem. |
| Certifique-se de que seu ponto final do IoT Hub DPS receba conexões apenas de seus dispositivos e ativos de rede | Use o recurso [de filtro IP](iot-dps-ip-filtering.md) IoT DPS para criar regras de filtro para o dispositivo e APIs de serviço DPS. Essas regras de filtro podem ser usadas para permitir conexões somente a partir de seus dispositivos e endereços IP de ativos de rede (ver seção [limitações).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Práticas recomendadas

* Ao adicionar regras ALLOW na configuração de firewall de seus dispositivos, é melhor fornecer portas específicas [usadas pelos protocolos aplicáveis](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP das instâncias De DPS ioT estão sujeitos a alterações. Essas alterações são publicadas periodicamente através de tags de serviço antes de fazer efeito. Portanto, é importante que você desenvolva processos para recuperar e usar regularmente as tags de serviço mais recentes. Esse processo pode ser automatizado através da [API de detecção de tags de serviço](../virtual-network/service-tags-overview.md#service-tags-on-premises). A API de descoberta de tags de serviço ainda está em visualização e, em alguns casos, pode não produzir a lista completa de tags e endereços IP. Até que a API de descoberta esteja geralmente disponível, considere usar as [tags de serviço no formato JSON para download](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Use o *AzureIoTHub.[ nome da região]* tag para identificar prefixos IP usados por pontos finais DPS em uma região específica. Para explicar a recuperação de desastres de data center ou [failover regional,](../iot-hub/iot-hub-ha-dr.md)certifique-se de que a conectividade com prefixos IP da região de geo-par da instância Do DPS também esteja ativada.

* A configuração de regras de firewall para uma instância DPS pode bloquear a conectividade necessária para executar os comandos Azure CLI e PowerShell contra ele. Para evitar esses problemas de conectividade, você pode adicionar regras ALLOW para os prefixos de endereço IP de seus clientes para reativar clientes CLI ou PowerShell para se comunicar com a instância DoD.  


## <a name="limitations-and-workarounds"></a>Limitações e Soluções Alternativas

* O recurso de filtro IP DPS tem um limite de 100 regras. Esse limite pode ser levantado através de solicitações através do Suporte ao Cliente do Azure. 

* Suas regras de [filtragem ip](iot-dps-ip-filtering.md) configuradas são aplicadas apenas nos pontos finais do DPS e não nos pontos finais do IoT Hub vinculado. A filtragem ip para hubs IoT vinculados deve ser configurada separadamente. Para obter mais informações, consulte [as regras de filtragem ip do IoT Hub](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Suporte para IPv6 

O IPv6 atualmente não é suportado no IoT Hub ou DPS.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre configurações de endereçoIP com DPS, consulte:

* [Configurar a filtragem do IP](iot-dps-ip-filtering.md)
