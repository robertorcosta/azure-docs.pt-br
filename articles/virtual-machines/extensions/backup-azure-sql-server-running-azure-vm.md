---
title: Backup do Azure para SQL Server em execução no Azure VM
description: Neste artigo, saiba como registrar o Azure Backup no SQL Server em execução em uma máquina virtual Do Azure.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247377"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup do Azure para SQL Server em execução no Azure VM

O Azure Backup, entre outras ofertas, oferece suporte para fazer backup de cargas de trabalho, como o SQL Server em execução em VMs azure. Como o aplicativo SQL está sendo executado dentro de uma VM Do Azure, o serviço de backup precisa de permissão para acessar o aplicativo e buscar os detalhes necessários.
Para fazer isso, o Azure Backup instala a extensão **AzureBackupWindowsWorkload** na VM, na qual o SQL Server está sendo executado, durante o processo de registro acionado pelo usuário.

## <a name="prerequisites"></a>Pré-requisitos

Para obter a lista de cenários suportados, consulte a [matriz de suporte](../../backup/sql-support-matrix.md#scenario-support) suportada pelo Azure Backup.

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup suporta tags NSG, implantando um servidor proxy ou faixas IP listadas; para obter detalhes sobre cada um dos métodos, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Esquema de extensão

O esquema de extensão e os valores de propriedade são os valores de configuração (configurações em tempo de execução) que o serviço está passando para a API de CRP. Esses valores de configuração são usados durante o registro e atualização. A extensão **AzureBackupWindowsWorkload** também usa esse esquema. O esquema está pré-definido; um novo parâmetro pode ser adicionado no campo objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

O JSON a seguir mostra o esquema da extensão WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Valores de propriedade

Nome | Valor/exemplo | Tipo de dados
 --- | --- | ---
localidade | pt-br  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzJMjQxYTItOGRjNy00ZGE5LWI4NTMTMMjjdjYTJhNDZLLM2ZkiwiSW dGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlOkIjoiMDU5NWIWOGEtYZI4Zi00ZmFlLWE5ODItOTkwOWMyMGJJJJJJvhiiwiU3Vic2NyaXB0aW9uSWQiOi JkNGEzOTliNy1iYjAyLTQ2MWMODdmYS1jNTM5ODI3ZTzNTQiLJTVVAWD250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhWEyTtct Yjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2Vydmlzu5hbWUiOjUsIlZcnzpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWTDDMMuY29tIn1dfQ ==" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (configurações protegidas) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzJMjQxYTItOGRjNy00ZGE5LWI4NTMTMMjjdjYTJhNDZLLM2ZkiwiSW dGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlOkIjoiMDU5NWIWOGEtYZI4Zi00ZmFlLWE5ODItOTkwOWMyMGJJJJJJvhiiwiU3Vic2NyaXB0aW9uSWQiOi JkNGEzOTliNy1iYjAyLTQ2MWMODdmYS1jNTM5ODI3ZTzNTQiLJTVVAWD250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhWEyTtct Yjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2Vydmlzu5hbWUiOjUsIlZcnzpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWTDDMMuY29tIn1dfQ ==" | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>Implantação de modelo

Recomendamos adicionar a extensão AzureBackupWindowsWorkload a uma máquina virtual é habilitando o backup do SQL Server na máquina virtual. Isso pode ser alcançado através do [modelo Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) projetado para automatizar o backup em uma VM do SQL Server.

## <a name="powershell-deployment"></a>Implantação do PowerShell

Você precisa 'registrar' a VM do Azure que contém o aplicativo SQL com um cofre de serviços de recuperação. Durante o registro, a extensão AzureBackupWindowsWorkload é instalada na VM. Use [o cmdlet Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) para registrar o VM.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando retornará um contêiner de **backup** deste recurso e o status será **registrado**.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) sobre as diretrizes de solução de problemas do Azure SQL Server VM
- [Perguntas comuns](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sobre o backup de bancos de dados SQL Server que são executados em máquinas virtuais (VMs) do Azure e que usam o serviço de backup do Azure.
