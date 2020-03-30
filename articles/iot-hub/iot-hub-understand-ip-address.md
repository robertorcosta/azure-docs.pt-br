---
title: Entendendo o endereço IP do seu hub de IoT | Microsoft Docs
description: Entenda como consultar seu endereço IP do hub de IoT e suas propriedades. O endereço IP do seu hub IoT pode mudar durante certos cenários, como recuperação de desastres ou failover regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367560"
---
# <a name="iot-hub-ip-addresses"></a>Endereços IP do IoT Hub

Os prefixos de endereço IP dos pontos finais públicos do IoT Hub são publicados periodicamente a [tag de serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> Para dispositivos que são implantados dentro de redes locais, o Azure IoT Hub suporta a integração de conectividade VNET com pontos finais privados. Consulte [o suporte do IoT Hub para o VNET's](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints) para obter mais informações.


Você pode usar esses prefixos de endereço IP para controlar a conectividade entre o IoT Hub e seus dispositivos ou ativos de rede, a fim de implementar uma variedade de metas de isolamento de rede:

| Goal | Cenários aplicáveis | Abordagem |
|------|-----------|----------|
| Certifique-se de que seus dispositivos e serviços se comuniquem apenas com pontos finais do IoT Hub | [Mensagens de dispositivo para nuvem](./iot-hub-devguide-messaging.md)e nuvem para [dispositivo,](./iot-hub-devguide-messages-c2d.md) [métodos diretos, gêmeos](./iot-hub-devguide-direct-methods.md) [de dispositivos e módulos](./iot-hub-devguide-device-twins.md) e [fluxos de dispositivos](./iot-hub-device-streams-overview.md) | Use as tags de serviço _AzureIoTHub_ e _EventHub_ para descobrir prefixos de endereço IP do IoT Hub e event Hub e configurar regras do ALLOW na configuração de firewall de seus dispositivos e serviços para os prefixos de endereço IP de acordo com esses prefixos de endereço IP; desfazer o tráfego para outros endereços IP de destino com os quais você não deseja que os dispositivos ou serviços se comuniquem. |
| Certifique-se de que o ponto final do dispositivo IoT Hub receba conexões somente a partir de seus dispositivos e ativos de rede | [Mensagens de dispositivo para nuvem](./iot-hub-devguide-messaging.md)e nuvem para [dispositivo,](./iot-hub-devguide-messages-c2d.md) [métodos diretos, gêmeos](./iot-hub-devguide-direct-methods.md) [de dispositivos e módulos](./iot-hub-devguide-device-twins.md) e [fluxos de dispositivos](./iot-hub-device-streams-overview.md) | Use [o recurso de filtro IP do](iot-hub-ip-filtering.md) IoT Hub para permitir conexões de seus dispositivos e endereços IP de ativos de rede (ver seção [limitações).](#limitations-and-workarounds) | 
| Certifique-se de que os recursos de ponto final personalizados de suas rotas (contas de armazenamento, barramento de serviço e hubs de eventos) sejam acessíveis somente a partir de seus ativos de rede | [Roteamento de mensagem](./iot-hub-devguide-messages-d2c.md) | Siga as orientações do seu recurso sobre a restrição de conectividade (por exemplo, através de regras de [firewall,](../storage/common/storage-network-security.md) [links privados](../private-link/private-endpoint-overview.md)ou [pontos finais de serviço);](../virtual-network/virtual-network-service-endpoints-overview.md) use as tags de serviço _Do AzureIoTHub_ para descobrir prefixos de endereço IP do IoT Hub e adicionar regras ALLOW para esses prefixos IP na configuração de firewall do seu recurso (ver seção [limitações).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Práticas recomendadas

* Ao adicionar regras ALLOW na configuração de firewall de seus dispositivos, é melhor fornecer portas específicas [usadas pelos protocolos aplicáveis](./iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP do hub IoT estão sujeitos a alterações. Essas alterações são publicadas periodicamente através de tags de serviço antes de fazer efeito. Portanto, é importante que você desenvolva processos para recuperar e usar regularmente as tags de serviço mais recentes. Esse processo pode ser automatizado através da [API de detecção de tags de serviço](../virtual-network/service-tags-overview.md#service-tags-on-premises). Observe que a API de detecção de tags de serviço ainda está em visualização e, em alguns casos, pode não produzir a lista completa de tags e endereços IP. Até que a API de descoberta esteja geralmente disponível, considere usar as [tags de serviço no formato JSON para download](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Use o *AzureIoTHub.[ nome da região]* tag para identificar prefixos IP usados por pontos finais de hub IoT em uma região específica. Para explicar a recuperação de desastres de data center ou [failover regional,](iot-hub-ha-dr.md) certifique-se de que a conectividade com prefixos IP da região de geo-par do seu IoT Hub também esteja ativada.

* A configuração de regras de firewall no IoT Hub pode bloquear a conectividade necessária para executar os comandos Azure CLI e PowerShell contra seu IoT Hub. Para evitar isso, você pode adicionar regras ALLOW para os prefixos de endereço IP de seus clientes para reativar clientes CLI ou PowerShell para se comunicar com seu IoT Hub.  


## <a name="limitations-and-workarounds"></a>Limitações e Soluções Alternativas

* O recurso de filtro IP do IoT Hub tem um limite de 10 regras. Esse limite pode ser levantado através de solicitações através do Suporte ao Cliente do Azure. 

* Suas regras de [filtragem ip](iot-hub-ip-filtering.md) configuradas são aplicadas apenas em seus pontos finais IP do IoT Hub e não no ponto final do Hub de eventos incorporado do seu hub de IoT. Se você também precisar de filtragem ip para ser aplicado no Event Hub onde suas mensagens estão armazenadas, você pode fazê-lo trazendo seu próprio recurso do Event Hub onde você pode configurar diretamente as regras de filtragem de IP desejadas. Para isso, você precisa provisionar seu próprio recurso do Event Hub e configurar [o roteamento de mensagens](./iot-hub-devguide-messages-d2c.md) para enviar suas mensagens para esse recurso em vez do Hub de eventos incorporado do Seu IoT Hub. Finalmente, como discutido na tabela acima, para ativar a funcionalidade de roteamento de mensagens, você também precisa permitir a conectividade dos prefixos de endereço IP do IoT Hub ao recurso do Event Hub provisionado.

* Ao direcionar para uma conta de armazenamento, permitir o tráfego dos prefixos de endereço IP do IoT Hub só é possível quando a conta de armazenamento está em uma região diferente como o Seu IoT Hub.

## <a name="support-for-ipv6"></a>Suporte para IPv6 

O IPv6 atualmente não é suportado no IoT Hub.
