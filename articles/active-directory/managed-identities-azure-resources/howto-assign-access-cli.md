---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o CLI do Azure-Azure AD
description: Instruções passo a passo para atribuir uma identidade gerenciada em um recurso, acesso a outro recurso, usando CLI do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969256"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerenciada a um recurso usando a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de identidade gerenciada de uma máquina virtual do Azure ou Conjunto de dimensionamento de máquinas virtuais a uma conta de armazenamento do Azure usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) e, em seguida, entre no Azure usando [az login](/cli/azure/reference-index#az-login). Use uma conta associada à assinatura do Azure na qual você gostaria de criar recursos.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC do Azure para atribuir um acesso de identidade gerenciada a outro recurso

Após habilitar a identidade gerenciada em um recurso do Azure, como uma [máquina virtual do Azure](qs-configure-cli-windows-vm.md) ou [Conjunto de dimensionamento de máquinas virtuais](qs-configure-cli-windows-vmss.md): 

1. Neste exemplo, fornecemos acesso de uma máquina virtual do Azure para uma conta de armazenamento. Primeiro, usamos [az resource list](/cli/azure/resource/#az-resource-list) para obter a entidade de serviço da máquina virtual nomeada myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para um conjunto de dimensionamento de máquinas virtuais do Azure, o comando é o mesmo, exceto aqui, você obtém a entidade de serviço para o conjunto de dimensionamento de máquinas virtuais nomeado "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Após obter a ID da entidade e serviço, use [criar atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create) para que a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais configurem o acesso do "Leitor" a uma conta de armazenamento nomeada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](qs-configure-cli-windows-vm.md).
- Para habilitar a identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](qs-configure-cli-windows-vmss.md).
