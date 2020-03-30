---
title: Habilite identidades gerenciadas em suas VMs de laboratório em Laboratórios Azure DevTest
description: Este artigo mostra como um proprietário de laboratório pode habilitar identidades gerenciadas atribuídas pelo usuário em suas máquinas virtuais de laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692661"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Habilite identidades gerenciadas atribuídas pelo usuário em máquinas virtuais de laboratório no Azure DevTest Labs
Como proprietário de laboratório, você pode habilitar identidades gerenciadas atribuídas pelo usuário em suas máquinas virtuais de laboratório (VMs) no Azure DevTest Labs.

Uma identidade gerenciada pode ser usada para autenticar qualquer serviço que suporte a autenticação do Azure Active Directory (AD), incluindo o Key Vault, sem passar nenhuma credencial no código. Para obter mais informações sobre identidades gerenciadas, consulte [O que é identidades gerenciadas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Com esse recurso, os usuários do laboratório podem compartilhar recursos do Azure, como o Azure SQL Database, no contexto do laboratório. A autenticação do recurso é cuidada pela própria identidade. Uma vez configurado, todos os VM de laboratório existentes/recém-criados serão habilitados com essa identidade. Os usuários de laboratório podem acessar recursos uma vez conectados às suas máquinas.

> [!NOTE]
> Você pode adicionar várias identidades gerenciadas atribuídas ao usuário para serem habilitadas em suas VMs de laboratório.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Para adicionar uma identidade gerenciada atribuída ao usuário para VMs de laboratório, siga estas etapas:

1. [Crie uma identidade gerenciada atribuída pelo usuário em sua assinatura](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navegue até a página **Configuração e políticas** para o seu laboratório.
2. Selecione **''Visualizar' (Visualização)** no menu à esquerda.
3. Selecione a guia **Máquina Virtual.**
4. Selecione **Adicionar** para selecionar uma identidade existente em uma lista de sossedes pré-preenchida. 

    ![Adicionar botão de identidade](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selecione uma **identidade gerenciada pelo usuário** existente na lista de desvassos e selecione **OK**. 

    ![Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Use API

1.  Depois de criar uma identidade, anote o ID de recurso da identidade. Deve parecer a seguinte amostra: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Execute um método PUT HTTPS para adicionar um novo recurso **ServiceRunner** ao laboratório, conforme mostrado no exemplo a seguir. 

    O recurso service runner é um recurso proxy para gerenciar e controlar identidades gerenciadas no DevTest Labs. O nome do corredor de serviço pode ser qualquer nome válido, mas recomendamos que você use o nome do recurso de identidade gerenciado.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre identidades gerenciadas, consulte [O que é identidades gerenciadas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).







