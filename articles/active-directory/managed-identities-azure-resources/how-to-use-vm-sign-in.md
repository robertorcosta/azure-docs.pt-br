---
title: Usar identidades gerenciadas em uma VM do Azure para entrar-Azure AD
description: Instruções e exemplos passo a passo para o uso de identidades gerenciadas de VM do Azure para a entidade de serviço de recursos do Azure para login do cliente de script e acesso a recursos.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3d6d128677d2e82f4750a7771885474bf284fb1
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184224"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para entrar 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de script do PowerShell e da CLI para entrada usando identidades gerenciadas para a entidade de serviço de recursos do Azure e orientação sobre tópicos importantes, como tratamento de erros.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos do Azure PowerShell ou da CLI do Azure neste artigo, instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) ou da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todos os exemplos de script neste artigo presumem que o cliente de linha de comando está sendo executado em uma VM com identidades gerenciadas para recursos do Azure habilitados. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) ou um dos artigos variantes (usando o PowerShell, CLI, um modelo ou um SDK do Azure). 
> - Para evitar erros durante o acesso a recursos, a identidade gerenciada da VM deve receber pelo menos o acesso "Reader" no escopo apropriado (a VM ou superior) para permitir operações do Azure Resource Manager na VM. Consulte [Atribuir identidades gerenciadas para acesso de recursos do Azure a um recurso usando o portal do Azure](howto-assign-access-portal.md) para obter detalhes.

## <a name="overview"></a>Visão geral

Identidades gerenciadas para recursos do Azure fornecem um [objeto principal de serviço](../develop/developer-glossary.md#service-principal-object), que é [criado ao habilitar identidades gerenciadas para recursos do Azure](overview.md#how-does-it-work) na VM. A entidade de serviço pode receber acesso aos recursos do Azure e pode ser usada como uma identidade pelos clientes de script/linha de comando para entrada e acesso aos recursos. Tradicionalmente, para acessar recursos protegidos em sua própria identidade, um cliente de script precisa:  

   - estar registrado e ter o consentimento do Azure AD como um aplicativo cliente Web/confidencial
   - entrar com sua entidade de serviço, usando as credenciais do aplicativo (que estão provavelmente inseridas no script)

Com as identidades gerenciadas dos recursos do Azure, seu cliente de script não precisa mais fazer nada, pois pode entrar nas identidades gerenciadas do objeto principal do serviço de recursos do Azure. 

## <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

1. Entrar no Azure AD sob a identidade da VM gerenciado para entidade de serviço de recursos do Azure  
2. Chamar o Azure Resource Manager e obter a ID da entidade de serviço da VM. A CLI cuida do gerenciamento de uso/aquisição de token para você automaticamente. Substitua o nome da máquina virtual para `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>PowerShell do Azure

O script a seguir demonstra como:

1. Entrar no Azure AD sob a identidade da VM gerenciado para entidade de serviço de recursos do Azure  
2. Chamar um cmdlet do Azure Resource Manager para obter informações sobre a VM. O PowerShell cuida do gerenciamento de uso de token para você automaticamente.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Serviços do Azure que oferecem suporte à autenticação do Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de recursos que oferecem suporte ao Azure AD e que foram testados com identidades gerenciadas para recursos do Azure e seus respectivos IDs de recursos.

## <a name="error-handling-guidance"></a>Diretrizes sobre tratamento de erro 

Respostas como a seguinte podem indicar que a identidade gerenciada da VM para recursos do Azure não foi configurada corretamente:

- PowerShell: *Invoke-WebRequest: não é possível conectar-se ao servidor remoto*
- CLI: *MSI: falha ao recuperar um token do `http://localhost:50342/oauth2/token` com um erro de ' HTTPConnectionPool (host = ' localhost ', Port = 50342)* 

Se você receber um desses erros, retorne para a VM do Azure no [Portal do Azure](https://portal.azure.com) e:

- Vá para a página **Identidade** e assegure que **Sistema atribuído** esteja definido como "Sim".
- Vá para a página **Extensões** e verifique se as identidades gerenciadas da extensão de recursos do Azure  **(reprovação planejada para janeiro de 2019)** foram implantadas com êxito.

Se um deles estiver incorreto, talvez seja necessário reimplementar as identidades gerenciadas dos recursos do Azure no seu recurso novamente ou solucionar a falha de implantação. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) se precisar de ajuda com a configuração da VM.

## <a name="next-steps"></a>Próximas etapas

- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md) ou [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](qs-configure-cli-windows-vm.md)






