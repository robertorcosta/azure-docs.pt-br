---
title: Use identidades gerenciadas do Azure para criar ambientes no DevTest Labs | Microsoft Docs
description: Aprenda a usar identidades gerenciadas no Azure para implantar ambientes em um laboratório no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931152"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Use identidades gerenciadas do Azure para implantar ambientes em laboratório 
Como dono de laboratório, você pode usar uma identidade gerenciada para implantar ambientes em um laboratório. Esse recurso é útil em cenários onde o ambiente contém ou tem referências aos recursos do Azure, como cofres-chave, galerias de imagens compartilhadas e redes que são externas ao grupo de recursos do ambiente. Ele permite a criação de ambientes de sandbox que não se limitam ao grupo de recursos desse ambiente.

> [!NOTE]
> Atualmente, uma única identidade atribuída pelo usuário é suportada por laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
- [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Usar o portal do Azure
Nesta seção, você, como proprietário de laboratório, usa o portal Azure para adicionar uma identidade gerenciada pelo usuário ao laboratório. 

1. Na página do laboratório, **selecione Configuração e políticas**. 
1. Selecione **Identidade** na seção **Configurações.**
1. Para adicionar uma identidade atribuída ao usuário, **selecione Adicionar** na barra de ferramentas. 
1. Selecione uma **identidade** em uma lista de paradas pré-preenchidas.
1. Selecione **OK**.

    ![Adicionar identidade gerenciada pelo usuário](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Você vê a identidade adicionada gerenciada pelo usuário na lista. 

    ![Identidade gerenciada pelo usuário na lista](./media/use-managed-identities-environments/identity-in-list.png)

Uma vez salvo, o laboratório usará essa identidade enquanto implanta todos os ambientes de laboratório. Você também pode acessar o recurso de identidade no Azure selecionando a identidade da lista. 

O dono do laboratório não precisa fazer nada especial enquanto implanta um ambiente enquanto a identidade adicionada ao laboratório tiver permissões para os recursos externos que o ambiente precisa acessar. 

Para alterar a identidade gerenciada pelo usuário atribuída ao laboratório, remova a identidade anexada ao laboratório primeiro e adicione outra ao laboratório. Para remover uma identidade anexada ao laboratório, selecione **... (elipse)** e clique **em Remover**. 

![Identidade gerenciada pelo usuário na lista](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Use API

1. Depois de criar uma identidade, anote o ID de recurso dessa identidade. Deve parecer a seguinte amostra: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Execute um método PUT Https `ServiceRunner` para adicionar um novo recurso ao laboratório semelhante ao exemplo a seguir. O recurso service runner é um recurso proxy para gerenciar e controlar identidades gerenciadas no DevTest Labs. O nome do corredor de serviço pode ser qualquer nome válido, mas recomendamos que você use o nome do recurso de identidade gerenciado. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Aqui está um exemplo: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Uma vez que a identidade atribuída pelo usuário seja adicionada ao laboratório, o serviço Azure DevTest Labs irá usá-la enquanto implanta ambientes do Azure Resource Manager. Por exemplo, se você precisar do modelo do Gerenciador de recursos para acessar uma imagem externa da galeria de imagens compartilhadas, certifique-se de que a identidade adicionada ao laboratório tenha permissões mínimas necessárias para o recurso de galeria de imagens compartilhadas. 
