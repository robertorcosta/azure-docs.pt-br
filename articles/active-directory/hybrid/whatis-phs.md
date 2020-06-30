---
title: O que é sincronização de hash da senha com o Azure AD? | Microsoft Docs
description: Descreve a sincronização de hash de senha.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 568f284a22f170c2a6a0d60d9e2cfed0645937ba
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261368"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>O que é sincronização de hash da senha com o Azure AD?
A sincronização de hash de senha é um dos métodos de entrada usados para atingir a identidade híbrida. O Azure AD Connect sincroniza um hash, do hash, da senha de um usuário de uma instância do Active Directory local para uma instância do Azure AD baseada em nuvem.

A sincronização de hash de senha é uma extensão para o recurso de sincronização de diretório, implementado pelo Azure AD Connect Sync. Você pode usar esse recurso para entrar em serviços do Azure AD como o Office 365. Você entra no serviço usando a mesma senha que usa para entrar sua instância do Active Directory local.

![O que é o Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

A sincronização de hash de senha ajuda reduzindo o número de senhas que os usuários precisam manter para apenas uma. A sincronização de hash de senha pode:

* Aumentar a produtividade dos seus usuários.
* Reduzir os custos de assistência técnica.  

Opcionalmente, você poderá configurar a sincronização de hash de senha como um backup se optar por usar os [Serviços de Federação do Active Directory (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) como seu método de entrada.

Para usar a sincronização de hash de senha no seu ambiente, você precisa:

* Instalar o Azure AD Connect.  
* Configurar a sincronização de diretórios entre sua instância do Active Directory local e a instância do Azure Active Directory.
* Permitir a sincronização de hash de senha.



Para obter mais informações, confira [O que é a identidade híbrida?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Próximas etapas

- [O que é identidade híbrida?](whatis-hybrid-identity.md)
- [O que é o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é PTA (autenticação de passagem)?](how-to-connect-pta.md)
- [O que é federação?](whatis-fed.md)
- [O que é logon único?](how-to-connect-sso.md)
- [Como a sincronização de hash de senha funciona](how-to-connect-password-hash-synchronization.md)
