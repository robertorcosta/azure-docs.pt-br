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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473245"
---
# <a name="supported-platforms"></a>Plataformas compatíveis 

## <a name="virtual-machines--servers"></a>Máquinas virtuais / servidores<a name="vm-server"></a>

O Security Center suporta máquinas/servidores virtuais em diferentes tipos de ambientes híbridos:

* Apenas Azure
* Azul e no local
* Azul e outras nuvens
* Azul, outras nuvens e no local

Para um ambiente Azure ativado por uma assinatura do Azure, o Azure Security Center descobrirá automaticamente os recursos IaaS que são implantados dentro da assinatura.

> [!NOTE]
> Para receber o conjunto completo de recursos de segurança, você deve ter o [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), que é usado pelo Azure Security Center, instalado e [configurado corretamente para enviar dados ao Azure Security Center](security-center-enable-data-collection.md#manual-agent).

As seções a seguir listam os sistemas operacionais de servidor suportados nos quais o [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), que é usado pelo Azure Security Center, pode ser executado.

### <a name="windows-server-operating-systems"></a>Sistemas operacionais do servidor Windows<a name="os-windows"></a>

|Sistema operacional|Com o apoio do Azure Security Center|Suporte para integração com o Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Para saber mais sobre os recursos suportados para os sistemas operacionais Windows, listados acima, consulte [recursos suportados por máquina virtual/ servidor](security-center-services.md#vm-server-features).

### <a name="windows-operating-systems"></a>Sistemas operacionais Windows<a name="os-windows (non-server)"></a>

O Azure Security Center integra-se aos serviços do Azure para monitorar e proteger suas máquinas virtuais baseadas no Windows.

### <a name="linux-operating-systems"></a>Sistemas operacionais Linux<a name="os-linux"></a>

64 bits

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e Oracle Linux 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

> [!NOTE]
> Uma vez que a lista de sistemas operacionais Linux suportados está constantemente mudando, se preferir, clique [aqui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) para ver a lista mais atualizada de versões suportadas, caso tenha havido mudanças desde que este tópico foi publicado pela última vez.

Para saber mais sobre os recursos suportados para os sistemas operacionais Linux, listados acima, consulte [recursos suportados por máquina/servidor virtual](security-center-services.md#vm-server-features).

### <a name="managed-virtual-machine-services"></a>Serviços gerenciados de máquinas virtuais<a name="virtual-machine"></a>

As máquinas virtuais também são criadas em uma assinatura de clientes como parte de alguns serviços gerenciados pelo Azure, como Azure Kubernetes (AKS), Azure Databricks e muito mais. Essas máquinas virtuais também são descobertas pelo Azure Security Center, e o agente de análise log pode ser instalado e configurado de acordo com os [sistemas operacionais Windows/Linux](#os-windows)suportados, listados acima.

### <a name="cloud-services"></a>Serviços em nuvem<a name="cloud-services"></a>

Máquinas virtuais que são executadas em um serviço de nuvem também são suportadas. Apenas funções Web e de trabalho de serviços de nuvem que são executadas em slots de produção são monitorados. Para saber mais sobre os serviços de nuvem, confira [Visão geral dos Serviços de Nuvem do Azure](../cloud-services/cloud-services-choose-me.md).

A proteção para máquinas virtuais residentes no Azure Stack também é suportada. Para obter mais informações sobre a integração do Security Center com o Azure Stack, consulte [as máquinas virtuais Azure Stack no Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [o Security Center coleta dados e o Log Analytics Agent](security-center-enable-data-collection.md).
- Saiba como [o Security Center gerencia e protege dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar o Azure Security Center](security-center-planning-and-operations-guide.md).
- Conheça [os recursos disponíveis para os diferentes ambientes em nuvem.](security-center-services.md)
- Saiba mais sobre [proteção contra ameaças para máquinas Windows e Linux no Azure Security Center](threat-protection.md#windows-machines).
