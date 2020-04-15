---
title: Remediar servidores de configuração de estado de automação do Azure não compatíveis
description: Como reaplicar configurações sob demanda para servidores onde o estado de configuração se afastou
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383149"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Remediar servidores DSC não compatíveis

Quando os servidores são registrados no Azure Automation State `ApplyOnly`Configuration, o modo de configuração é definido como , `ApplyandMonitor`ou `ApplyAndAutoCorrect`. Se o modo não `ApplyAndAutoCorrect`for definido para , servidores que se afastam de um estado compatível por qualquer motivo permanecem incompatíveis até que sejam corrigidos manualmente.

A computação do Azure oferece um recurso chamado Run Command que permite que os clientes executem scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para este recurso ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais do Windows usando o PowerShell

Para obter instruções passo-a-passo usando o recurso Executar comando em máquinas virtuais windows, consulte a página de documentação [Executar scripts PowerShell em sua VM windows com comando run](/azure/virtual-machines/windows/run-command).

Para forçar um nó de configuração do Estado de Automação do Azure a baixar a configuração mais recente e aplicá-la, use o cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta das máquinas virtuais Linux

Funcionalidade semelhante não está disponível atualmente para servidores Linux.
A única opção é repetir o processo de inscrição.
Para os nódulos Azure, você pode corrigir a deriva do portal Azure ou usando cmdlets do módulo Az. Detalhes sobre esse processo são documentados em [máquinas de onboarding para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal).
Para os nós híbridos, você pode corrigir drift usando os scripts Python incluídos.
Consulte [PowerShell DSC para Linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para ver um exemplo de uso da configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).