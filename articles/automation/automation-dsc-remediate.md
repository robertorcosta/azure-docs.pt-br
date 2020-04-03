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
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585492"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Corrigir servidores DSC fora de conformidade

Quando os servidores são registrados na configuração do estado de automação do Azure, o 'Modo de configuração' é definido como ApplyOnly, ApplyandMonitor ou ApplyAndAutoCorrect.
Se o modo não for configurado como AutoCorrect, os servidores que se desviarem de um estado compatível por qualquer motivo permanecerão incompatíveis até que sejam corrigidos manualmente.

A computação do Azure oferece um recurso chamado Run Command que permite que os clientes executem scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para este recurso ao corrigir manualmente a deriva de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Deriva correta de máquinas virtuais do Windows usando o PowerShell

Para obter instruções passo a passo usando o recurso Executar comando em máquinas virtuais windows, consulte a página de documentação [Executar scripts PowerShell em sua VM windows com comando run](/azure/virtual-machines/windows/run-command).

Para forçar um nó de configuração do Estado de Automação do `Update-DscConfiguration` Azure a baixar a configuração mais recente e aplicá-la, use o cmdlet.
Para obter detalhes, consulte a documentação do cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva correta das máquinas virtuais Linux

Funcionalidade semelhante não está disponível atualmente para servidores Linux.
A única opção é repetir o processo de inscrição.
Para os nódulos Azure, a correção de deriva pode ser feita a partir do portal ou usando cmdlets da Az Automation.
Os detalhes sobre esse processo estão documentados na página [Máquinas de Onboarding para gerenciamento pelo Azure Automation State Configuration](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal).
Para os nós híbridos, a correção de deriva pode ser feita usando os scripts Python incluídos.
Consulte a documentação no [PowerShell DSC para linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Próximas etapas

- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
