---
title: Selecione e implante o Azure Security Center para agente ioT| Microsoft Docs
description: Saiba como selecionar e implantar o Azure Security Center para agentes de segurança IoT em dispositivos IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d70f2f3ec87c8673013bcf7b6f70ebcbb8d06f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770009"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecione e implante um agente de segurança em seu dispositivo IoT

O Azure Security Center for IoT fornece arquiteturas de referência para agentes de segurança que monitoram e coletam dados de dispositivos IoT.
Para saber mais, consulte [a arquitetura de referência do agente de segurança](security-agent-architecture.md).

Os agentes são desenvolvidos como projetos de código aberto, e estão disponíveis em dois sabores: <br> [C](https://aka.ms/iot-security-github-c), e [C#](https://aka.ms/iot-security-github-cs).

Neste artigo, você aprenderá como: 
> [!div class="checklist"]
> * Compare os sabores do agente de segurança
> * Descubra plataformas de agentes suportadas
> * Escolha o sabor do agente certo para sua solução

## <a name="understand-security-agent-options"></a>Entenda as opções do agente de segurança

Cada centro de segurança do Azure para sabor de agente de segurança IoT oferece o mesmo conjunto de recursos e suporta opções de configuração semelhantes. 

O agente de segurança baseado em C tem uma pegada de memória mais baixa, e é a escolha ideal para dispositivos com menos recursos disponíveis. 

|     | Agente de segurança baseado em C | Agente de segurança baseado em C# |
| --- | ----------- | --------- |
| Código aberto | Disponível [licença do MIT](https://en.wikipedia.org/wiki/MIT_License) no [GitHub](https://aka.ms/iot-security-github-cs) | Disponível [licença do MIT](https://en.wikipedia.org/wiki/MIT_License) no [GitHub](https://aka.ms/iot-security-github-c) |
| Linguagem de desenvolvimento    | C | C# |
| Plataformas Windows suportadas? | Não | Sim |
| Pré-requisitos do Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Plataformas Linux suportadas? | Sim, x64 e x86 | Sim, apenas x64 |
| Pré-requisitos do Linux | libunwind8, libcurl3, uuid-runtime, auditado, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditado, audispd-plugins, sudo, netstat, iptables |
| Pegada de disco | 10,5 MB | 90 MB |
| Pegada de memória (em média) | 5,5 MB | 33 MB |
| [Autenticação](concept-security-agent-authentication-methods.md) para IoT Hub | Sim | Sim |
| Coleta de dados [de segurança](how-to-agent-configuration.md#supported-security-events) | Sim | Sim |
| Agregação de eventos | Sim | Sim |
| Configuração remota através do [módulo de segurança twin](concept-security-module.md) | Sim | Sim |
|

## <a name="security-agent-installation-guidelines"></a>Diretrizes de instalação de agentes de segurança

Para **Windows**: O script Install SecurityAgent.ps1 deve ser executado a partir de uma janela PowerShell do administrador. 

Para **Linux**: O InstallSecurityAgent.sh deve ser executado como superusuário. Recomendamos prefixar o comando de instalação com "sudo".


## <a name="choose-an-agent-flavor"></a>Escolha um sabor de agente 

Responda às seguintes perguntas sobre seus dispositivos IoT para selecionar o agente correto:

- Você está usando _o Windows Server_ ou o Windows _IoT Core?_ 

    [Implante um agente de segurança baseado em C#para Windows](how-to-deploy-windows-cs.md).

- Você está usando uma distribuição Linux com arquitetura x86? 

    [Implantar um agente de segurança baseado em C para Linux](how-to-deploy-linux-c.md).

- Você está usando uma distribuição Linux com arquitetura x64?

    Ambos os sabores do agente podem ser usados. <br>
    [Implantar um agente de segurança baseado em C para Linux](how-to-deploy-linux-c.md) e/ou implantar um agente de segurança baseado em [C#para Linux](how-to-deploy-linux-cs.md).

Ambos os sabores do agente oferecem o mesmo conjunto de recursos e suportam opções de configuração semelhantes.
Consulte [a comparação do agente de segurança](how-to-deploy-agent.md#understand-security-agent-options) para saber mais.

## <a name="supported-platforms"></a>Plataformas compatíveis

A lista a seguir inclui todas as plataformas suportadas atualmente.

|Centro de Segurança Azure para agente de IoT |Sistema operacional |Arquitetura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64, ARMv7|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, continue a orientar como fazer para a configuração do agente. 
> [!div class="nextstepaction"]
> [Configuração do agente como orientar](./how-to-agent-configuration.md)
