---
title: 'Gerenciamento de aplicações: Melhores práticas e recomendações | Microsoft Docs'
description: Aprenda as melhores práticas e recomendações para gerenciar aplicativos no Azure Active Directory. Saiba como usar aplicativos de provisionamento automático e publicação no local com o Proxy do aplicativo.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085285"
---
# <a name="application-management-best-practices"></a>Práticas recomendadas de gerenciamento de aplicativos
Este artigo contém recomendações e práticas recomendadas para gerenciar aplicativos no Azure Active Directory (Azure AD), usando provisionamento automático e publicação de aplicativos no local com o Proxy do aplicativo.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Aplicativo em nuvem e recomendações de login único
| Recomendação | Comentários |
| --- | --- |
| Confira a galeria de aplicativos do Azure AD para aplicativos  | O Azure AD possui uma galeria que contém milhares de aplicativos pré-integrados que estão habilitados com o SSO (Single Sign-on) da Enterprise. Para obter orientação de configuração específica do aplicativo, consulte a [lista de tutoriais do aplicativo SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Use o SSO federado baseado em SAML  | Quando um aplicativo o suporta, use O SSO federado, baseado em SAML, com OAD do Azure em vez de SSO e ADFS baseados em senha.  | 
| Use SHA-256 para assinar certificados  | O Azure AD usa o algoritmo SHA-256 por padrão para assinar a resposta SAML. Use SHA-256 a menos que o aplicativo exija SHA-1 (consulte opções de assinatura de [certificados](certificate-signing-options.md) e [problema de login do aplicativo](application-sign-in-problem-application-error.md).)  | 
| Exigir atribuição do usuário  | Por padrão, os usuários podem acessar seus aplicativos corporativos sem serem atribuídos a eles. No entanto, se o aplicativo expõe funções ou se você deseja que o aplicativo apareça no painel de acesso de um usuário, exigirá a atribuição do usuário. (Consulte [orientação do desenvolvedor para integrar aplicativos](developer-guidance-for-integrating-applications.md).)  | 
| Implantar o painel de acesso Meus Aplicativos aos seus usuários | O painel `https://myapps.microsoft.com` de [acesso](end-user-experiences.md) é um portal baseado na Web que fornece aos usuários um único ponto de entrada para seus aplicativos baseados em nuvem atribuídos. À medida que recursos adicionais, como gerenciamento de grupo e redefinição de senha de autoatendimento são adicionados, os usuários podem encontrá-los no painel de acesso. Consulte [Planejar a implantação de um painel de acesso](access-panel-deployment-plan.md).
| Use a atribuição de grupo  | Se estiver incluído em sua assinatura, atribua grupos a um aplicativo para que você possa delegar o gerenciamento de acesso contínuo ao proprietário do grupo. (Consulte [orientação do desenvolvedor para integrar aplicativos](developer-guidance-for-integrating-applications.md).)   | 
| Estabelecer um processo de gestão de certificados | A vida útil máxima de um certificado de assinatura é de três anos. Para evitar ou minimizar a paralisação devido ao vencimento de um certificado, use funções e listas de distribuição de e-mail para garantir que as notificações de alteração relacionadas ao certificado sejam monitoradas de perto. |

## <a name="provisioning-recommendations"></a>Recomendações de provisionamento
| Recomendação | Comentários |
| --- | --- |
| Use tutoriais para configurar o provisionamento com aplicativos em nuvem | Verifique a [Lista de tutoriais do aplicativo SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para obter orientações passo a passo sobre a configuração do provisionamento para o aplicativo de galeria que você deseja adicionar. |
| Use registros de provisionamento (visualização) para monitorar o status | Os [registros de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) dão detalhes sobre todas as ações realizadas pelo serviço de provisionamento, incluindo status para usuários individuais. |
| Atribuir um grupo de distribuição ao e-mail de notificação de provisionamento | Para aumentar a visibilidade dos alertas críticos enviados pelo serviço de provisionamento, atribua um grupo de distribuição à configuração de E-mails de notificação. |


## <a name="application-proxy-recommendations"></a>Recomendações de proxy de aplicativos
| Recomendação | Comentários |
| --- | --- |
| Use proxy de aplicativo para acesso remoto a recursos internos | O Proxy de aplicativo é recomendado para dar aos usuários remotos acesso a recursos internos, substituindo a necessidade de uma VPN ou proxy reverso. Não se destina a acessar recursos de dentro da rede corporativa porque poderia adicionar latência.
| Use domínios personalizados | Configure domínios personalizados para seus aplicativos (consulte [Configurar domínios personalizados)](application-proxy-configure-custom-domain.md)para que os URLs para usuários e entre aplicativos funcionem dentro ou fora da sua rede. Você também poderá controlar sua marca e personalizar seus URLs.  Ao usar nomes de domínio personalizados, planeje adquirir um certificado público de uma autoridade de certificado confiável não confiável da Microsoft. O Azure Application Proxy suporta certificados padrão ([curinga)](application-proxy-wildcard.md)ou baseados em SAN. (Consulte [o planejamento do proxy do aplicativo](application-proxy-deployment-plan.md).) |
| Sincronizar usuários antes de implantar o Proxy do aplicativo | Antes de implantar o proxy do aplicativo, sincronize as identidades dos usuários de um diretório local ou crie-as diretamente no Azure AD. A sincronização de identidade permite que o Azure AD pré-autenticar os usuários antes de conceder-lhes acesso aos aplicativos publicados pelo App Proxy. Ele também fornece as informações necessárias do identificador de usuário para realizar o sso (single sign-on). (Consulte [o planejamento do proxy do aplicativo](application-proxy-deployment-plan.md).) |
| Siga nossas dicas para alta disponibilidade e balanceamento de carga | Para saber como o tráfego flui entre os usuários, os conectores proxy do aplicativo e os servidores de aplicativos back-end, e para obter dicas para otimizar o desempenho e o balanceamento de carga, consulte [O balanceamento de alta disponibilidade e carga dos conectores e aplicativos do Proxy do aplicativo.](application-proxy-high-availability-load-balancing.md) |
| Use vários conectores | Use dois ou mais conectores proxy de aplicativo para maior resiliência, disponibilidade e escala (consulte [conectores proxy de aplicativo](application-proxy-connectors.md)). Crie grupos de conectores e garanta que cada grupo de conectores tenha pelo menos dois conectores (três conectores são ótimos). |
| Localize servidores conectores próximos a servidores de aplicativos e certifique-se de que eles estão no mesmo domínio | Para otimizar o desempenho, localize fisicamente o servidor conector próximo aos servidores de aplicativos (consulte [considerações de topologia da rede](application-proxy-network-topology.md)). Além disso, os servidores de servidor de conector e aplicativos web devem pertencer ao mesmo domínio do Active Directory, ou devem abranger domínios confiáveis. Essa configuração é necessária para o SSO com IWA (Integrated Windows Authentication, autenticação integrada do Windows) e Kerberos Constrained Delegation (KCD). Se os servidores estiverem em diferentes domínios, você precisará usar a delegação baseada em recursos para SSO (consulte [KCD para fazer login único com proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Habilite atualizações automáticas para conectores | Habilite atualizações automáticas para seus conectores para os recursos mais recentes e correções de bugs. A Microsoft oferece suporte direto para a versão mais recente do conector e uma versão anterior. (Consulte o [histórico da versão do proxy do aplicativo](application-proxy-release-version-history.md).) |
| Contorne seu proxy no local | Para facilitar a manutenção, configure o conector para contornar seu proxy no local para que ele se conecte diretamente aos serviços do Azure. (Consulte [conectores proxy de aplicativos e servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Use o proxy do aplicativo Azure AD por proxy de aplicativo web | Use o Proxy do aplicativo Azure AD para a maioria dos cenários locais. O Proxy de aplicativo da Web só é preferido em cenários que requerem um servidor proxy para AD FS e onde você não pode usar domínios personalizados no Azure Active Directory. (Consulte [migração de proxy de aplicativo](application-proxy-migration.md).) |
