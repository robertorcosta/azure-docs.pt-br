---
title: Registrar em massa máquinas virtuais do SQL no Azure com o provedor de recursos da VM do SQL | Microsoft Docs
description: Registre em massa VMs do SQL Server com o provedor de recursos da VM do SQL para melhorar a capacidade de gerenciamento.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b83a44db98907f505c7bf0d8302470cf3031a967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761253"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrar várias máquinas virtuais do SQL no Azure com o provedor de recursos da VM do SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como registrar suas VMs (máquinas virtuais) do SQL Server em massa no Azure com o provedor de recursos de VM do SQL usando o `Register-SqlVMs`cmdlet do PowerShell.

Este artigo ensina você a registrar SQL Server VMs em massa. Como alternativa, você pode registrar [todas as VMs de SQL Server automaticamente ou as](sql-vm-resource-provider-automatic-registration.md) [VMs SQL Server individuais](sql-vm-resource-provider-register.md). 

## <a name="overview"></a>Visão geral

O cmdlet `Register-SqlVMs` pode ser usado para registrar todas as máquinas virtuais em uma determinada lista de assinaturas, em grupos de recursos ou em uma lista de máquinas virtuais específica. O cmdlet registra as máquinas virtuais no modo de gerenciamento _leve_ e gera um relatório [e um arquivo de log](#output-description). 

O processo de registro não tem riscos, não tem nenhum tempo de inatividade e não reinicia o SQL Server ou a máquina virtual. 

Para obter mais informações sobre o provedor de recursos, veja [Provedor de recursos da VM do SQL](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM do SQL Server no provedor de recursos, você precisa de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/) [registrada no provedor de recursos](sql-vm-resource-provider-register.md#register-subscription-with-rp) e que contenha máquinas virtuais não registradas do SQL Server. 
- As credenciais de cliente usadas para registrar as máquinas virtuais existem em qualquer uma das seguintes funções do Azure: **colaborador de máquina virtual**, **colaborador**ou **proprietário**. 
- A versão mais recente do [AZ PowerShell](/powershell/azure/new-azureps-module-az). 
- A versão mais recente do [AZ.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="get-started"></a>Introdução

Antes de continuar, primeiro você deve criar uma cópia local do script, importá-la como um módulo do PowerShell e conectá-la ao Azure. 

### <a name="create-the-script"></a>Criar o script

Para criar o script, copie o [script completo](#full-script) no fim deste artigo e salve-o localmente como `RegisterSqlVMs.psm1`. 

### <a name="import-the-script"></a>Importar o script

Depois de criar o script, você poderá importá-lo como um módulo no terminal do PowerShell. 

Abra um terminal administrativo do PowerShell e acesse o local em que o arquivo `RegisterSqlVMs.psm1` foi salvo. Execute o seguinte cmdlet do PowerShell para importar o script como um módulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Conectar-se ao Azure

Use o seguinte cmdlet do PowerShell para se conectar ao Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Registrar todas as VMs em uma lista de assinaturas 

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em uma lista de assinaturas:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Saída de exemplo: 

```
Number of subscriptions registration failed for 
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

## <a name="register-all-vms-in-a-single-subscription"></a>Registrar todas as VMs em uma assinatura única

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em uma assinatura única: 

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

## <a name="register-all-vms-in-multiple-resource-groups"></a>Registrar todas as VMs em vários grupos de recursos

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em vários grupos de recursos em uma assinatura única:

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

## <a name="register-all-vms-in-a-resource-group"></a>Registrar todas as VMs em um grupo de recursos

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em um grupo de recursos único: 

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

## <a name="register-specific-vms-in-a-single-resource-group"></a>Registrar VMs específicas em um único grupo de recursos

Use o seguinte cmdlet para registrar máquinas virtuais do SQL Server específicas em um grupo de recursos único:

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

## <a name="register-a-specific-vm"></a>Registrar uma VM específica

Use o seguinte cmdlet para registrar uma máquina virtual do SQL Server específica: 

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

Um relatório e um arquivo de log são gerados sempre que o cmdlet `Register-SqlVMs` é usado. 

### <a name="report"></a>Relatório

O relatório é gerado como um arquivo `.txt` chamado `RegisterSqlVMScriptReport<Timestamp>.txt`, em que o carimbo de data/hora é o horário em que o cmdlet foi iniciado. O relatório lista os seguintes detalhes:

| **Valor de saída** | **Descrição** |
| :--------------  | :-------------- | 
| Ocorreu falha no registro do número de assinaturas porque você não tem acesso ou as credenciais estão incorretas | Isso fornece o número e a lista de assinaturas que tiveram problemas com a autenticação fornecida. O erro detalhado pode ser encontrado no log ao pesquisar pela ID da assinatura. | 
| Número de assinaturas que não puderam ser testadas, pois não estavam registradas no RP | Esta seção aborda a contagem e a lista de assinaturas que não foram registradas no provedor de recursos da VM do SQL. |
| Total de VMs encontradas | A contagem de máquinas virtuais encontradas no escopo dos parâmetros passados para o cmdlet. | 
| VMs já registradas | A contagem de máquinas virtuais que foram ignoradas porque já estavam registradas no provedor de recursos. |
| Número de VMs registradas com êxito | A contagem de máquinas virtuais que foram registradas com êxito após a execução do cmdlet. Lista as máquinas virtuais registradas no formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| Número de VMs com falha ao registrar devido a um erro | Contagem de máquinas virtuais com falha ao registrar devido a algum erro. Os detalhes do erro podem ser encontrados no arquivo de log. | 
| Número de VMs ignoradas porque a VM ou o agente convidado na VM não estava em execução | A contagem e a lista de máquinas virtuais que não puderam ser registradas porque a máquina virtual ou o agente convidado na máquina virtual não estava em execução. Será possível tentar novamente depois de iniciar a máquina virtual ou o agente convidado. Os detalhes podem ser encontrados no arquivo de log. |
| Número de VMs ignoradas porque não estavam executando o SQL Server no Windows | Contagem de máquinas virtuais que foram ignoradas porque não estavam executando o SQL Server ou não eram máquinas virtuais do Windows. As máquinas virtuais são listadas no formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Os erros são registrados no arquivo de log chamado `VMsNotRegisteredDueToError<Timestamp>.log`, em que o carimbo de data/hora é a hora em que o script foi iniciado. Se o erro ocorrer em nível de assinatura, o log conterá a ID da assinatura e a mensagem de erro separadas por vírgulas. Se o erro ocorrer no registro da máquina virtual, o log conterá a ID da assinatura, o nome do grupo de recursos, o nome da máquina virtual, o código de erro e a mensagem separados por vírgulas. 

## <a name="remarks"></a>Comentários

Ao registrar VMs do SQL Server no provedor de recursos usando o script fornecido, considere o seguinte:

- O registro com o provedor de recursos requer um agente convidado em execução na VM do SQL Server. As imagens do Windows Server 2008 não têm um agente convidado, portanto, ocorrerá falha nessas máquinas virtuais e será necessário registrá-las manualmente usando o [modo de gerenciamento NoAgent](sql-vm-resource-provider-register.md#management-modes).
- Há uma lógica de repetição interna para solucionar erros transparentes. Se a máquina virtual for registrada com êxito, a operação será rápida. No entanto, em caso de falha do registro, cada máquina virtual será testada novamente.  Portanto, o processo de registro requer um tempo significativo para ser concluído, embora o requisito de tempo real dependa do tipo e da quantidade de erros. 

## <a name="full-script"></a>Script completo

Para ver o script completo no GitHub, confira [Registrar VMs do SQL em massa com o Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copie o script completo e salve-o como `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)
