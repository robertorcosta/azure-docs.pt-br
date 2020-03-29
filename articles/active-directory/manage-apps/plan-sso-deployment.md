---
title: Planeje uma implantação única de login do Azure Active Directory
description: Guia para ajudá-lo a planejar, implantar e gerenciar o SSO em sua organização.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512820"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planejar uma implantação de logon único

O soco de logon único (SSO) significa acessar todos os aplicativos e recursos que um usuário precisa, fazendo login apenas uma vez usando uma única conta de usuário. Com o SSO, os usuários podem acessar todos os aplicativos necessários sem serem obrigados a autenticar uma segunda vez.

## <a name="benefits-of-sso"></a>Benefícios do SSO

O sode-on único (SSO) adiciona segurança e conveniência quando os usuários acessam aplicativos no Azure Active Directory (Azure AD). 

Muitas organizações dependem de aplicativos de software como serviço (SaaS), como Office 365, Box e Salesforce, para produtividade do usuário final. Historicamente, a equipe de TI precisava criar e atualizar contas de usuário individualmente em cada aplicativo SaaS, e os usuários precisavam lembrar de uma senha para cada um.

O Azure Marketplace possui mais de 3000 aplicativos com conexões SSO pré-integradas, facilitando sua integração em seu inquilino.

## <a name="licensing"></a>Licenciamento

- **Licenciamento Azure AD** - SSO para aplicações SaaS pré-integradas é gratuito. No entanto, o número de objetos em seu diretório e os recursos que você deseja implantar podem exigir licenças adicionais. Para obter uma lista completa de requisitos de licença, consulte [Preços do Diretório Ativo do Azure](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenciamento de aplicativos** - Você precisará das licenças apropriadas para seus aplicativos SaaS para atender às suas necessidades de negócios. Trabalhe com o proprietário do aplicativo para determinar se os usuários atribuídos ao aplicativo têm as licenças apropriadas para suas funções dentro do aplicativo. Se o Azure AD gerenciar o provisionamento automático com base em funções, as funções atribuídas no Azure AD devem estar alinhadas com o número de licenças de propriedade do aplicativo. O número indevido de licenças de propriedade do aplicativo pode levar a erros durante o provisionamento/atualização de um usuário.

## <a name="plan-your-sso-team"></a>Planeje sua equipe de SSO

- **Engajar as partes interessadas certas** - Quando os projetos de tecnologia falham, é normalmente devido a expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando as partes interessadas certas](https://aka.ms/deploymentplans) e que as partes interessadas entendam seus papéis.
- **Planejamento de comunicações** - A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique proativamente com seus usuários sobre como sua experiência mudará, quando ela mudará e como ganhar suporte se eles tiverem problemas. Revise as opções [de como os usuários finais acessarão seus aplicativos habilitados para SSO](end-user-experiences.md)e crie suas comunicações para corresponder à sua seleção. 

## <a name="plan-your-sso-protocol"></a>Planeje seu protocolo SSO

Uma implementação de SSO baseada em protocolos de federação melhora a segurança, a confiabilidade e as experiências do usuário final e é mais fácil de implementar. Muitos aplicativos são pré-integrados no Azure AD com [guias passo a passo disponíveis](../saas-apps/tutorial-list.md). Você pode encontrá-los em [nosso Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informações detalhadas sobre cada método SSO podem ser encontradas no artigo [Único login para aplicativos no Azure Active Directory](what-is-single-sign-on.md).

Existem duas maneiras principais pelas quais você pode habilitar seus usuários a fazer em um único login em seus aplicativos:

- **Com a inscrição única federada** O Azure AD autentica o usuário para o aplicativo usando sua conta Azure AD. Este método é suportado para aplicativos que suportam protocolos como SAML 2.0, WS-Federation ou OpenID Connect, e é o modo mais rico de logon único. Recomendamos o uso do SSO federado com o Azure AD quando um aplicativo o suporta, em vez de SSO e ADFS baseados em senha.

- **Com o** login único baseado em senha que os usuários fazem login no aplicativo com um nome de usuário e senha na primeira vez, eles acessam-no. Após o primeiro logon, o Azure AD fornece o nome de usuário e a senha ao aplicativo. O logon único baseado em senha permite o armazenamento e a reprodução segura de senhas do aplicativo usando uma extensão de navegador da Web ou aplicativo móvel. Essa opção aproveita o processo de login existente fornecido pelo aplicativo, permite que um administrador gerencie as senhas e não exige que o usuário saiba a senha.

### <a name="considerations-for-federation-based-sso"></a>Considerações para o SSO baseado na federação

- **Usando OpenID Connect e OAuth** - Se o aplicativo que você está conectando o suporta, use o método OIDC/OAuth 2.0 para habilitar seu SSO para esse aplicativo. Este método requer menos configuração e permite uma experiência de usuário mais rica. Para obter mais informações, consulte o guia do desenvolvedor [do OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)e [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Configurações de ponto final para SSO baseado em SAML** - Se você usar o SAML, seus desenvolvedores precisarão de informações específicas antes de configurar o aplicativo. Para obter mais informações, consulte [Editar a configuração básica do SAML](configure-single-sign-on-non-gallery-applications.md).
- **Gerenciamento de certificados para SSO baseado em SAML** - Quando você habilita O SSO federado para sua aplicação, o Azure AD cria um certificado que é por padrão válido por três anos. Você pode personalizar a data de validade desse certificado, se necessário. Certifique-se de que você tem processos em vigor para renovar certificados antes de sua expiração. Para saber mais, consulte [Azure AD Managing Certificates](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Considerações para SSO baseado em senha

O uso do Azure AD para SSO baseado em senha requer a implantação de uma extensão de navegador que recuperará as credenciais com segurança e preencherá os formulários de login. Defina um mecanismo para implantar a extensão em escala com [navegadores suportados](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). As opções incluem:

- [Política de grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Gerenciador de configuração para Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Download e configuração orientados pelo usuário para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Para saber mais, consulte [Como configurar o sinal único de senha.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Capturando formulários de login metadados para aplicativos que não estão na galeria

A Microsoft suporta a captura de metadados em um aplicativo web para saque de senhas (capturando os campos de nome de usuário e senha). Navegue até a URL de login durante o processo de configuração do aplicativo para capturar os metadados de formulários. Peça ao proprietário do aplicativo a URL de login exata. Essas informações são usadas durante o processo de login, mapeando as credenciais do Azure AD para o aplicativo durante a ativação.

Para saber mais, consulte [O que é acesso ao aplicativo e SSO com o Azure AD? – SSO baseado em senha](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicações de que metadados em formulários precisam ser recapturados

Quando os aplicativos mudam seu layout HTML, talvez seja necessário recapturar os metadados para ajustar as alterações. Sintomas comuns que indicam que o layout HTML tem alteração incluem:

- Usuários relatando que clicar no aplicativo fica "preso" na página de login
- Usuários relatando que o nome de usuário ou senha não está preenchido

#### <a name="shared-accounts"></a>Contas compartilhadas

Do ponto de vista do login, os aplicativos com contas compartilhadas não são diferentes de um aplicativo de galeria que usa sso de senha para usuários individuais. No entanto, existem algumas etapas adicionais necessárias ao planejar e configurar um aplicativo destinado a usar contas compartilhadas:

1. Trabalhe com usuários de negócios de aplicativos para documentar o seguinte:
   1. Conjunto de usuários na organização que usará o aplicativo
   1. Conjunto existente de credenciais no aplicativo associado ao conjunto de usuários 
1. Para cada combinação de conjunto de usuários e credenciais, crie um grupo de segurança na nuvem ou no local com base em suas necessidades.
1. Redefinir as credenciais compartilhadas. Uma vez que o aplicativo é implantado no Azure AD, os indivíduos não precisam da senha da conta compartilhada. Uma vez que o Azure AD armazenará a senha, considere defini-la como muito longa e complexa. 
1. Configure a rolagem automática da senha se o aplicativo a suportar. Dessa forma, nem mesmo o administrador que fez a configuração inicial saberá a senha da conta compartilhada. 

## <a name="plan-your-authentication-method"></a>Planeje seu método de autenticação

Escolher o método de autenticação correto é uma primeira decisão fundamental na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado usando o Azure AD Connect, que também provisiona usuários na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo. Você deve escolher o que mais combina com seu cenário específico. Para obter mais informações, consulte [Escolha o método de autenticação certo para a solução de identidade híbrida do Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Planeje sua segurança e governança 

A identidade é o novo pivô principal para a atenção à segurança e investimentos porque os perímetros de rede tornaram-se cada vez mais porosos e menos eficazes com a explosão de dispositivos BYOD e aplicações em nuvem. 

### <a name="plan-access-reviews"></a>Análises de acesso de planos

[As Avaliações de Acesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) permitem que as organizações gerenciem com eficiência membros de grupo, acesso a aplicativos corporativos e atribuições de função. Você deve planejar revisar o acesso do usuário regularmente para garantir que apenas as pessoas certas tenham acesso contínuo.

Alguns dos principais tópicos a serem planejados durante a criação de avaliações de acesso incluem:

1. Identificar uma cadência para avaliações de acesso que se ajustem às necessidades do seu negócio. Isso pode ser tão frequente quanto uma vez por semana, mensalmente, anualmente ou como um exercício demanda.

1. Crie grupos que representam os revisores dos relatórios de acesso ao aplicativo. Você precisará garantir que as partes interessadas mais familiarizadas com o aplicativo e seus usuários-alvo e casos de uso sejam participantes de suas avaliações de acesso

1. Completar uma revisão de acesso inclui tirar permissões de acesso de aplicativos para usuários que não precisam mais de acesso. Plano para lidar com possíveis solicitações de suporte de usuários negados. Um usuário excluído permanecerá excluído no Azure AD por 30 dias durante o qual poderá ser restaurado por um administrador, se necessário. Após 30 dias, esse usuário será excluído permanentemente. Usando o portal Azure Active Directory, um administrador global pode excluir explicitamente permanentemente um usuário recentemente excluído antes que esse período de tempo seja atingido.

### <a name="plan-auditing"></a>Auditoria de planos

O Azure AD fornece [relatórios contendo insights técnicos e de negócios.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) 

Tanto os relatórios de segurança quanto de atividades estão disponíveis. Relatórios de segurança mostram os usuários sinalizados para riscos e logins arriscados. Relatórios de atividade ajudam você a entender o comportamento dos usuários em sua organização, detalhando a atividade de login e fornecendo trilhas de auditoria de todos os logins. Você pode usar relatórios para gerenciar riscos, aumentar a produtividade e monitorar a conformidade.

| Tipo de relatório | Revisão de acesso | Relatórios de segurança | Relatório de login |
|-------------|---------------|------------------|----------------|
| Use para revisar | Permissões de aplicativo e uso. | Contas potencialmente comprometidas | Quem está acessando os aplicativos |
| Ações potenciais | Acesso à auditoria; revogar permissões | Revogar o acesso; força reset de segurança | Revogar acesso |

O Azure AD retém a maioria dos dados de auditoria por 30 dias e disponibiliza os dados através do portal admin azure ou através de uma API para você baixar em seus sistemas de análise.

### <a name="consider-using-microsoft-cloud-application-security"></a>Considere usar o Microsoft Cloud Application Security

O Microsoft Cloud App Security (MCAS) é uma solução CASB (Cloud Access Security Broker). Ele dá visibilidade aos seus aplicativos e serviços em nuvem, fornece análises sofisticadas para identificar e combater ameaças cibernéticas e permite que você controle como seus dados viajam.

A implantação do MCAS permite:

- Use o Cloud Discovery para mapear e identificar seu ambiente em nuvem e os aplicativos em nuvem que sua organização está usando.
- Aplicativos sancionadores e sem sanções em sua nuvem
- Use conectores de aplicativos fáceis de implantar que aproveitem as APIs do provedor, para visibilidade e governança dos aplicativos aos que você se conecta
- Use a proteção de controle de aplicativos de acesso condicional para obter visibilidade e controle em tempo real sobre acesso e atividades dentro de seus aplicativos na nuvem
- Ajuda você a ter controle contínuo, configurando e, em seguida, continuamente afinando políticas.

O controle de sessão de sessão do Microsoft Cloud Application Security (MCAS) está disponível para qualquer navegador em qualquer plataforma principal em qualquer sistema operacional. Aplicativos móveis e aplicativos da área de trabalho também podem ser bloqueados ou permitidos. Ao integrar nativamente com o Azure AD, todos os aplicativos configurados com SAML ou aplicativos Open ID Connect com logon único no Azure AD podem ser suportados, incluindo [vários aplicativos em destaque](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Para obter informações sobre o MCAS, consulte a visão geral do [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). McAS é um serviço de assinatura baseado no usuário. Você pode rever os detalhes do licenciamento na [folha de dados de licenciamento do MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Usar o acesso condicional

Com o Conditional Access, você pode automatizar decisões de controle de acesso baseadas em critérios para seus aplicativos em nuvem.

As políticas de Acesso Condicional são impostas após a conclusão da autenticação multifator. Portanto, o Conditional Access não se destina a ser uma defesa de primeira linha para cenários como ataques de negação de serviço (DoS), mas pode usar sinais desses eventos para determinar o acesso. Por exemplo, o nível de risco de login, a localização da solicitação e assim por diante podem ser usados. Para obter mais informações sobre o Acesso Condicional, consulte [a visão geral](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e o plano de [implantação.](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)

## <a name="azure-sso-technical-requirements"></a>Requisitos técnicos do Azure SSO

A seção a seguir detalha os requisitos para configurar seu aplicativo específico, incluindo os ambientes necessários, pontos finais, mapeamento de reclamações, atributos, certificados e protocolos necessários usados. Você precisará dessas informações para configurar o SSO no [portal Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Detalhes do mecanismo de autenticação

Para todos os aplicativos SaaS pré-integrados, a Microsoft fornece um tutorial e você não precisará dessas informações. Se o aplicativo não estiver em nosso marketplace/galeria de aplicativos, você pode precisar coletar os seguintes dados:

- **Provedor de identidade atual que o aplicativo usa para SSO, se aplicável** - Por exemplo: AD FS, PingFederate, Okta
- **Protocolos suportados pelo aplicativo de destino** - Por exemplo, SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protocolo sendo configurado com Azure AD** - Por exemplo, SAML 2.0 ou 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Requisitos de atributo

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de usuário Do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos. Planeje o mapeamento dos atributos do usuário do Azure AD para o seu aplicativo e [personalize os mapeamentos de atributos padrão de](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) acordo com as necessidades da sua empresa.

### <a name="certificate-requirements"></a>Requisitos de certificado

O certificado para o aplicativo deve estar atualizado, ou há o risco de os usuários não conseguirem acessar o aplicativo. A maioria dos certificados de aplicação SaaS são bons por 36 meses. Você altera a duração do certificado na lâmina do aplicativo. Certifique-se de documentar o vencimento e saber como você gerenciará sua renovação de certificado. 

Existem duas maneiras de gerenciar seus certificados. 

- **Rolagem automática de certificados** - A Microsoft suporta [a assinatura de rolagem de chaves no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Embora este seja o nosso método preferido para gerenciar certificados, nem todos os ISV suportam esse cenário.

- **Atualização manual** - Cada aplicativo tem seu próprio certificado que expira com base na forma como é definido. Antes de o certificado do aplicativo expirar, crie um novo certificado e envie-o para o ISV. Essas informações podem ser retiradas dos metadados da federação. [Leia mais sobre metadados da federação aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementar SSO

Use as seguintes fases para planejar e implantar sua solução em sua organização:

### <a name="user-configuration-for-sso"></a>Configuração do usuário para SSO

- **Identifique seus usuários de teste**

   Entre em contato com o proprietário do aplicativo e peça que eles criem um mínimo de três usuários de teste dentro do aplicativo. Certifique-se de que as informações que você usará como identificador principal são preenchidas corretamente e correspondem a um atributo disponível no Azure AD. Na maioria dos casos, isso será mapeado para o "NameID" para aplicativos baseados em SAML. Para os tokens JWT, é o "preferred_username".
   
   Crie o usuário no Azure AD manualmente como um usuário baseado em nuvem ou sincronize o usuário a partir do local usando o mecanismo de sincronização Azure AD Connect. Certifique-se de que as informações correspondem às reivindicações que estão sendo enviadas ao aplicativo.

- **Configure o SSO**

   Na [lista de aplicativos,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)localize e abra o tutorial SSO para o seu aplicativo e siga os passos do tutorial para configurar com sucesso seu aplicativo SaaS.

   Se você não conseguir localizar seu aplicativo, consulte [a documentação do aplicativo personalizado](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Isso irá mostrar como adicionar um aplicativo que não está localizado na galeria Azure AD.

   Opcionalmente, você pode usar reclamações emitidas no token SAML para o aplicativo corporativo usando a [documentação de orientação da Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Certifique-se de que este mapeie o que você espera receber na resposta SAML para o seu aplicativo. Se você encontrar problemas durante a configuração, use nossa orientação sobre [como depurar a integração SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

O onboarding de aplicativos personalizados é um recurso de licenças AD Premium P1 ou P2 do Azure.

### <a name="provide-sso-change-communications-to-end-users"></a>Forneça comunicações de alteração de SSO para usuários finais

Implemente seu plano de comunicação. Certifique-se de que você está deixando seus usuários finais saberem que uma mudança está chegando, quando ela chegou, o que fazer agora e como buscar ajuda.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifique os cenários do usuário final para OSS

Você pode usar os seguintes casos de teste para realizar testes em dispositivos pessoais e corporativos para garantir que suas configurações de SSO estejam funcionando conforme o esperado. Os cenários abaixo supõem que um usuário está navegando para uma URL de aplicativo e passando por um fluxo de autenticação iniciado pelo provedor de serviços (sp-iniciado auth flow).

| Cenário | Resultado esperado no fluxo auth iniciado por SP pelo usuário |
|----------|---------------------------------------------------|
| Faça login no aplicativo com IE enquanto estiver no corpnet. | A Autenticação Integrada do Windows (IWA) ocorre sem solicitações adicionais. |
| Faça login no aplicativo com IE enquanto estiver fora do corpnet com nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O usuário faz login e solicitações do navegador para o MFA. |
| Faça login no aplicativo com IE enquanto estiver fora do corpnet com uma sessão atual e nunca tenha realizado o MFA. | O usuário não recebe solicitação de primeiro fator. O usuário recebe solicitação de MFA. |
| Faça login no aplicativo com IE enquanto estiver fora da corpnet com uma sessão atual e já realizou o MFA nesta sessão. | O usuário não recebe solicitação de primeiro fator. O usuário não recebe MFA. SSOs do usuário em aplicação. |
| Faça login no aplicativo com o Chrome/Firefox/Safari enquanto estiver fora do corpnet com uma sessão atual e já tenha realizado o MFA nesta sessão. | O usuário não recebe solicitação de primeiro fator. O usuário não recebe MFA. O SSO do usuário está em aplicação. |
| Faça login no aplicativo com o Chrome/Firefox/Safari enquanto estiver fora do corpnet com nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O usuário faz login e solicitações do navegador para o MFA. |
| Faça login no aplicativo com o Chrome/Firefox enquanto estiver na rede corporativa com uma sessão atual. | O usuário não recebe solicitação de primeiro fator. O usuário não recebe MFA. O SSO do usuário está em aplicação. |
| Faça login no aplicativo com aplicativo móvel com uma nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O usuário faz login com sucesso e solicitações do cliente ADAL para MFA. |
| O usuário não autorizado tenta entrar no aplicativo com URL de login. | Solicitação baseada em formulários no AD FS Server. O usuário não faz login com o primeiro fator. |
| O usuário autorizado tenta fazer login, mas insere uma senha incorreta. | O usuário navega até a URL do aplicativo e recebe erro de nome de usuário/senha ruim. |
| O usuário autorizado clica no link em um e-mail e já está autenticado. | O usuário clica na URL e é conectado ao aplicativo sem solicitações adicionais. |
| O usuário autorizado clica no link em um e-mail e ainda não está autenticado. | O usuário clica na URL e é solicitado a autenticar com o primeiro fator. |
| Usuário autorizado faz login no aplicativo com aplicativo móvel (SP iniciado) com uma nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O usuário faz login com sucesso e solicitações do cliente ADAL para MFA. |
| O Usuário não autorizado tenta entrar no aplicativo com URL de login (SP iniciado). | Solicitação baseada em formulários no AD FS Server. O usuário não faz login com o primeiro fator. |
| O usuário autorizado tenta fazer login, mas insere uma senha incorreta.| O usuário navega até a URL do aplicativo e recebe erro de nome de usuário/senha ruim. |
| O usuário autorizado faz logout e faz login novamente. | Se a URL de saída estiver configurada, o usuário será logado em todos os serviços e solicitará a autenticação. |
| O usuário autorizado faz logout e faz login novamente. | Se a URL de saída de sign-out não estiver configurada, o usuário será automaticamente logado de volta usando o token existente a partir da sessão de navegador Azure AD existente. |
| O usuário autorizado clica no link em um e-mail e já está autenticado. | O usuário clica na URL e é conectado ao aplicativo sem solicitações adicionais. |
| O usuário autorizado clica no link em um e-mail e ainda não está autenticado. | O usuário clica na URL e é solicitado a autenticar com o primeiro fator. |

## <a name="manage-sso"></a>Gerenciar SSO

Esta seção descreve os requisitos e recomendações para gerenciar com sucesso o SSO.

### <a name="required-administrative-roles"></a>Funções administrativas obrigatórias

Use sempre a função com o menor número de permissões disponíveis para realizar a tarefa necessária dentro do Azure Active Directory. A Microsoft recomenda [revisar as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a certa para resolver suas necessidades para cada persona para este aplicativo. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a implantação ter sido concluída.

| Persona| Funções | Função Azure AD (se necessário) |
|--------|-------|-----------------------------|
| Admin help desk | Suporte de nível 1 | Nenhum |
| Admin de identidade | Configurar e depurar quando os problemas afetam o Azure AD | Administrador global |
| Admin de aplicação | Atestado de usuário no aplicativo, configuração em usuários com permissões | Nenhum |
| Admins de infra-estrutura | Dono de capotamento cert | Administrador global |
| Proprietário/stakeholder do negócio | Atestado de usuário no aplicativo, configuração em usuários com permissões | Nenhum |

Recomendamos o uso [do Pim (Privileged Identity Management, gerenciamento de identidade privilegiado)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) para gerenciar suas funções para fornecer auditoria, controle e revisão de acesso adicionais para usuários com permissões de diretório.

### <a name="sso-certificate-lifecycle-management"></a>Gerenciamento do ciclo de vida do certificado SSO

É importante identificar as funções certas e listas de distribuição de e-mail encarregadas de gerenciar o ciclo de vida do certificado de assinatura entre o Azure AD e o aplicativo que está sendo configurado com o único login. Aqui estão alguns dos principais papéis que recomendamos ter em vigor:

- Proprietário para atualizar propriedades do usuário no aplicativo
- Suporte ao proprietário on-call para quebra/correção de aplicativos
- Lista de distribuição de e-mail monitorada de perto para notificações de alteração relacionadas a certificados

A duração máxima de um certificado é de três anos. Recomendamos estabelecer um processo sobre como você lidará com uma alteração de certificado entre o Azure AD e sua aplicação. Isso pode ajudar a prevenir ou minimizar uma paralisação devido ao vencimento de um certificado ou à rolagem do certificado de força.

### <a name="rollback-process"></a>Processo de reversão

Depois de concluir os testes com base em seus casos de teste, é hora de entrar em produção com sua aplicação. Mudar para a produção significa que você implementará suas configurações planejadas e testadas em seu inquilino de produção e a distribuirá para seus usuários. No entanto, é importante planejar o que fazer caso sua implantação não seja como planejado. Se a configuração sso falhar durante a implantação, você deve entender como mitigar qualquer paralisação e reduzir o impacto para seus usuários.

A disponibilidade de métodos de autenticação dentro do aplicativo determinará sua melhor estratégia. Certifique-se sempre de ter documentação detalhada para os proprietários de aplicativos sobre exatamente como voltar ao estado de configuração de login original, caso sua implantação tenha problemas.

- **Se o aplicativo suportar vários provedores de identidade,** por exemplo, LDAP e AD FS e Ping, não exclua a configuração sso existente durante a implantação. Em vez disso, desative-o durante a migração no caso de precisar trocá-lo de volta mais tarde. 

- **Se o aplicativo não suportar vários IDPs,** mas permitir que os usuários façam login usando autenticação baseada em formulários (nome de usuário/senha), certifique-se de que os usuários podem voltar a essa abordagem caso a nova implantação de configuração SSO falhe.

### <a name="access-management"></a>Gerenciamento de acesso

Recomendamos escolher uma abordagem dimensionada ao gerenciar o acesso aos recursos. As abordagens comuns incluem utilizar grupos locais sincronizando-se via Azure AD Connect, [criando Grupos Dinâmicos no Azure AD com base em atributos do usuário](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)ou criando grupos de [autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) no Azure AD gerenciados por um proprietário de recursos.

### <a name="monitor-security"></a>Monitore a segurança

Recomendamos a criação de uma cadência regular na qual você revise os diferentes aspectos da segurança do aplicativo SaaS e realize quaisquer ações corretivas necessárias.

### <a name="troubleshooting"></a>Solução de problemas

Os links a seguir apresentam cenários de solução de problemas. Você pode querer criar um guia específico para sua equipe de suporte que incorpore esses cenários e as etapas para corrigi-los.

#### <a name="consent-issues"></a>Questões de consentimento

- [Erro de consentimento inesperado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Erro de consentimento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemas de credenciais

- [Problemas ao entrar em um portal personalizado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemas para entrar no painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Erro na página de entrada do aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problema saqueando um aplicativo da Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemas de SSO para aplicativos listados na Azure Application Gallery

- [Problema com senha SSO para aplicativos listados na Galeria de Aplicativos do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema com SSO federado para aplicações listadas na Galeria de Aplicativos do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemas de SSO para aplicativos NÃO listados na Galeria de Aplicativos do Azure

- [Problema com senha SSO para aplicativos NÃO listados na Galeria de Aplicativos do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema com SSO federado para aplicações NÃO listadas na Galeria de Aplicativos do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Próximas etapas

[Depurar o SSO baseado em SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapeamento de sinistros para aplicativos via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalização de sinistros emitidos em token SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocolo SAML de inscrição única](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocolo SAML de Logout Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (para usuários externos, como parceiros e fornecedores)

[Acesso Condicional aazure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Proteção de identidade azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Acesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Tutorial do Aplicativo SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
