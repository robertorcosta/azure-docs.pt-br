---
title: Provisionamento de usuário automatizado em aplicativo SaaS no Azure AD | Microsoft Docs
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454526"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatize o provisionamento e o desprovisionamento do usuário para aplicativos com o Azure Active Directory

No Azure Active Directory (Azure AD), o termo **provisionamento de aplicativos** refere-se à criação automática de identidades e funções do usuário nos aplicativos[cloud (SaaS)](https://azure.microsoft.com/overview/what-is-saas/)aos os que os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o provisionamento de um usuário do Azure AD em aplicativos como [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md), entre outros.

![Diagrama de visão geral do provisionamento](./media/user-provisioning/provisioning-overview.png)

Este recurso permite que você:

- **Automatize o provisionamento**: Crie automaticamente novas contas nos sistemas certos para novas pessoas quando elas se juntarem à sua equipe ou organização.
- **Automatizar o deprovisionamento:** Desativar automaticamente as contas nos sistemas certos quando as pessoas saem da equipe ou organização.
- **Sincronizar dados entre sistemas:** Certifique-se de que as identidades em seus aplicativos e sistemas sejam mantidas atualizadas com base em mudanças no diretório ou no sistema de recursos humanos.
- **Grupos de provisão:** Provisão de grupos para aplicativos que os apoiam.
- **Governar o acesso:** Monitore e audite quem foi provisionado em seus aplicativos.
- **Implantar perfeitamente em cenários de campo marrom:** Combine as identidades existentes entre sistemas e permita uma fácil integração, mesmo quando os usuários já existem no sistema de destino.
- **Use personalização rica:** Aproveite os mapeamentos de atributos personalizáveis que definem quais dados do usuário devem fluir do sistema de origem para o sistema de destino.
- **Receba alertas para eventos críticos:** O serviço de provisionamento fornece alertas para eventos críticos e permite a integração do Log Analytics, onde você pode definir alertas personalizados para atender às suas necessidades de negócios.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

À medida que o número de aplicativos usados em organizações modernas continua a crescer, os administradores de TI são encarregados do gerenciamento de acesso em escala. Padrões como OAmL (Security Assertions Markup Language) ou Open ID Connect (OIDC) permitem que os admins configurem rapidamente o logon único (SSO), mas o acesso também exige que os usuários sejam provisionados no aplicativo. Para muitos admins, provisionamento significa criar manualmente cada conta de usuário ou carregar arquivos CSV a cada semana, mas esses processos são demorados, caros e propensos a erros. Soluções como o SAML just-in-time (JIT) foram adotadas para automatizar o provisionamento, mas as empresas também precisam de uma solução para desprovisionar os usuários quando eles deixam a organização ou não precisam mais de acesso a determinados aplicativos com base na mudança de função.

Algumas motivações comuns para o uso do provisionamento automático incluem:

- Maximizando a eficiência e a precisão dos processos de provisionamento.
- Economizando em custos associados à hospedagem e manutenção de soluções e scripts de provisionamento personalizados.
- Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saem da organização.
- Importando facilmente um grande número de usuários para um determinado aplicativo ou sistema SaaS.
- Ter um único conjunto de políticas para determinar quem está provisionado e quem pode fazer login em um aplicativo.

O provisionamento de usuários do Azure AD pode ajudar a enfrentar esses desafios. Para saber mais sobre como os clientes têm usado o provisionamento de usuários Do Azure AD, você pode ler o [estudo de caso ASOS](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do provisionamento do usuário no Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento de usuário automático do Azure AD?

O Azure AD possui suporte pré-integrado para muitos aplicativos saas populares e sistemas de recursos humanos, e suporte genérico para aplicativos que implementam partes específicas do [padrão SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Aplicativos pré-integrados (aplicativos SaaS da galeria)**. Você pode encontrar todos os aplicativos para os quais o Azure AD suporta um conector de provisionamento pré-integrado na [lista de tutoriais de aplicativos para provisionamento de usuários](../saas-apps/tutorial-list.md). Os aplicativos pré-integrados listados na galeria geralmente usam APIs de gerenciamento de usuário baseadas em SCIM 2.0 para provisionamento. 

   ![Logotipo da Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se você quiser solicitar um novo aplicativo para provisionamento, você pode [solicitar que seu aplicativo seja integrado à nossa galeria de aplicativos.](../develop/howto-app-gallery-listing.md) Para uma solicitação de provisionamento do usuário, exigimos que o aplicativo tenha um ponto final compatível com SCIM. Por favor, solicite que o fornecedor de aplicativos siga o padrão SCIM para que possamos embarcar o aplicativo em nossa plataforma rapidamente.

* **Aplicativos que suportam SCIM 2.0**. Para obter informações sobre como conectar genericamente aplicativos que implementam APIs de gerenciamento de usuário baseadas em SCIM 2.0, consulte [Build a SCIM endpoint e configure o provisionamento do usuário](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é o Sistema de Gerenciamento de Identidade Cross-domain (SCIM)?

Para ajudar a automatizar o provisionamento e o desprovisionamento, os aplicativos expõem as APIs proprietárias do usuário e do grupo. No entanto, qualquer um que tenha tentado gerenciar usuários em mais de um aplicativo dirá que cada aplicativo tenta realizar as mesmas ações simples, como criar ou atualizar usuários, adicionar usuários a grupos ou desprovisionar usuários. No entanto, todas essas ações simples são implementadas um pouco diferente, usando diferentes caminhos de ponto final, métodos diferentes para especificar informações do usuário e um esquema diferente para representar cada elemento de informação.

Para enfrentar esses desafios, a especificação SCIM fornece um esquema de usuário comum para ajudar os usuários a se moverem para dentro, para fora e em torno de aplicativos. O SCIM está se tornando o padrão de fato para provisionamento e, quando usado em conjunto com padrões da federação como SAML ou OpenID Connect, fornece aos administradores uma solução baseada em padrões de ponta a ponta para gerenciamento de acesso.

Para obter orientações detalhadas sobre o desenvolvimento de um ponto final SCIM para automatizar o provisionamento e o desprovisionamento de usuários e grupos para um aplicativo, consulte [Build a SCIM endpoint e configure o provisionamento do usuário](use-scim-to-provision-users-and-groups.md). Para aplicativos pré-integrados na galeria (Slack, Azure Databricks, Snowflake, etc.), você pode pular a documentação do desenvolvedor e usar os tutoriais fornecidos [aqui](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Provisionamento manual versus automático

Os aplicativos na galeria Azure AD suportam um dos dois modos de provisionamento:

* **O** provisionamento manual significa que ainda não há conector automático de provisionamento Azure AD para o aplicativo. As contas de usuário devem ser criadas manualmente, por exemplo, adicionando usuários diretamente ao portal administrativo do aplicativo ou carregando uma planilha com detalhes da conta do usuário. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

* **Automático** significa que o conector de provisionamento do Azure AD foi desenvolvido para este aplicativo. Você deve seguir o tutorial de configuração específico para configurar o provisionamento para o aplicativo. Tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md).

Na galeria Azure AD, os aplicativos que suportam provisionamento automático são designados por um ícone **de provisionamento.** Mude para a nova experiência de visualização de galeria para ver esses ícones (no banner na parte superior da **página Adicionar um aplicativo**, selecione o link que diz Clique aqui para experimentar a nova e melhorada galeria de **aplicativos**).

![Ícone de provisionamento na galeria de aplicativos](./media/user-provisioning/browse-gallery.png)

O modo de provisionamento suportado por um aplicativo também é visível na guia **Provisionamento** depois de adicionar o aplicativo aos seus **aplicativos Enterprise**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o provisionamento automático para um aplicativo?

Para aplicativos pré-integrados listados na galeria, a orientação passo a passo está disponível para configurar o provisionamento automático. Veja a [lista de tutoriais para aplicativos de galeria integrados](../saas-apps/tutorial-list.md). O vídeo a seguir demonstra como configurar o provisionamento automático do usuário para salesforce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outros aplicativos que suportam o SCIM 2.0, siga as etapas do artigo [Construa um ponto final SCIM e configure](use-scim-to-provision-users-and-groups.md)o provisionamento do usuário .


## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
- [Personalização de mapeamentos de atributos para provisionamento de usuários](customize-application-attributes.md)
- [Escrever expressões para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtros de escopo para provisionamento de usuários](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Construa um ponto final SCIM e configure o provisionamento do usuário](use-scim-to-provision-users-and-groups.md)
- [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
