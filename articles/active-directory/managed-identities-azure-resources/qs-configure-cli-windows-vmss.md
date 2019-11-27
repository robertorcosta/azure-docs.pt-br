---
title: Configurar identidades gerenciadas no conjunto de dimensionamento de máquinas virtuais-CLI do Azure-Azure AD
description: Instruções passo a passo para a configuração de identidades gerenciadas atribuídas pelo usuário e pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure, usando CLI do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
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
ms.openlocfilehash: 74d8faed0637b5b5b82e1ad450a3b1535bb063e4
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547304"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais do Azure, usando o CLI do Azure:
- Habilitar e desabilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade gerenciada atribuída ao usuário em um conjunto de dimensionamento de máquinas virtuais do Azure


## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a seção de [visão geral](overview.md). **Não deixe de analisar a [diferença entre uma identidade gerenciada atribuída pelo sistema e atribuída pelo usuário](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisa das seguintes atribuições de controle de acesso baseado em função do Azure:

    > [!NOTE]
    > Não são necessárias atribuições de função do diretório adicionais do Azure AD.

    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou ao sistema de um conjunto de dimensionamento de máquinas virtuais.
    - Função de [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerenciada atribuída ao usuário.
    - [Papel de Operador de Identidade Gerenciado](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquina virtual.
- Para executar os exemplos de script da CLI, você tem três opções:
    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou mais recente), se você preferir usar um console da CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a versão mais recente do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema para um conjunto de dimensionamento de máquinas virtuais do Azure usando o CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais com a identidade gerenciada atribuída ao sistema habilitada:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta associada à assinatura do Azure, na qual você deseja implantar o conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
   az login
   ```

2. Crie um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para confinamento e implantação do conjunto de dimensionamento de máquinas virtuais e os recursos relacionados, usando [az group create](/cli/azure/group/#az-group-create). Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie um conjunto de dimensionamento de máquinas virtuais usando [az vmss create](/cli/azure/vmss/#az-vmss-create) . O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS* com uma identidade gerenciada atribuída ao sistema, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquina virtual do Azure existente

Se você precisar habilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

   ```azurecli-interactive
   az login
   ```

2. Use o comando [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) para habilitar uma identidade gerenciada atribuída ao sistema para uma VM existente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade gerenciada atribuída ao sistema, mas ainda precisar de identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema e não tiver identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>identidade gerenciada atribuída ao usuário

Nesta seção, você aprende como habilitar e remover uma identidade gerenciada atribuída ao usuário usando a CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Esta seção orienta a criação de um conjunto de dimensionamento de máquinas virtuais e a atribuição de uma identidade gerenciada atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais. Se você já tiver um conjunto de dimensionamento de máquinas virtuais que deseja usar, pule esta seção e vá para a próxima.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [grupo de recursos ](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para independência e implantação da identidade gerenciada atribuída ao usuário, usando [ az group create ](/cli/azure/group/#az-group-create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte. O valor do recurso `id` atribuído à identidade gerenciada atribuída ao usuário é usado na etapa a seguir.

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

3. Crie um conjunto de dimensionamento de máquinas virtuais usando [AZ vmss Create](/cli/azure/vmss/#az-vmss-create). O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais associado à nova identidade gerenciada atribuída pelo usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua os valores dos parâmetros `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Atribua a identidade gerenciada atribuída pelo usuário ao seu conjunto de dimensionamento de máquinas virtuais usando [AZ vmss Identity Assign](/cli/azure/vmss/identity). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` é um recurso da identidade de usuário atribuída `name` propriedade, conforme criado na etapa anterior:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Para remover uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais, use [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Se esta for a única identidade gerenciada atribuída ao usuário atribuída ao conjunto de dimensionamento de máquinas virtuais, `UserAssigned` será removido do valor do tipo de identidade.  Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade `name` da identidade gerenciada atribuída ao usuário, que pode ser localizada na seção de identidade do conjunto de dimensionamento de máquinas virtuais usando `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o seu conjunto de dimensionamento de máquina virtual não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades gerenciadas atribuídas pelo usuário, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas ao sistema e atribuídas ao usuário, você poderá remover todas as identidades atribuídas ao usuário, alternando para usar somente a identidade gerenciada atribuída ao sistema. Use o seguinte comando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Próximos passos

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para Início Rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure completo, consulte: 

  - [Criar um conjunto de dimensionamento de máquinas virtuais com CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















