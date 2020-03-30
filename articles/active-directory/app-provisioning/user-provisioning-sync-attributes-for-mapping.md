---
title: Sincronizar atributos ao Azure AD para mapeamento | Microsoft Docs
description: Aprenda a sincronizar atributos do seu Active Directory no local para o Azure AD. Ao configurar o provisionamento do usuário em aplicativos SaaS, use o recurso de extensão de diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522264"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronize um atributo do seu Active Directory no local para o Azure AD para provisionamento a um aplicativo

Ao personalizar mapeamentos de atributos para provisionamento de usuários, você pode descobrir que o atributo que deseja mapear não aparece na lista **de atributos Origem.** Este artigo mostra como adicionar o atributo ausente sincronizando-o do seu Active Directory (AD) no local ao Azure Active Directory (Azure AD).

O Azure AD deve conter todos os dados necessários para criar um perfil de usuário ao provisionar contas de usuários do Azure AD para um aplicativo SaaS. Em alguns casos, para disponibilizar os dados, você pode precisar sincronizar atributos do seu AD no local para o Azure AD. O Azure AD Connect sincroniza automaticamente certos atributos ao Azure AD, mas não todos os atributos. Além disso, alguns atributos (como o SAMAccountName) sincronizados por padrão podem não ser expostos usando a API do Microsoft Graph. Nesses casos, você pode usar o recurso de extensão de diretório Azure AD Connect para sincronizar o atributo ao Azure AD. Dessa forma, o atributo será visível para a API do Microsoft Graph e para o serviço de provisionamento AD do Azure.

Se os dados necessários para o provisionamento estão no Active Directory, mas não estão disponíveis para provisionamento devido às razões descritas acima, siga essas etapas.
 
## <a name="sync-an-attribute"></a>Sincronize um atributo 

1. Abra o assistente Azure AD Connect, escolha Tarefas e escolha **Personalizar opções de sincronização**.

   ![Assistente de conexão de diretório ativo do Azure Página de tarefas adicionais](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Faça login como administrador global do Azure AD. 

3. Na página **Recursos opcionais,** selecione **Sincronização de atributo de extensão do diretório**.
 
   ![Assistente de conexão do diretório ativo do Azure Página de recursos opcionais](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione os atributos que deseja estender ao Azure AD.
   > [!NOTE]
   > A pesquisa em **Atributos Disponíveis** é sensível ao caso.

   ![Página de seleção de extensões do diretório do azure active connect connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Termine o assistente Azure AD Connect e permita que um ciclo de sincronização completo seja executado. Quando o ciclo é concluído, o esquema é estendido e os novos valores são sincronizados entre o AD e o Azure AD no local.
 
6. No portal Azure, enquanto você está [editando mapeamentos de atributos do usuário,](customize-application-attributes.md)a lista **de atributos De Origem** agora conterá o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)`. Selecione o atributo e mapeie-o para o aplicativo de destino para provisionamento.

   ![Página de seleção de extensões do diretório do azure active connect connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A capacidade de provisionar atributos de referência de AD no local, como **gerenciado por** ou **DN/DistinguishedName,** não é suportada hoje. Você pode solicitar esse recurso no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Próximas etapas

* [Defina quem está no escopo para provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
