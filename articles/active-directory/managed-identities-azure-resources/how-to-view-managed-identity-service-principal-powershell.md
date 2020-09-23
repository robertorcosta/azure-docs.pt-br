---
title: Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.
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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f9ac119a3365eff39fe1a65ff8b553d3900b117
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969315"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende a exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Caso ainda não tenha uma conta do Azure, [inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite a [identidade designada pelo sistema em uma máquina virtual](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) ou [aplicativo](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)e, em seguida, entre no Azure usando `Connect-AzAccount` .

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

O comando a seguir demonstra como exibir a entidade de serviço de um aplicativo ou VM com identidade atribuída pelo sistema habilitada. Substitua `<VM or application name>` pelos seus próprios valores.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como exibir entidades de serviço do Azure AD usando o PowerShell, confira [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).
