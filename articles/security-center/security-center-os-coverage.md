---
title: Plataformas suportadas pelo Azure Security Center | Microsoft Docs
description: Este documento fornece uma lista de plataformas suportadas pelo Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521909"
---
# <a name="supported-platforms"></a>Plataformas compatíveis 

Esta página mostra as plataformas e ambientes suportados pelo Azure Security Center.

## <a name="combinations-of-environments"></a>Combinações de ambientes<a name="vm-server"></a>

O Azure Security Center suporta máquinas e servidores virtuais em diferentes tipos de ambientes híbridos:

* Apenas Azure
* Azul e no local
* Azul e outras nuvens
* Azul, outras nuvens e no local

Para um ambiente Azure ativado por uma assinatura do Azure, o Azure Security Center descobrirá automaticamente os recursos IaaS que são implantados dentro da assinatura.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

O Security Center depende do [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Certifique-se de que suas máquinas estão executando um dos sistemas operacionais suportados para este agente, conforme descrito nas seguintes páginas:

* [Agente de Análise de Log para sistemas operacionais suportados pelo Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agente log analytics para sistemas operacionais suportados por Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Certifique-se também de que seu agente log analytics esteja [configurado corretamente para enviar dados ao Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Para saber mais sobre os recursos específicos do Security Center disponíveis no Windows e Linux, consulte [a cobertura de recursos para máquinas](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Serviços gerenciados de máquinas virtuais<a name="virtual-machine"></a>

As máquinas virtuais também são criadas em uma assinatura de clientes como parte de alguns serviços gerenciados pelo Azure, como Azure Kubernetes (AKS), Azure Databricks e muito mais. O Security Center também descobre essas máquinas virtuais, e o agente Log Analytics pode ser instalado e configurado se um sistema operacional suportado estiver disponível.

## <a name="cloud-services"></a>Serviços em nuvem<a name="cloud-services"></a>

Máquinas virtuais que são executadas em um serviço de nuvem também são suportadas. Apenas funções Web e de trabalho de serviços de nuvem que são executadas em slots de produção são monitorados. Para saber mais sobre os serviços de nuvem, confira [Visão geral dos Serviços de Nuvem do Azure](../cloud-services/cloud-services-choose-me.md).

A proteção para VMs residentes no Azure Stack também é suportada. Para obter mais informações sobre a integração do Security Center com o Azure Stack, consulte [as máquinas virtuais Azure Stack no Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [o Security Center coleta dados usando o Log Analytics Agent](security-center-enable-data-collection.md).
- Saiba como [o Security Center gerencia e protege dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar o Azure Security Center](security-center-planning-and-operations-guide.md).