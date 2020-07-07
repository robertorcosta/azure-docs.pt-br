---
title: Plataformas com suporte na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de plataformas com suporte na central de segurança do Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80521909"
---
# <a name="supported-platforms"></a>Plataformas com Suporte 

Esta página mostra as plataformas e os ambientes com suporte na central de segurança do Azure.

## <a name="combinations-of-environments"></a>Combinações de ambientes<a name="vm-server"></a>

A central de segurança do Azure dá suporte a máquinas virtuais e servidores em diferentes tipos de ambientes híbridos:

* Somente o Azure
* Azure e local
* Azure e outras nuvens
* Azure, outras nuvens e locais

Para um ambiente do Azure ativado em uma assinatura do Azure, a central de segurança do Azure descobrirá automaticamente os recursos de IaaS implantados na assinatura.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A central de segurança depende do [agente de log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Verifique se os computadores estão executando um dos sistemas operacionais com suporte para esse agente, conforme descrito nas páginas a seguir:

* [Agente de Log Analytics para sistemas operacionais com suporte do Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agente de Log Analytics para sistemas operacionais com suporte do Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Verifique também se o agente de Log Analytics está [configurado corretamente para enviar dados para a central de segurança](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Para saber mais sobre os recursos específicos da central de segurança disponíveis no Windows e no Linux, consulte [cobertura de recursos para computadores](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Serviços de máquina virtual gerenciados<a name="virtual-machine"></a>

As máquinas virtuais também são criadas em uma assinatura de cliente como parte de alguns serviços gerenciados pelo Azure também, como o Azure kubernetes (AKS), Azure Databricks e muito mais. A central de segurança descobre também essas máquinas virtuais, e o agente de Log Analytics pode ser instalado e configurado se um sistema operacional com suporte estiver disponível.

## <a name="cloud-services"></a>Serviços de nuvem<a name="cloud-services"></a>

Também há suporte para máquinas virtuais que são executadas em um serviço de nuvem. Apenas funções Web e de trabalho de serviços de nuvem que são executadas em slots de produção são monitorados. Para saber mais sobre os serviços de nuvem, confira [Visão geral dos Serviços de Nuvem do Azure](../cloud-services/cloud-services-choose-me.md).

Também há suporte para a proteção para VMs que residem no Azure Stack. Para obter mais informações sobre a integração da central de segurança com o Azure Stack, consulte integrar [suas máquinas virtuais Azure Stack à central de segurança](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [a central de segurança coleta dados usando o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).