---
title: Alterar a senha da conta do conector do Azure AD | Microsoft Docs
description: Este tópico documenta como restaurar a conta do conector do AD do Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204524"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Alterar a senha da conta do Conector Azure AD
A conta do conector do Azure AD deve ser gratuita pelo serviço. Se você precisa redefinir suas credenciais, este tópico é indicado para você. Por exemplo, se um administrador global tiver por engano, redefina a senha na conta usando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta do conector do Azure AD não puder contatar o Azure AD devido a problemas de autenticação, a senha poderá ser redefinida.

1. Entre no servidor de sincronização do Azure AD Connector e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.  
   ![Cmdlet addadsyncaadserviceaccount do PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de Administrador Global do Azure AD.

Esse cmdlet redefinirá a senha da conta de serviço e a atualizará no Azure AD e no mecanismo de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Problemas conhecidos que essas etapas podem resolver
Esta seção é uma lista de erros relatados por clientes que foram corrigidos por uma redefinição de credenciais na conta do conector do Azure AD.

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

