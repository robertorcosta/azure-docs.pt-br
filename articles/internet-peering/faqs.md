---
title: Peering na Internet - PERGUNTAS FREQUENTES
titleSuffix: Azure
description: Peering na Internet - PERGUNTAS FREQUENTES
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775492"
---
# <a name="internet-peering---faqs"></a>Peering na Internet - PERGUNTAS FREQUENTES

Você pode revisar as informações abaixo para perguntas gerais.

**Qual é a diferença entre peering de Internet e Peering Service?**

Peering Service é um serviço que pretende fornecer conectividade IP pública de nível corporativo à Microsoft para seus clientes corporativos. A Internet de nível corporativo inclui conectividade através de ISPs que têm alta conectividade de throughput com a Microsoft e redundância para uma conectividade HA. Além disso, o tráfego do usuário é otimizado para latência para o Microsoft Edge mais próximo. Peering Service baseia-se na conectividade de peering com a operadora parceira. A conectividade de peering com o parceiro deve ser de peering direto em oposição ao peering do Exchange. Peering direto deve ter redundância local e geo-redundância.

**O que é o legado olhando?**

A conexão de peering configurada usando o Azure PowerShell é gerenciada como um recurso do Azure. As conexões de peering configuradas no passado são armazenadas em nosso sistema como peering legado que você pode optar por converter para gerenciar como um recurso do Azure.

**Quando o New-AzPeeringDirectConnectionObject é chamado, quais endereços IP são dados aos dispositivos Microsoft e Peer?**

Ao ligar para o cmdlet New-AzPeeringDirectConnectionObject, um endereço /31 (a.b.c.d/31) ou um endereço /30 (a.b.c.d/30) é inserido. O primeiro endereço IP (a.b.c.d+0) é dado ao dispositivo do Peer e o segundo endereço IP (a.b.c.d+1) é dado ao dispositivo Microsoft.

**O que é maxprefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 parâmetros em New-AzPeeringDirectConnectionObject cmdlet?**

Os parâmetros MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 representam o número máximo de prefixos IPv4 e IPv6 que um Peer quer que a Microsoft aceite. Esses parâmetros podem ser modificados a qualquer momento.