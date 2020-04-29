---
title: Corrigir servidores de configuração de estado de automação do Azure não compatível
description: Como reaplicar as configurações sob demanda aos servidores em que o estado da configuração foi descompasso
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406076"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Corrigir servidores DSC não compatíveis

Quando os servidores são registrados com a configuração de estado da automação do Azure, o `ApplyOnly`modo `ApplyandMonitor`de configuração `ApplyAndAutoCorrect`é definido como, ou. Se o modo não estiver definido `ApplyAndAutoCorrect`como, os servidores que desaparecerem de um estado compatível por qualquer motivo permanecerão não compatíveis até que sejam corrigidos manualmente.

A computação do Azure oferece um recurso chamado executar comando que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esse recurso ao corrigir manualmente a descompasso de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Corrigir descompasso das máquinas virtuais do Windows usando o PowerShell

Para obter instruções detalhadas usando o recurso executar comando em máquinas virtuais do Windows, consulte a página de documentação [executar scripts do PowerShell em sua VM do Windows com o comando executar](/azure/virtual-machines/windows/run-command).

Para forçar um nó de configuração de estado da automação do Azure a baixar a configuração mais recente e aplicá-la, use o cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Corrigir descompasso das máquinas virtuais do Linux

A funcionalidade semelhante não está disponível atualmente para servidores Linux.
A única opção é repetir o processo de registro.
Para nós do Azure, você pode corrigir a descompasso do portal do Azure ou usando cmdlets de módulo AZ. Os detalhes sobre esse processo estão documentados em [máquinas de integração para o gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal).
Para nós híbridos, você pode corrigir a descompasso usando os scripts Python incluídos.
Confira o [repositório DSC do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma referência de cmdlet do PowerShell, consulte [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).