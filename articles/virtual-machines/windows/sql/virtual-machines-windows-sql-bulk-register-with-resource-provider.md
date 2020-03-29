---
title: Registro em massa de máquinas virtuais SQL no Azure com o provedor de recursos SQL VM | Microsoft Docs
description: Registro em massa De VMs do SQL Server com o provedor de recursos SQL VM para melhorar a capacidade de gerenciamento.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353879"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registro em massa de máquinas virtuais SQL no Azure com o provedor de recursos SQL VM

Este artigo descreve como registrar em massa sua máquina virtual SQL Server (VM) no Azure com o provedor de recursos SQL VM usando o `Register-SqlVMs` cmdlet PowerShell.

O `Register-SqlVMs` cmdlet pode ser usado para registrar todas as máquinas virtuais em uma determinada lista de assinaturas, grupos de recursos ou uma lista de máquinas virtuais específicas. O cmdlet registrará as máquinas virtuais no modo de gerenciamento _leve_ e, em seguida, gerará um [relatório e um arquivo de registro](#output-description). 

O processo de registro não traz nenhum risco, não tem tempo de inatividade e não reiniciará o SQL Server ou a máquina virtual. 

Para obter mais informações sobre o provedor de recursos, consulte [o provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar seu VM do SQL Server com o provedor de recursos, você precisará do seguinte: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/) que foi [registrada no provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) e contém máquinas virtuais SQL Server não registradas. 
- As credenciais do cliente usadas para registrar as máquinas virtuais existem em qualquer uma das seguintes funções de RBAC: **Contribuinte da Máquina Virtual,** **Contribuinte**ou **Proprietário.** 
- A versão mais recente do [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- A versão mais recente do [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Introdução

Antes de prosseguir, você deve primeiro criar uma cópia local do script, importá-lo como um módulo PowerShell e conectar-se ao Azure. 

### <a name="create-script"></a>Criar script

Para criar o script, copie o [script completo](#full-script) do final `RegisterSqlVMs.psm1`deste artigo e salve-o localmente como . 

### <a name="import-script"></a>Script de importação

Uma vez que o script é criado, você pode importá-lo como um módulo no terminal Powershell. 

Abra um terminal PowerShell administrativo e `RegisterSqlVMs.psm1` navegue até onde você salvou o arquivo. Em seguida, execute o seguinte cmdlet PowerShell para importar o script como um módulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Conectar-se ao Azure

Use o seguinte cmdlet PowerShell para conectar ao Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Todas as VMs em lista de assinaturas 

Use o cmdlet a seguir para registrar todas as máquinas virtuais do SQL Server em uma lista de assinaturas:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Saída de exemplo: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Todas as VMs em uma única assinatura

Use o cmdlet a seguir para registrar todas as máquinas virtuais do SQL Server em uma única assinatura: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Saída de exemplo:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Todas as VMs em vários grupos de recursos

Use o cmdlet a seguir para registrar todas as máquinas virtuais do SQL Server em vários grupos de recursos dentro de uma única assinatura:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Saída de exemplo:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Todas as VMs em um grupo de recursos

Use o cmdlet a seguir para registrar todas as máquinas virtuais do SQL Server em um único grupo de recursos: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Saída de exemplo:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>VMs específicos em grupo de recursos únicos

Use o cmdlet a seguir para registrar máquinas virtuais específicas do SQL Server em um único grupo de recursos:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Saída de exemplo:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>VM específico

Use o cmdlet a seguir para registrar uma máquina virtual específica do SQL Server: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Saída de exemplo:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Descrição da saída

Tanto um relatório quanto um arquivo `Register-SqlVMs` de registro são gerados cada vez que o cmdlet é usado. 

### <a name="report"></a>Relatório

O relatório é `.txt` gerado `RegisterSqlVMScriptReport<Timestamp>.txt` como um arquivo nomeado onde o carimbo de data e hora é o momento em que o cmdlet foi iniciado. O relatório lista os seguintes detalhes:

| **Valor de saída** | **Descrição** |
| :--------------  | :-------------- | 
| Número de assinaturas de registro falhou porque você não tem acesso ou credenciais estão incorretas | Isso fornece o número e a lista de assinaturas que tiveram problemas com a autenticação fornecida. O erro detalhado pode ser encontrado no registro procurando o ID de assinatura. | 
| Número de assinaturas que não puderam ser testadas porque não estão registradas no RP | Esta seção contém a contagem e a lista de assinaturas que não foram registradas no provedor de recursos SQL VM. |
| Total de VMs encontrados | A contagem de máquinas virtuais que foram encontradas no escopo dos parâmetros passados para o cmdlet. | 
| VMs já cadastrados | A contagem de máquinas virtuais que foram ignoradas como já estavam registradas no provedor de recursos. |
| Número de VMs registradas com sucesso | A contagem de máquinas virtuais que foram registradas com sucesso após a execução do cmdlet. Lista as máquinas virtuais `SubscriptionID, Resource Group, Virtual Machine`registradas no formato . | 
| Número de VMs não foi registrado devido a erro | Contagem de máquinas virtuais que não se registraram devido a algum erro. Os detalhes do erro podem ser encontrados no arquivo de registro. | 
| Número de VMs ignorados como o VM ou o agente gust na VM não está funcionando | Contagem e lista de máquinas virtuais que não podiam ser registradas como a máquina virtual ou o agente convidado na máquina virtual não estavam funcionando. Estes podem ser repetidos assim que a máquina virtual ou o agente convidado forem iniciados. Os detalhes podem ser encontrados no arquivo de registro. |
| Número de VMs ignorados, pois eles não estão executando o SQL Server no Windows | Contagem de máquinas virtuais que foram ignoradas por não estarem executando o SQL Server ou não são uma máquina virtual do Windows. As máquinas virtuais estão `SubscriptionID, Resource Group, Virtual Machine`listadas no formato . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Os erros são registrados no `VMsNotRegisteredDueToError<Timestamp>.log` arquivo de registro nomeado onde o carimbo de data e hora é a hora em que o script foi iniciado. Se o erro estiver no nível de assinatura, o log contém o SubscriptionID separado por comuma e a mensagem de erro. Se o erro for com o registro da máquina virtual, o log contém o ID de assinatura, nome do grupo de recursos, nome da máquina virtual, código de erro e mensagem separados por commas. 

## <a name="remarks"></a>Comentários

Ao registrar VMs do SQL Server com o provedor de recursos usando o script fornecido, considere o seguinte:

- O registro com o provedor de recursos requer um agente convidado em execução no VM do SQL Server. As imagens do Windows Server 2008 não possuem um agente convidado, então essas máquinas virtuais falharão e devem ser registradas manualmente usando o [modo de gerenciamento NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- Há uma lógica de repetição incorporada para superar erros transparentes. Se a máquina virtual for registrada com sucesso, então é uma operação rápida. No entanto, se o registro falhar, cada máquina virtual será repetidanovamente.  Como tal, você deve permitir um tempo significativo para concluir o processo de registro - embora o requisito de tempo real dependa do tipo e número de erros. 

## <a name="full-script"></a>Script completo

Para obter o script completo no GitHub, consulte [VMs SQL de registro em massa com Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copie o script completo `RegisterSqLVMs.psm1`e salve-o como .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FaQ para SQL Server em um VM Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para SQL Server em um VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
