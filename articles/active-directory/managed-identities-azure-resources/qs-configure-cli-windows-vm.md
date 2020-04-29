---
title: Configurar identidades gerenciadas na VM do Azure usando o CLI do Azure-Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas atribuídas ao usuário e ao sistema em uma VM do Azure usando a CLI do Azure.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244141"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando a CLI do Azure, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em uma VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, você tem três opções:
    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a última versão da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir usar um console de CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a versão mais recente do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema em uma VM do Azure usando a CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída ao sistema habilitada, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM:

   ```azurecli-interactive
   az login
   ```

2. Criar um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#az-group-create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#az-vm-create). O exemplo a seguir cria uma VM nomeada *myVM* com uma identidade gerenciada atribuída ao sistema, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída ao sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função [Colaboração da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```azurecli-interactive
   az login
   ```

2. Use [az vm identity assign](/cli/azure/vm/identity/) com `identity assign` para habilitar a identidade atribuída ao sistema para uma VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desabilitar uma identidade atribuída ao sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída ao sistema em uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

Se você tiver uma Máquina Virtual que não precisa mais da identidade atribuída ao sistema, mas ainda precisar de identidades atribuídas ao usuário, use o comando a seguir:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se você tiver uma máquina virtual que não precisa mais da identidade atribuída ao sistema e não tiver identidades atribuídas ao usuário, use o comando a seguir:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade gerenciada atribuída ao usuário de uma VM do Azure usando a CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM durante sua criação, sua conta precisa das atribuições de função [Contribuinte virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [grupo de recursos ](~/articles/azure-resource-manager/management/overview.md#terminology) para independência e implantação da identidade gerenciada atribuída ao usuário, usando [ az group create ](/cli/azure/group/#az-group-create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte. O valor da ID de recurso atribuído à identidade gerenciada atribuída pelo usuário é usado na etapa a seguir.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#az-vm-create). O exemplo a seguir cria uma VM associada à nova identidade atribuída ao usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua os valores dos parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada usuário atribuído a uma VM existente do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Crie uma identidade atribuída pelo usuário usando [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade atribuída ao usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > Atualmente, não há suporte para criação de identidades gerenciadas atribuídas ao usuário com caracteres especiais (ou seja, sublinhado) no nome. Use caracteres alfanuméricos. Procure novamente por atualizações.  Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Atribua a identidade atribuída ao usuário à VM usando [az vm identity assign](/cli/azure/vm). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade `name` do recurso de identidade gerenciada atribuída ao usuário, conforme criado na etapa anterior:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída ao usuário a uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor). 

Se essa for a única identidade gerenciada atribuída ao usuário atribuída à máquina virtual, `UserAssigned` será removido do valor do tipo de identidade.  Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade `name` da identidade atribuída ao usuário, que pode ser localizada na seção de identidade da máquina virtual usando `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se a VM não tiver uma identidade gerenciada atribuída ao sistema e você quiser remover todas as identidades atribuídas ao usuário, use o comando a seguir:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se a VM tiver identidades atribuídas ao sistema e atribuídas ao usuário, você poderá remover todas as identidades atribuídas ao usuário, alternando para usar somente atribuídas ao sistema. Use o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Próximas etapas
- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte: 
  - [Crie máquinas virtuais Windows com o CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Crie máquinas virtuais Linux com o CLI](../../virtual-machines/linux/quick-create-cli.md) 

















