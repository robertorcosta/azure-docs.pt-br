---
title: Cinco etapas para integrar todos os seus aplicativos com o Azure AD
description: Este guia explica como integrar todos os seus aplicativos com o Azure AD. Em cada etapa, explicamos o valor e fornecemos links para recursos que explicam os detalhes técnicos.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057120"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Cinco etapas para integrar todos os seus aplicativos com o Azure AD

O Azure Active Directory (Azure AD) é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft. O Azure AD fornece soluções seguras de autenticação e autorização para que clientes, parceiros e funcionários possam acessar os aplicativos de que precisam. Com o Azure AD, o [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [a autenticação multifator](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks), o [logon único](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)e o [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) tornam o gerenciamento de identidade e acesso fácil e seguro.

Se sua empresa tiver uma assinatura Microsoft 365, provavelmente você [já usa](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) o Azure AD. No entanto, o Azure AD pode ser usado para todos os seus aplicativos e [centralizar o gerenciamento de aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) você pode usar os mesmos recursos, ferramentas e políticas de gerenciamento de identidade em todo o seu portfólio de aplicativos. Isso fornecerá uma solução unificada que melhora a segurança, reduz os custos, aumenta a produtividade e permite garantir a conformidade. E você terá acesso remoto a aplicativos locais.

Este guia explica como integrar todos os seus aplicativos com o Azure AD. Em cada etapa, explicamos o valor e fornecemos links para recursos que explicam os detalhes técnicos. Apresentamos essas etapas em uma ordem que recomendamos. No entanto, você pode ir para qualquer parte do processo para começar com qualquer um que adicione o maior valor para você.

Outros recursos deste tópico, incluindo White papers detalhados do processo de negócios, que podem ser encontrados em nossos [recursos para migrar aplicativos para Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) página.

## <a name="1-use-azure-ad-for-new-applications"></a>1. usar o Azure AD para novos aplicativos

Primeiro, concentre-se nos aplicativos adquiridos recentemente. Quando sua empresa começar a usar um novo aplicativo, [adicione-o ao seu locatário do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) imediatamente. Configure uma política da empresa para que a adição de novos aplicativos ao Azure AD seja a prática padrão em sua organização. Isso é minimamente prejudicial aos processos de negócios existentes e permite investigar e provar o valor que você obtém da integração de aplicativos sem alterar a forma como as pessoas fazem negócios em seu ambiente hoje.

O Azure Active Directory (AD do Azure) tem uma galeria que contém milhares de aplicativos previamente integrados para que seja mais fácil começar. Você pode [Adicionar um aplicativo da Galeria à sua organização do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) com [tutoriais](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) passo a passo para a integração com aplicativos populares, como:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Margem de atraso](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Além disso, você pode [integrar aplicativos que não estão na Galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), incluindo qualquer aplicativo que já exista em sua organização ou qualquer aplicativo de terceiros de um fornecedor que ainda não faz parte da galeria do Azure AD. Você também pode [Adicionar seu aplicativo à Galeria](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) se ele não estiver lá.

Por fim, você também pode integrar os aplicativos desenvolvidos internamente. Isso é abordado na etapa cinco deste guia.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. determinar o uso do aplicativo e priorizar o trabalho existente

Em seguida, descubra os aplicativos que os funcionários estão frequentemente usando e Priorize seu trabalho para integrá-los ao Azure AD.

Você pode começar usando as [ferramentas de descoberta de nuvem](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) do Microsoft Cloud app Security&#39;para descobrir e gerenciar a &quot; &quot; ti de sombra em sua rede (ou seja, aplicativos não gerenciados pelo departamento de ti). Você pode [usar a ATP (proteção avançada contra ameaças) do Microsoft defender](https://docs.microsoft.com/cloud-app-security/wdatp-integration) para simplificar e estender o processo de descoberta.

Além disso, você pode usar o [AD FS relatório de atividade do aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) no portal do Azure para descobrir todos os aplicativos do AD FS em sua organização, o número de usuários exclusivos que entraram neles e a compatibilidade para integrá-los ao Azure AD.

Depois de descobrir seu panorama existente, você desejará [criar um plano](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) e priorizar os aplicativos de maior prioridade a serem integrados. Alguns exemplos de perguntas que você pode fazer para orientar esse processo são:

- Quais aplicativos são os mais usados?
- Quais são os difuso?
- Quais aplicativos serão encerrados no futuro, tornando a movimentação desnecessária?
- Quais aplicativos precisam permanecer no local e não podem ser movidos para a nuvem?

Você verá os maiores benefícios e economias de custos quando todos os seus aplicativos estiverem integrados e você não depender mais de várias soluções de identidade. No entanto, você terá um gerenciamento de identidade mais fácil e maior segurança à medida que você move o gradual para esse objetivo. Você deseja usar esse tempo para priorizar seu trabalho e decidir o que faz sentido para sua situação.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. integrar aplicativos que dependem de outros provedores de identidade

Durante o processo de descoberta, você pode encontrar aplicativos que não foram controlados pelo departamento de ti, o que deixa seus dados e recursos vulneráveis. Você também pode ter aplicativos que usam soluções de identidade alternativas, incluindo Serviços de Federação do Active Directory (AD FS) (ADFS) ou outros provedores de identidade. Considere como você pode consolidar seu gerenciamento de identidade e acesso para economizar dinheiro e aumentar a segurança. Reduzir o número de soluções de identidade que você terá:

- Economize dinheiro eliminando a necessidade de provisionamento e autenticação de usuário local, bem como taxas de licenciamento pagas para outros provedores de identidade de nuvem para o mesmo serviço.
- Reduza a sobrecarga administrativa e habilite uma segurança mais rígida com menos redundância em seu processo de gerenciamento de identidade e acesso.
- Permita que os funcionários obtenham acesso seguro de logon único a todos os aplicativos necessários por meio do [portal do myapps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).
- Melhore a inteligência dos serviços relacionados à [proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) do Azure ad&#39;s como acesso condicional, aumentando a quantidade de dados obtida do uso do aplicativo e amplie seus benefícios para os aplicativos recém-adicionados.

Publicamos diretrizes para gerenciar o processo comercial de integração de aplicativos com o Azure AD, incluindo um [cartaz](https://aka.ms/AppOnePager) e uma [apresentação](https://aka.ms/AppGuideline) que você pode usar para fazer com que os proprietários de negócios e aplicativos saibam e interessados. Você pode modificar esses exemplos com sua própria identidade visual e publicá-los em sua organização por meio do portal da empresa, do boletim informativo ou de outra mídia ao concluir esse processo.

Um bom lugar para começar é avaliar o uso de Serviços de Federação do Active Directory (AD FS) (ADFS). Muitas organizações usam o ADFS para autenticação com aplicativos SaaS, aplicativos de linha de negócios personalizados e aplicativos baseados no Microsoft Azure AD 365

![O diagrama mostra aplicativos locais, aplicativos de linha de negócios, aplicativos SaaS e, por meio do Azure AD, o Office 365 todos conectando com linhas pontilhadas em Active Directory e AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Você pode atualizar essa configuração [substituindo o ADFS pelo Azure ad como o centro](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) da sua solução de gerenciamento de identidade. Isso habilita o logon para cada aplicativo que seus funcionários desejam acessar e torna mais fácil para os funcionários encontrar qualquer aplicativo de negócios necessário por meio do [portal myapps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access), além dos outros benefícios mencionados acima.

![O diagrama mostra aplicativos locais por meio de Active Directory e AD FS, aplicativos de linha de negócios, aplicativos SaaS e o Office 365 todos conectando-se a linhas pontilhadas em Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Depois que o Azure AD se tornar o provedor de identidade central, você poderá mudar do ADFS completamente, em vez de usar uma solução federada. Os aplicativos que usavam o ADFS anteriormente para autenticação agora podem usar o Azure AD sozinho.

![O diagrama mostra o local, aplicativos de linha de negócios, aplicativos SaaS e o Office 365 todos conectando-se com linhas pontilhadas em Azure Active Directory. Active Directory e AD FS não estão presentes.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Você também pode migrar aplicativos que usam um provedor de identidade baseado em nuvem diferente para o Azure AD. Sua organização pode ter várias soluções de IAM (gerenciamento de acesso de identidade) em vigor. A migração para uma infraestrutura do Azure AD é uma oportunidade de reduzir as dependências em licenças IAM (no local ou na nuvem) e nos custos de infraestrutura. Nos casos em que você já deve ter pago pelo Azure AD por meio de licenças do M365, não há motivo para pagar o custo adicional de outra solução IAM.

## <a name="4-integrate-on-premises-applications"></a>4. integrar aplicativos locais

Tradicionalmente, os aplicativos eram mantidos seguros, permitindo o acesso somente enquanto estiverem conectados à rede corporativa. No entanto, em um mundo cada vez mais conectado, queremos permitir o acesso a aplicativos para clientes, parceiros e/ou funcionários, independentemente de onde estão no mundo. O [azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) é um recurso do Azure AD que conecta seus aplicativos locais existentes ao Azure AD e não requer que você mantenha servidores de borda ou outras infraestruturas adicionais para fazer isso.

![Um diagrama mostra o serviço de proxy de aplicativo em ação. Um usuário acessa " https://sales.contoso.com " e sua solicitação é redirecionada por meio https://sales-contoso.msappproxy.net de "" em Azure Active Directory ao endereço local " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Você pode usar [o tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) para habilitar o proxy de aplicativo e adicionar um aplicativo local ao seu locatário do Azure AD.

Além disso, você pode integrar os controladores de entrega de aplicativos como F5 Big-IP APM ou Zscaler Private Access. Integrando-os com o Azure AD, você obtém a autenticação moderna e o gerenciamento de identidade do Azure AD junto com os recursos de gerenciamento e segurança de tráfego do produto de parceiro. Chamamos essa solução de [acesso híbrido seguro](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Se você usar qualquer um dos serviços a seguir hoje, temos tutoriais que o orientarão sobre como integrá-los ao Azure AD.

- [EAA (Akamai Enterprise Application Access)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [ADC (controlador de entrega de aplicativos) da Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (anteriormente conhecido como Citrix Netscaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. integrar aplicativos que seus desenvolvedores criam

Para aplicativos criados em sua empresa, os desenvolvedores podem usar a plataforma de [identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) para implementar a autenticação e a autorização. Aplicativos integrados à plataforma com o ser [registrado no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) e gerenciados como qualquer outro aplicativo em seu portfólio.

Os desenvolvedores podem usar a plataforma para aplicativos de uso interno e aplicativos voltados para o cliente, e há outros benefícios que acompanham o uso da plataforma. As [MSAL (bibliotecas de autenticação da Microsoft)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), que fazem parte da plataforma, permitem que os desenvolvedores habilitem experiências modernas como a autenticação multifator e o uso de chaves de segurança para acessar seus aplicativos sem a necessidade de implementá-lo por conta própria. Além disso, aplicativos integrados com a plataforma Microsoft Identity podem acessar [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) um ponto de extremidade unificado de API fornecendo os dados de Microsoft 365 que descrevem os padrões de produtividade, identidade e segurança em uma organização. Os desenvolvedores podem usar essas informações para implementar recursos que aumentam a produtividade de seus usuários. Por exemplo, ao identificar as pessoas com as quais o usuário esteve interagindo recentemente e identificando-las na interface do usuário do aplicativo&#39;s.

Temos uma [série de vídeos](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) que fornece uma introdução abrangente à plataforma, bem como [muitos exemplos de código](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) em linguagens e plataformas com suporte.

## <a name="next-steps"></a>Próximas etapas

- [Recursos para migrar aplicativos para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
