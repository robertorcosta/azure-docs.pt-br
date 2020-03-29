---
title: Alterar a senha da conta do Azure AD Connector | Microsoft Docs
description: Este tópico documenta como restaurar a conta do Azure AD Connector.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204524"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Alterar a senha da conta do Conector Azure AD
A conta Azure AD Connector deve ser gratuita. Se você precisa redefinir suas credenciais, este tópico é indicado para você. Por exemplo, se um administrador global tiver, por engano, redefinirá a senha na conta usando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta do Azure AD Connector não puder entrar em contato com o Azure AD devido a problemas de autenticação, a senha poderá ser redefinida.

1. Entre no servidor de sincronização do Azure AD Connector e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.  
   ![Cmdlet addadsyncaadserviceaccount do PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de Administrador Global do Azure AD.

Esse cmdlet redefinirá a senha da conta de serviço e a atualizará no Azure AD e no mecanismo de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Problemas conhecidos que essas etapas podem resolver
Esta seção é uma lista de erros relatados pelos clientes que foram corrigidos por uma credenciais redefinidas na conta Azure AD Connector.

---
Evento 6900  
O servidor encontrou um erro inesperado ao processar uma notificação de alteração de senha:  
AADSTS70002: erro ao validar as credenciais. AADSTS50054: A senha antiga é usada para autenticação.

---
Evento 659  
Erro ao recuperar a configuração de sincronização de política de senha. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: erro ao validar as credenciais. AADSTS50054: A senha antiga é usada para autenticação.

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrando suas identidades locais ao Azure Active Directory](whatis-hybrid-identity.md)

