---
title: Busca de nome de usuário durante o login - Azure Active Directory | Microsoft Docs
description: Como as mensagens na tela refletem a aparência do nome de usuário durante o login no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024243"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Descoberta de home reino para páginas de login do Azure Active Directory

Estamos mudando nosso comportamento de login do Azure Active Directory (Azure AD) para abrir espaço para novos métodos de autenticação e melhorar a usabilidade. Durante o login, o Azure AD determina onde um usuário precisa autenticar. O Azure AD toma decisões inteligentes lendo a organização e as configurações do usuário para o nome de usuário inserido na página de login. Este é um passo em direção a um futuro sem senha que permite credenciais adicionais como fido 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento de descoberta de reinos domésticos

Historicamente, a descoberta do reino doméstico era regida pelo domínio que é fornecido no login ou por uma política de Home Realm Discovery para alguns aplicativos legados. Por exemplo, em nosso comportamento de descoberta, um usuário do Azure Active Directory poderia digitar mal seu nome de usuário, mas ainda assim chegaria à tela de coleta de credenciais de sua organização. Isso ocorre quando o usuário fornece corretamente o nome de domínio da organização "contoso.com". Esse comportamento não permite que a granularidade personalize experiências para um usuário individual.

Para suportar uma gama mais ampla de credenciais e aumentar a usabilidade, o comportamento de busca de nome de usuário do Azure Active Directory durante o processo de login agora é atualizado. O novo comportamento toma decisões inteligentes lendo configurações de nível de inquilino e usuário com base no nome de usuário inserido na página de login. Para tornar isso possível, o Azure Active Directory verificará se o nome de usuário inserido na página de login existe em seu domínio especificado ou redireciona o usuário para fornecer suas credenciais.

Um benefício adicional deste trabalho é a melhoria das mensagens de erro. Aqui estão alguns exemplos da mensagem de erro melhorada ao fazer login em um aplicativo que suporta apenas usuários do Azure Active Directory.

- O nome de usuário foi digitado mal ou o nome de usuário ainda não foi sincronizado com o Azure AD:
  
    ![o nome de usuário é digitado errado ou não encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio é digitado errado:
  
    ![o nome de domínio é mal digitado ou não encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O usuário tenta fazer login com um domínio conhecido do consumidor:
  
    ![login com um domínio consumidor conhecido](./media/signin-realm-discovery/consumer-domain.png)
  
- A senha é digitada incorretamente, mas o nome de usuário é preciso:  
  
    ![senha é digitada inemis com bom nome de usuário](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esse recurso pode ter um impacto nos domínios federados que dependem do antigo Home Realm Discovery de nível de domínio para forçar a federação. Para obter atualizações sobre quando o suporte a domínio federado será adicionado, consulte [Home realm discovery durante o login para serviços Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Enquanto isso, algumas organizações treinaram seus funcionários para fazer login com um nome de usuário que não existe no Azure Active Directory, mas contém o nome de domínio adequado, porque os nomes de domínio encaminham os usuários atualmente para o ponto final de domínio de sua organização. O novo comportamento de login não permite isso. O usuário é notificado para corrigir o nome de usuário e não é permitido fazer login com um nome de usuário que não existe no Azure Active Directory.
>
> Se você ou sua organização tiver práticas que dependem do comportamento antigo, é importante que os administradores da organização atualizem a documentação de login e autenticação dos funcionários e treinem os funcionários para usar o nome de usuário do Azure Active Directory para fazer login.
  
Se você tem preocupações com o novo comportamento, deixe suas observações na seção **Feedback** deste artigo.  

## <a name="next-steps"></a>Próximas etapas

[Personalize sua marca de login](../fundamentals/add-custom-domain.md)
