---
title: O que é o gerenciamento de aplicativos no Azure Active Directory
description: Uma visão geral do uso do Azure AD (Active Directory) como um sistema de IAM (Gerenciamento de Identidades e Acesso) para seus aplicativos locais e de nuvem.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: dafd3d7805924d92354c3de6ded50f17598f80f6
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078063"
---
# <a name="what-is-application-management"></a>O que é gerenciamento de aplicativos?

O Azure AD é um sistema de IAM (Gerenciamento de Identidades e Acesso). Ele fornece um só local para armazenar informações sobre identidades digitais. Você pode configurar seus aplicativos de software para usar o Azure AD como o local em que as informações do usuário são armazenadas. 

O Azure AD precisa ser configurado para se integrar a um aplicativo. Em outras palavras, ele precisa saber quais aplicativos o estão usando como um sistema de identidade. O processo de manter o Azure AD ciente desses aplicativos e como ele deve tratá-los é conhecido como gerenciamento de aplicativos.

Gerencie os aplicativos na folha **Aplicativos empresariais** localizada na seção Gerenciar do portal do Azure Active Directory.

![A opção Aplicativos empresariais na seção Gerenciar do portal do Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>O que é um sistema de IAM (Gerenciamento de Identidades e Acesso)?
Um aplicativo é um software usado para alguma finalidade. A maioria dos aplicativos exige que os usuários se conectem, de modo que o aplicativo possa fornecer uma experiência adaptada a esse usuário específico. Em outras palavras, o aplicativo precisa saber a identidade do usuário que está usando o aplicativo. Porque ele sabe qual funcionalidade deve ser oferecida ou removida para o usuário.

Se cada aplicativo controlar os usuários separadamente, o resultado será um silo de diferentes nomes de usuário e logons para cada aplicativo. Um aplicativo não saberá nada sobre os usuários em outros aplicativos.

Um sistema de identidade centralizado resolve esse problema, fornecendo um só local para armazenar informações do usuário que possam ser usadas posteriormente por todos os aplicativos. Esses sistemas ficaram conhecidos como sistemas de IAM (Gerenciamento de Identidades e Acesso). O Azure AD é o sistema de IAM para a nuvem da Microsoft.

>[!TIP]
>Um sistema de IAM fornece um só local para controlar as identidades dos usuários. O Azure AD é o sistema de IAM para a nuvem da Microsoft.


## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que gerenciar aplicativos com uma solução de nuvem?

Geralmente, as organizações têm centenas de aplicativos que os usuários precisam para trabalhar. Os usuários acessam esses aplicativos em vários dispositivos e locais. Novos aplicativos são adicionados, desenvolvidos e publicados todos os dias. Com tantos aplicativos e pontos de acesso, é mais importante do que nunca usar uma solução baseada em nuvem para gerenciar o acesso dos usuários a todos os aplicativos.

>[!TIP]
>A galeria de aplicativos do Azure AD contém muitos aplicativos populares que já estão pré-configurados para funcionar com o Azure AD como um provedor de identidade.

## <a name="how-does-azure-ad-work-with-applications"></a>Como o Azure AD funciona com aplicativos?

O Azure AD simplifica a maneira de gerenciar seus aplicativos, fornecendo um só sistema de identidade para seus aplicativos locais e de nuvem. Você pode adicionar seus aplicativos SaaS (software como serviço), aplicativos locais e aplicativos LOB (linha de negócios) ao Azure AD. Os usuários entram uma vez para acessar com segurança esses aplicativos, juntamente com o Office 365 e outros aplicativos de negócios da Microsoft. Você pode reduzir os custos administrativos [automatizando o provisionamento de usuários](../app-provisioning/user-provisioning.md). Também é possível usar a autenticação multifator e as políticas de Acesso Condicional para oferecer acesso seguro aos aplicativos.

![Diagrama que mostra aplicativos federados por meio do Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Quais tipos de aplicativos posso integrar ao Azure AD?

Há quatro tipos principais de aplicativos que você pode adicionar aos seus **Aplicativos empresariais** e gerenciar com o Azure AD:

- **Os aplicativos da Galeria do Azure AD** – o Azure AD tem uma galeria que contém milhares de aplicativos que foram pré-integrados para logon único com o Azure AD. Alguns dos aplicativos que sua organização usa provavelmente estão na galeria. [Saiba mais sobre como planejar a integração do aplicativo](plan-an-application-integration.md) ou obter etapas detalhadas de integração para aplicativos individuais nos [tutoriais de aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Aplicativos locais com Proxy de Aplicativo** – com o Proxy de Aplicativo do Azure AD, você pode integrar seus aplicativos Web locais com o Azure AD para dar suporte ao logon único. Em seguida, os usuários finais podem acessar seus aplicativos Web locais da mesma forma que acessam o Office 365 e outros aplicativos SaaS; confira [Fornecer acesso remoto a aplicativos locais por meio do Proxy de Aplicativo do Azure AD](application-proxy.md).

- **Aplicativos desenvolvidos de forma personalizada** – ao criar seus próprios aplicativos de linha de negócios, você pode integrá-los ao Azure AD para dar suporte ao logon único. Ao registrar seu aplicativo com o Azure AD, você tem controle sobre a política de autenticação para o aplicativo. Para saber mais, confira [Orientação para desenvolvedores](developer-guidance-for-integrating-applications.md).

- **Aplicativos inexistentes na Galeria** – traga seus aplicativos! Suporte a logon único para outros aplicativos adicionando-os ao Azure AD. Há várias maneiras de integrar um aplicativo, e algumas delas estão listadas abaixo. Para saber mais, confira [Configurar o logon único do SAML](configure-saml-single-sign-on.md).

>[!TIP]
>Você pode integrar o Azure AD a um aplicativo, mesmo que ele ainda não esteja pré-configurado e na galeria de aplicativos. Você pode **integrar o Azure AD a qualquer um** dos aplicativos a seguir
> - Qualquer link da Web, ou aplicativo, que renderize um **campo de nome de usuário e senha**.
> - Qualquer aplicativo que dê suporte a **protocolos SAML ou OpenID Connect**.
> - Qualquer aplicativo que dê suporte ao padrão **SCIM (Sistema de Gerenciamento de Usuários entre Domínios)** .

## <a name="manage-risk-with-conditional-access-policies"></a>Gerenciar os riscos com as políticas de Acesso Condicional

Combinar o SSO (logon único) do Azure AD com [Acesso Condicional](../conditional-access/concept-conditional-access-cloud-apps.md) aumenta a segurança no acesso aos aplicativos. As funcionalidades de segurança incluem a proteção de identidade em escala de nuvem, o controle de acesso baseado em risco, a autenticação multifator nativa e as políticas de Acesso Condicional. Essas funcionalidades permitem ter um controle granular das políticas baseadas em aplicativos ou em grupos que precisam de mais segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o logon único

Habilitar o SSO (logon único) em todos os aplicativos e no Office 365 aprimora a experiência de logon para os usuários atuais ao reduzir ou eliminar as solicitações de entrada. O ambiente do usuário parece mais coeso e há menos distração sem as várias solicitações ou a necessidade de gerenciar diversas senhas. O grupo de negócios pode gerenciar e aprovar o acesso por meio de uma associação dinâmica de autoatendimento. Permitir que as pessoas certas na empresa gerenciem o acesso a um aplicativo aumenta a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o logon único*, os administradores precisam criar e atualizar as contas de usuário para cada aplicativo individual, o que leva tempo. Além disso, os usuários precisam controlar várias credenciais para acessar seus aplicativos. Dessa maneira, os usuários tendem a anotar suas senhas ou usar outras soluções de gerenciamento de senhas que apresentam riscos de segurança de dados. [Leia mais sobre o logon único](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governança e a conformidade

Com o Azure AD, é possível monitorar os acessos ao aplicativo por meio de relatórios que usam as ferramentas de SIEM (gerenciamento de eventos e informações de segurança). É possível acessar os relatórios no portal ou nas APIs. Programaticamente, é possível auditar quem tem acesso aos aplicativos e remover o acesso de usuários inativos nas revisões de acesso.

## <a name="manage-costs"></a>Gerenciar os custos

Ao migrar para o Azure AD, você economiza custos e elimina a dificuldade de gerenciar uma infraestrutura local. O Azure AD também oferece acesso de autoatendimento para aplicativos, o que economiza tempo para administradores e usuários. O logon único elimina as senhas específicas de aplicativo. Essa capacidade de iniciar sessão uma vez economiza custos relacionados com a redefinição de senha para aplicativos e acaba com a perda de produtividade ao recuperar senhas.

Para aplicativos voltados para Recursos Humanos ou outros aplicativos com um grande conjunto de usuários, aproveite o provisionamento de aplicativos para automatizar o processo de provisionamento e desprovisionamento de usuários; confira [O que é o provisionamento de aplicativos?](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>Próximas etapas

- [Série de guias de início rápido sobre gerenciamento de aplicativo no Azure AD](view-applications-portal.md)
- [Introdução à integração de aplicativos](plan-an-application-integration.md)
- [Saiba como automatizar o provisionamento](../app-provisioning/user-provisioning.md)
