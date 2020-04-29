---
title: Exibir a entidade de serviço de uma identidade gerenciada no portal do Azure-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o portal do Azure.
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
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79298590"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Exibir a entidade de serviço de uma identidade gerenciada no portal do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende como exibir a entidade de serviço de uma identidade gerenciada usando o portal do Azure.

 > [!NOTE] 
 > As entidades de serviço são aplicativos empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Caso ainda não tenha uma conta do Azure, [inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite a [identidade designada pelo sistema em uma máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicativo](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Esse procedimento demonstra como exibir a entidade de serviço de uma VM com a identidade atribuída do sistema habilitada (as mesmas etapas se aplicam para um aplicativo).

1. Clique no **Azure Active Directory** e, em seguida, clique em **Aplicativos empresariais**.
2. Em **tipo de aplicativo**, escolha **todos os aplicativos** e clique em **aplicar**.
3. Na caixa de filtro de pesquisa, digite o nome da VM ou aplicativo que tem identidade gerenciada habilitada ou escolha-o na lista apresentada.

   ![Exibir as entidades gerenciadas de serviço de identidade no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Próximas etapas

[Identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview)

