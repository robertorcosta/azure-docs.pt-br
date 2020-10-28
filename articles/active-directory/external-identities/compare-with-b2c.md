---
title: Comparar Identidades Externas – Azure Active Directory | Microsoft Docs
description: As Identidades Externas do Azure AD permitem que as pessoas fora da sua organização acessem seus aplicativos e recursos usando sua própria identidade. Compare soluções para Identidades Externas, incluindo colaboração B2B do Azure Active Directory e Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf57e60fc05d579365d459e4a5d5288c2ca52bb0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442143"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>O que são identidades externas no Azure Active Directory?

Com Identidades Externas no Azure AD, você pode permitir que pessoas fora da sua organização acessem seus aplicativos e recursos, deixando elas entrarem usando qualquer identidade que preferirem. Seus parceiros, distribuidores, fornecedores e outros usuários convidados podem “trazer suas próprias identidades”. Quer eles façam parte do Azure AD ou de outro sistema gerenciado por TI, ou tenham uma identidade social não gerenciada como Google ou Facebook, eles podem usar suas próprias credenciais para entrar. O provedor de identidade gerencia a identidade do usuário externo e você gerencia o acesso aos seus aplicativos com o Azure AD para manter seus recursos protegidos. 

## <a name="external-identities-scenarios"></a>Cenários de Identidades Externas

As Identidades Externas do Azure AD concentram-se menos na relação de um usuário com sua organização e muito mais no modo como um indivíduo deseja acessar seus aplicativos e recursos. Nessa estrutura, o Azure AD dá suporte a uma variedade de cenários de colaboração B2B (entre empresas) até o desenvolvimento de aplicativos para clientes e consumidores (entre empresas ou B2C).

- **Compartilhe aplicativos com usuários externos (colaboração B2B)** . Convide usuários externos em seu próprio locatário como usuários “convidados” aos quais você pode atribuir permissões (para autorização), permitindo que eles usem suas credenciais existentes (para autenticação). Os usuários acessam os recursos compartilhados usando um processo simples de convite e resgate com a conta corporativa ou de estudante ou qualquer conta de email. E agora com a disponibilidade de Fluxos do usuário de inscrição para autoatendimento (versão prévia), você também pode fornecer uma experiência de entrada para seus usuários externos por meio do aplicativo que deseja compartilhar. Você pode definir configurações de fluxo do usuário para controlar como o usuário se inscreve para o aplicativo e que permitem que eles usem sua conta corporativa, conta de estudante ou qualquer identidade social (como o Google ou Facebook) que desejam usar.  Para obter mais informações, consulte a [Documentação do Azure AD B2B](index.yml).

- **Desenvolva aplicativos destinados a outros locatários do Azure AD (locatário único ou multilocatário)** . Quando desenvolver aplicativos para o Azure AD, você pode direcionar os usuários de uma única organização (locatário único) ou usuários de qualquer organização que já tenha um locatário do Azure AD (chamados de aplicativos multilocatários). Esses aplicativos multilocatários são registrados uma vez por você mesmo em seu próprio Azure AD, mas podem ser usados por qualquer usuário do Azure AD de qualquer organização sem qualquer ação adicional de sua parte.

- **Desenvolva aplicativos sem marca para consumidores e clientes (Azure AD B2C)** . Se você for um negócio ou desenvolvedor criando aplicativos voltados para o cliente, poderá dimensionar para consumidores, clientes ou cidadãos usando um Azure AD B2C. Os desenvolvedores podem usar o Azure AD como o sistema de identidade completo para seu aplicativo, enquanto permite que os clientes entrem com uma identidade já estabelecida (como Facebook ou Gmail). Com o Azure AD B2C, você tem controle total para personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos. Para obter mais informações, consulte a [Documentação do Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Comparar soluções de Identidades Externas

A tabela a seguir fornece uma comparação detalhada dos vários cenários que você pode habilitar com Identidades Externas do Azure AD.

| Aplicativos multilocatários  | Colaboração de usuário externo (B2B) | Aplicativos para consumidores ou clientes (B2C)  |
| ---- | --- | --- |
| Cenário principal: Software como um serviço (SaaS) corporativo | Cenário principal: Colaboração por meio de aplicativos da Microsoft (Microsoft 365, Teams etc.) ou um software de colaboração próprio.  | Cenário principal: Aplicativos transacionais usando aplicativos desenvolvidos personalizados.   |
| Destinado a: Organizações que desejam fornecer software a muitos clientes corporativos.    | Destinado a: organizações que desejam poder autenticar usuários de uma organização de parceiros, independentemente do provedor de identidade.    | Destinado a: Clientes convidados dos aplicativos Web e móveis, sejam indivíduos, clientes institucionais ou organizacionais, para um diretório do Azure AD separado do diretório da sua própria organização. |
| Identidades com suporte: Funcionários com contas do Azure AD. | Identidades com suporte: funcionários com contas corporativas ou de estudante, parceiros com contas corporativas ou de estudante ou qualquer endereço de email. Em breve com suporte à federação direta.      | Identidades com suporte: os usuários do consumidor com contas de aplicativo locais (qualquer nome de usuário ou endereço de email) ou identidades sociais com suporte com federação direta.       |
| Os usuários externos são gerenciados em seu próprio diretório, isolados do diretório onde o aplicativo foi registrado.    | Usuários externos são gerenciados no mesmo diretório que os funcionários, mas são anotados de maneira especial. Eles podem ser gerenciados da mesma forma que os funcionários, podem ser adicionados aos mesmos grupos e assim por diante.    | Usuários externos são gerenciados no diretório do aplicativo. Eles são gerenciados separadamente do diretório de parceiros e funcionários da organização (se houver).  |
| Logon único: Há suporte para SSO (logon único) em todos os aplicativos conectados ao Azure AD.          | Logon único: Há suporte para SSO (logon único) em todos os aplicativos conectados ao Azure AD. Por exemplo, você pode fornecer acesso ao Microsoft 365 ou a aplicativos locais, além de outros aplicativos SaaS, como o Salesforce ou o Workday.    | Logon único: Há suporte para SSO para aplicativos de clientes dentro dos locatários do B2C do AD do Azure. Não há suporte para o SSO com o Microsoft 365 nem com outros aplicativos SaaS da Microsoft.    |
| Ciclo de vida do cliente: gerenciado pela organização do próprio usuário.      | Ciclo de vida do parceiro: gerenciado pela organização convidada/host.    | Ciclo de vida do cliente: autoatendido ou gerenciado pelo aplicativo.      |
| Política de segurança e conformidade: Gerenciada pela organização convidada/host (por exemplo, com [políticas de acesso condicional](./conditional-access.md)).           | Política de segurança e conformidade: Gerenciada pela organização convidada/host (por exemplo, com [políticas de acesso condicional](./conditional-access.md)). | Política de segurança e conformidade: gerenciada pelo aplicativo.        |
| Identidade visual: a marca da organização convidada/host é usada.   | Identidade visual: a marca da organização convidada/host é usada.    | Identidade visual: gerenciada pelo aplicativo. Geralmente, tende a ser da marca do produto com o esmaecimento da organização na tela de fundo.   |
| Mais informações: [Gerenciar a identidade em aplicativos multilocatários](/azure/architecture/multitenant-identity/), [Guia de instruções](../develop/howto-convert-app-to-be-multi-tenant.md) | Mais informações: [Postagem no blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](what-is-b2b.md)                   | Mais informações: [Página do produto](https://azure.microsoft.com/services/active-directory-b2c/), [documentação](../../active-directory-b2c/index.yml)       |

Proteja e gerencie clientes e parceiros além dos limites organizacionais com Identidades Externas do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Sobre o Azure AD B2C](../../active-directory-b2c/overview.md)