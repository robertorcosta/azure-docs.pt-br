---
title: Pesquisa de nome de usuário durante a entrada-Azure Active Directory | Microsoft Docs
description: Como mensagens na tela refletem a pesquisa de nome de usuário durante a entrada no Azure Active Directory
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
ms.openlocfilehash: c1f27c7b91a78da8944c23fd353d3b6791b3e015
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582559"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Home Realm Discovery para páginas de entrada Azure Active Directory

Estamos alterando nosso comportamento de entrada do Azure Active Directory (AD do Azure) para liberar espaço para novos métodos de autenticação e melhorar a usabilidade. Durante a entrada, o AD do Azure determina onde um usuário precisa se autenticar. O Azure AD toma decisões inteligentes lendo as configurações da organização e do usuário para o nome de usuários inserido na página de entrada. Esta é uma etapa para um futuro gratuito de senha que permite credenciais adicionais como FIDO 2,0.

## <a name="home-realm-discovery-behavior"></a>Comportamento da descoberta de realm inicial

Historicamente, a descoberta de realm inicial foi regida pelo domínio que é fornecido na entrada ou por uma política de descoberta de realm inicial para alguns aplicativos herdados. Por exemplo, em nosso comportamento de descoberta, um usuário Azure Active Directory poderia digitar o nome de usuário indigitadamente, mas ainda chegaria à tela de coleção de credenciais de sua organização. Isso ocorre quando o usuário fornece corretamente o nome de domínio da organização "contoso.com". Esse comportamento não permite a granularidade de personalizar experiências para um usuário individual.

Para dar suporte a um intervalo mais amplo de credenciais e aumentar a usabilidade, Azure Active Directory comportamento de pesquisa de nome de usuário durante o processo de entrada agora é atualizado. O novo comportamento toma decisões inteligentes com a leitura de configurações no nível da organização e do usuário com base no nome de usuários inserido na página de entrada. Para tornar isso possível, Azure Active Directory verificará se o nome de usuário inserido na página de entrada existe no domínio especificado ou redireciona o usuário para fornecer suas credenciais.

Um benefício adicional desse trabalho é o sistema de mensagens de erro aprimorado. Aqui estão alguns exemplos das mensagens de erro aprimoradas ao entrar em um aplicativo que dá suporte apenas a Azure Active Directory usuários.

- O nome de usuário está digitado Inou o nome de usuário ainda não foi sincronizado com o Azure AD:
  
    ![o nome de usuário está digitado ou não foi encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio está digitado inparado:
  
    ![o nome de domínio está digitado ou não foi encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O usuário tenta entrar com um domínio de consumidor conhecido:
  
    ![entrar com um domínio de consumidor conhecido](./media/signin-realm-discovery/consumer-domain.png)
  
- A senha está digitada incorretamente, mas o nome de usuário é preciso:  
  
    ![a senha está digitada incorretamente com um bom nome de usuário](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esse recurso pode ter um impacto em domínios federados que dependem da descoberta de realm inicial no nível de domínio antigo para forçar a Federação. Para obter atualizações sobre quando o suporte a domínio federado será adicionado, consulte [Home Realm Discovery durante a entrada para serviços de Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Enquanto isso, algumas organizações treinavam seus funcionários para entrar com um nome de usuário que não existe no Azure Active Directory, mas contém o nome de domínio apropriado, pois os nomes de domínio roteiam os usuários no momento para o ponto de extremidade de domínio da organização. O novo comportamento de entrada não permite isso. O usuário é notificado para corrigir o nome de usuário e não tem permissão para entrar com um nome de usuário que não existe no Azure Active Directory.
>
> Se você ou sua organização tiver práticas que dependem do comportamento antigo, é importante que os administradores da organização atualizem a documentação de entrada e autenticação de funcionários e treinem os funcionários a usarem seus Azure Active Directory nome de usuário para entrar.
  
Se você tiver preocupações com o novo comportamento, deixe seus comentários na seção de **comentários** deste artigo.  

## <a name="next-steps"></a>Próximas etapas

[Personalizar sua identidade visual de entrada](../fundamentals/add-custom-domain.md)
