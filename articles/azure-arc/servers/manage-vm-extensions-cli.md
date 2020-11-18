---
title: Habilitar extensão de VM usando CLI do Azure
description: Este artigo descreve como implantar extensões de máquina virtual para servidores habilitados para Arc do Azure em execução em ambientes de nuvem híbrida usando o CLI do Azure.
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: bf0a3e0940efc7e79adbe9f763ffdf34ea690fac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833258"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Habilitar extensões de VM do Azure usando o CLI do Azure

Este artigo mostra como implantar e desinstalar extensões de VM do Azure, com suporte dos servidores habilitados para Arc do Azure, para uma máquina híbrida Linux ou Windows usando o CLI do Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Os comandos ConnectedMachine não são enviados como parte do CLI do Azure. Antes de usar o CLI do Azure para gerenciar extensões de VM em seu servidor híbrido gerenciado por servidores habilitados para Arc, você precisa carregar a extensão ConnectedMachine. Execute o seguinte comando para obtê-lo:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Habilitar extensão

Para habilitar uma extensão de VM em seu servidor habilitado para Arc, use [AZ connectedmachine Machine-Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) com os `--machine-name` `--extension-name` parâmetros,, `--location` ,, `--type` `settings` e `--publisher` .

O exemplo a seguir habilita a extensão de VM Log Analytics em um servidor Linux habilitado para Arc:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

O exemplo a seguir habilita a extensão de script personalizado em um servidor habilitado para Arc:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

O exemplo a seguir habilita a extensão de VM Key Vault (versão prévia) em um servidor habilitado para Arc:

```azurecli
az connectedmachine machine-extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Listar extensões instaladas

Para obter uma lista das extensões de VM em seu servidor habilitado para Arc, use [AZ connectedmachine Machine-extensão List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) com `--machine-name` os `--resource-group` parâmetros e.

Exemplo:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Por padrão, a saída de comandos da CLI do Azure é em JSON (JavaScript Object Notation). Para alterar a saída padrão para uma lista ou tabela, por exemplo, use [az configure --output](/cli/azure/reference-index). Você também pode adicionar `--output` a qualquer comando para realizar uma alteração uma única vez no formato da saída.

O exemplo a seguir mostra a saída JSON parcial do `az connectedmachine machine-extension -list` comando:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Remover uma extensão instalada

Para remover uma extensão de VM instalada em seu servidor habilitado para Arc, use [AZ connectedmachine Machine-Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) com os `--extension-name` `--machine-name` `--resource-group` parâmetros e.

Por exemplo, para remover a extensão de VM Log Analytics para Linux, execute o seguinte comando:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

- Você pode implantar, gerenciar e remover extensões de VM usando o [Azure PowerShell](manage-vm-extensions-powershell.md), dos modelos [portal do Azure](manage-vm-extensions-portal.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).

- As informações de solução de problemas podem ser encontradas no [guia solucionar problemas de extensões de VM](troubleshoot-vm-extensions.md).
