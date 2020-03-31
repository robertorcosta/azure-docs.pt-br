---
title: Planeje a implantação de um painel de acesso ao diretório ativo do Azure
description: Orientação sobre a implantação do Painel de Acesso ao Diretório Ativo do Azure
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897064"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planeje a implantação de um painel de acesso ao diretório ativo do Azure

O Painel de Acesso ao Azure Active Directory (Azure AD) é um portal baseado na Web que ajuda a reduzir os custos de suporte, aumentar a produtividade e a segurança e reduzir a frustração do usuário. O sistema inclui relatórios detalhados que rastreiam quando você acessa o sistema e notifica os administradores de uso indevido ou abuso.

Usando o Painel de Acesso AD do Azure, você pode:

* Descubra e acesse todos os recursos conectados ao Azure AD da empresa, como aplicativos
* Solicite acesso a novos aplicativos e grupos
* Gerencie o acesso a esses recursos para outros
* Gerenciar redefinições de senha de autoatendimento e configurações de autenticação multifatorial do Azure
* Gerenciar seus dispositivos

Também permite que os administradores gerenciem:

* Termos de serviço
* Organizações
* Análises de acesso


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Benefícios da integração do Painel de Acesso AD do Azure

O Painel de Acesso AD do Azure beneficia as empresas das seguintes maneiras:

**Fornece uma experiência intuitiva do usuário**: O Painel de Acesso fornece uma única plataforma para todos os aplicativos conectados ao Logon Único (SSO) do Azure. Você tem um portal unificado para encontrar configurações e novos recursos existentes, como gerenciamento de grupo e redefinição de senha de autoatendimento, à medida que são adicionados. A experiência intuitiva permite que os usuários voltem ao trabalho mais rápido e sejam mais produtivos, ao mesmo tempo em que reduzem sua frustração.

**Aumenta a produtividade**: Todos os aplicativos de usuário no Painel de Acesso têm SSO ativado. A habilitação do SSO entre aplicativos corporativos e o Office 365 cria uma experiência de login superior, reduzindo ou eliminando solicitações adicionais de login. O Painel de Acesso usa autoatendimento e associação dinâmica e melhora a segurança geral do seu sistema de identidade. Ele faz isso garantindo que as pessoas certas gerenciem o acesso aos aplicativos. O Painel de Acesso serve como uma página de aterrissagem coerente para que você encontre rapidamente recursos e continue as tarefas de trabalho.

**Gerencia o custo**: Habilitar o Painel de Acesso com o Azure AD pode ajudar no desinvestimento de infra-estruturas locais. Ele reduz os custos de suporte fornecendo-lhe um portal consistente para encontrar todos os seus aplicativos, solicitar acesso aos recursos e gerenciar contas.

**Aumenta a flexibilidade e a segurança**: O Painel de Acesso lhe dá acesso à segurança e flexibilidade que uma plataforma em nuvem oferece. Os administradores podem alterar facilmente as configurações para aplicativos e recursos e podem acomodar novos requisitos de segurança sem afetar os usuários.

**Permite auditoria robusta e rastreamento de uso**: Auditoria e rastreamento de uso para todos os recursos do usuário permitem saber quando os usuários estão usando seus recursos e garante que você pode avaliar a segurança.

### <a name="licensing-considerations"></a>Considerações sobre licenciamento

O Painel de Acesso é gratuito e não requer licenças para usar em um nível básico. No entanto, o número de objetos em seu diretório e os recursos adicionais que você deseja implantar podem exigir licenças adicionais. Alguns cenários ad comuns do Azure que têm requisitos de licenciamento incluem os seguintes recursos de segurança:

* [Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Membros baseados em grupo](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Consulte o [guia completo de licenciamento do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Pré-requisitos para implantação do Painel de Acesso AD do Azure

Complete os seguintes pré-requisitos antes de iniciar este projeto:

* [Integrar aplicativo SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Gerenciar a infra-estrutura de usuários e grupos do Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planeje a implantação do Painel de Acesso AD do Azure

A tabela a seguir descreve os principais casos de uso para uma implantação do Painel de Acesso:

| Área| Descrição |
| - | - |
| Acesso| O portal do Painel de Acesso é acessível a partir de dispositivos corporativos e pessoais dentro da rede corporativa. |
|Acesso | O portal do Painel de Acesso é acessível a partir de dispositivos corporativos fora da rede corporativa. |
| Auditoria| Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias. |
| Governança| O ciclo de vida das atribuições do usuário para aplicativos e grupos conectados ao Azure AD é definido e monitorado. |
| Segurança| O acesso aos recursos é controlado através de atribuições de usuários e grupos. Apenas usuários autorizados podem gerenciar o acesso a recursos. |
| Desempenho| Os cronogramas de propagação de atribuição de acesso são documentados e monitorados. |
| Experiência do Usuário| Os usuários estão cientes dos recursos do Painel de Acesso e de como usá-los.|
| Experiência do Usuário| Os usuários podem gerenciar seu acesso a aplicativos e grupos.|
| Experiência do Usuário| Os usuários podem gerenciar suas contas. |
| Experiência do Usuário| Os usuários estão cientes da compatibilidade do navegador. |
| Suporte| Os usuários podem encontrar suporte para problemas do Painel de Acesso. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Melhores práticas para implantação do Painel de Acesso AD do Azure

A funcionalidade do Painel de Acesso pode ser habilitada gradualmente. Recomendamos a seguinte ordem de implantação:

1. Meus Aplicativos
   * Lançador de aplicativos
   * Gerenciamento de aplicativos de autoatendimento
   * Integração do Microsoft Office 365

1. Descoberta de aplicativos de autoatendimento
   * Redefinição de senha de autoatendimento
   * Configurações de autenticação multifatorial
   * Gerenciamento de dispositivos
   * Termos de uso
   * Gerenciar organizações

1. Meus Grupos
   * Gerenciamento de grupo de autoatendimento
1. Análises de acesso
   * Gerenciamento de revisão de acesso

Começando com meus aplicativos apresenta os usuários ao portal como um lugar comum para acessar recursos. A adição da descoberta de aplicativos de autoatendimento se baseia na experiência do My Apps. Meus grupos e avaliações de acesso se baseiam nos recursos de autoatendimento.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planejar configurações para o Painel de Acesso AD do Azure

A tabela a seguir lista várias configurações importantes do Painel de Acesso e os valores típicos que você pode usar:

| Configuração| Valores típicos |
| - | - |
| Determine os grupos piloto| Identifique o grupo de segurança Azure AD a ser usado e certifique-se de que todos os membros piloto fazem parte do grupo. |
| Determine o grupo ou os grupos a serem habilitados para a produção.| Identifique os grupos de segurança Azure AD ou os grupos active directory sincronizados com o Azure AD, a serem usados. Certifique-se de que todos os membros piloto fazem parte do grupo. |
| Permitir que os usuários usem SSO para certos tipos de aplicativos| SSO federado, OAuth, Senha SSO, Proxy de Aplicativo |
| Permitir que os usuários usem redefinição de senha de autoatendimento | Sim |
| Permitir que os usuários usem autenticação multifatorial| Sim |
| Permitir que os usuários usem o gerenciamento de grupos de autoatendimento para determinados tipos de grupos| Grupos de segurança, escritório 365 grupos |
| Permitir que os usuários usem o gerenciamento de aplicativos de autoatendimento| Sim |
| Permitir que os usuários usem avaliações de acesso| Sim |

### <a name="plan-consent-strategy"></a>Estratégia de consentimento do plano

Os usuários ou administradores devem consentir com os termos de uso e políticas de privacidade de qualquer aplicativo. Se possível, dadas as regras de sua empresa, use o consentimento do administrador, o que dá aos usuários uma melhor experiência.

Para usar o consentimento do administrador, você deve ser um administrador global da organização, e os aplicativos devem ser:

* Registrado em sua organização

* Registrado em outra organização Ad do Azure e previamente consentido por pelo menos um usuário

Para obter mais informações, consulte [Configurar a forma como os usuários finais concordam com um aplicativo no Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Envolva as partes interessadas certas

Quando os projetos de tecnologia falham, eles normalmente o fazem por causa de expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando os stakeholders certos](../fundamentals/active-directory-deployment-plans.md) e que os papéis das partes interessadas no projeto são bem compreendidos.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe proativamente seus usuários como e quando sua experiência mudará e como ganhar suporte, se necessário.

Embora o Painel de Acesso normalmente não crie problemas de usuário, é importante se preparar. Crie guias e uma lista de todos os recursos para o seu pessoal de suporte antes do seu lançamento.

#### <a name="communications-templates"></a>Modelos de comunicação

A Microsoft fornece [modelos personalizáveis para e-mails e outras comunicações](https://aka.ms/APTemplates) para o Painel de Acesso. Você pode adaptar esses ativos para uso em outros canais de comunicação conforme apropriado para sua cultura corporativa.

## <a name="plan-your-sso-configuration"></a>Planeje sua configuração de SSO

Quando um usuário entra em um aplicativo, ele passa por um processo de autenticação e é obrigado a provar quem é. Sem o SSO, uma senha é armazenada no aplicativo, e o usuário é obrigado a saber essa senha. Com o SSO, as credenciais dos usuários são passadas para o aplicativo, de modo que eles não precisam reinserir senhas para cada aplicativo.

Para lançar aplicativos em Meus Aplicativos, o SSO deve ser habilitado.

O Azure AD suporta três maneiras diferentes de habilitar o [login único em aplicativos](what-is-single-sign-on.md):

* **Logon único federado** 
    * Habilita um aplicativo para redirecionar para o Azure AD para autenticação do usuário, em vez de solicitar uma senha. 
    * É suportado para aplicativos que usam protocolos, como SAML 2.0, WS-Federation ou OpenID Connect, e é o modo mais rico de logon único.

* **Login único baseado em senha** 
    * Permite armazenamento e replay de senha de aplicativo seguro usando uma extensão do navegador da Web ou um aplicativo móvel. 
    * Aproveita o processo de login existente fornecido pelo aplicativo, mas permite que um administrador gerencie as senhas. O usuário não é obrigado a saber a senha.

* **Logon único existente** 
    * Permite que o Azure AD aproveite qualquer assinatura única existente que tenha sido configurada para o aplicativo.
    * Permite que esses aplicativos sejam vinculados aos portais do Office 365 ou do Azure AD Access Panel. 
    * Habilita relatórios adicionais no Azure AD quando os aplicativos são lançados lá. 
    * Inclui o uso do Proxy do aplicativo Azure e o modo de tipo único vinculado.

Saiba como configurar o modo SSO de um aplicativo aqui: [Único login para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Para obter a melhor experiência com a página Meus Aplicativos, comece com a integração de aplicativos em nuvem que estão disponíveis para SSO federado. O SSO federado permite que os usuários tenham uma experiência consistente de um clique em suas superfícies de lançamento de aplicativos e tende a ser mais robusto no controle de configuração.

Use O SSO federado com o Azure AD (OpenID Connect/SAML) quando um aplicativo o suporta, em vez de SSO e ADFS baseados em senha.

Para obter mais informações sobre como implantar e configurar seus aplicativos SaaS, consulte o [plano de implantação do SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planeje implantar a extensão do navegador My Apps

Quando os usuários fazem login em aplicativos SSO baseados em senha, eles precisam instalar e usar a extensão de login seguro Meus Aplicativos. A extensão executa um script que transmite a senha para o formulário de login do aplicativo. Os usuários são solicitados a instalar a extensão quando iniciam o aplicativo SSO baseado em senha. Mais informações sobre a extensão podem ser encontradas nesta documentação sobre [a instalação da extensão do navegador Access Panel](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Se você deve integrar aplicativos SSO baseados em senha, você deve definir um mecanismo para implantar a extensão em escala com [navegadores suportados](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). As opções incluem:

* [Política de grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Gerenciador de configuração para Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Download e configuração orientados pelo usuário para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Saiba mais: [Como configurar a senha de cada login único](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Os usuários que não usam aplicativos SSO baseados em senha também se beneficiam da extensão. Esses benefícios incluem a capacidade de lançar qualquer aplicativo a partir de sua barra de pesquisa, encontrar acesso a aplicativos usados recentemente e ter um link para a página Meus Aplicativos.

Veja o que o usuário verá ao lançar um aplicativo SSO baseado em senha pela primeira vez:

![Captura de tela da extensão do navegador Meus Aplicativos instale a tela ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plano para acesso móvel

Um navegador protegido com a política Intune (Microsoft Edge ou Intune Managed Browser) é necessário para usuários móveis que lançam aplicativos SSO baseados em senha. Um navegador protegido por políticas permite a transferência da senha salva para o aplicativo. O Microsoft Edge ou o navegador gerenciado fornecem um conjunto de recursos de proteção de dados da Web. Você também pode usar o Microsoft Edge para cenários corporativos em dispositivos iOS e Android. O Microsoft Edge suporta os mesmos cenários de gerenciamento do Intune Managed Browser e melhora a experiência do usuário. Saiba mais: [Gerencie o acesso à Web usando um navegador protegido por políticas do Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planeje sua implantação de meus aplicativos

A base do Painel de Acesso é o lançador [https://myapps.microsoft.com](https://myapps.microsoft.com/)de aplicativos Meus Aplicativos, que os usuários acessam em . As páginas Do Meus Aplicativos dão aos usuários um único lugar para iniciar seu trabalho e chegar aos aplicativos necessários. Aqui, os usuários encontram uma lista de todos os aplicativos aos quais têm acesso único de login. 

![Uma captura de tela do painel de aplicativos](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Os mesmos aplicativos serão mostrados no lançador de aplicativos office 365 quando os usuários estiverem usando o portal Office 365.

Planeje a ordem na qual você adicionará aplicativos ao lançador My Apps e decida se você vai lançá-los gradualmente ou todos de uma vez. Para isso, crie um inventário de aplicativos listando o tipo de autenticação e quaisquer integrações SSO existentes para cada aplicativo.

#### <a name="add-applications-to-the-my-apps-panel"></a>Adicionar aplicativos ao painel Meus Aplicativos

Qualquer aplicativo habilitado para SSO do Azure AD pode ser adicionado ao launcher My Apps. Outros aplicativos são adicionados usando a opção SSO vinculado. Você pode configurar um bloco de aplicativos que se vincula à URL do seu aplicativo web existente. O SSO vinculado permite que você comece a direcionar os usuários para o portal Meus Aplicativos sem migrar todos os aplicativos para o Azure AD SSO. Você pode gradualmente mover-se para aplicativos configurados pelo Azure AD SSO sem interromper a experiência dos usuários.

#### <a name="use-my-apps-collections"></a>Use minhas coleções de aplicativos

Por padrão, todos os aplicativos são listados juntos em uma única página. Mas você pode usar coleções para agrupar aplicativos relacionados e apresentá-los em uma guia separada, tornando-os mais fáceis de encontrar. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicativos para funções de trabalho específicas, tarefas, projetos e assim por diante. Para obter informações, [consulte Como usar as coleções do Meus Aplicativos para personalizar os painéis de acesso ao usuário.](access-panel-collections.md) 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planeje se deve usar meus aplicativos ou um portal existente

Seus usuários podem já ter um aplicativo ou portal diferente dos Meus Aplicativos. Em caso caso for, decida se deve apoiar ambos os portais ou usar apenas um.

Se um portal existente já estiver sendo usado como ponto de partida para os usuários, você pode integrar a funcionalidade Meus Aplicativos usando URLs de acesso ao usuário. UrLs de acesso ao usuário funcionam como links diretos para os aplicativos disponíveis no portal Meus Aplicativos. Esses URLs podem ser incorporados em qualquer site existente. Quando um usuário seleciona o link, ele abre o aplicativo no portal Meus Aplicativos.

Você pode encontrar a propriedade URL de acesso do usuário na área **Propriedades** do aplicativo no portal Azure.

![Uma captura de tela do painel de aplicativos](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planeje a descoberta e o acesso de aplicativos de autoatendimento

Uma vez que um conjunto principal de aplicativos é implantado na página Meus Aplicativos do usuário, você deve habilitar recursos de gerenciamento de aplicativos de autoatendimento. A descoberta de aplicativos de autoatendimento permite que os usuários:

* Encontre novos aplicativos para adicionar aos meus aplicativos. 
* Adicione aplicativos opcionais que eles podem não saber que precisam durante a configuração.

Os fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para acessar aplicativos. Os usuários que forem aprovadores receberão notificações no portal Meus Aplicativos quando houver solicitação pendente de acesso ao aplicativo.

## <a name="plan-self-service-group-membership"></a>Planejar a adesão ao grupo de autoatendimento 

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos do Office 365 no Azure AD. O proprietário do grupo pode aprovar ou negar pedidos de associação e delegar o controle da associação do grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança habilitados por e-mail ou listas de distribuição.

Para planejar a adesão ao grupo de autoatendimento, determine se você permitirá que todos os usuários da sua organização criem e gerenciem grupos ou apenas um subconjunto de usuários. Se você estiver permitindo um subconjunto de usuários, você precisará configurar um grupo ao qual essas pessoas são adicionadas. Consulte [Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para obter detalhes sobre como habilitar esses cenários.

## <a name="plan-reporting-and-auditing"></a>Planejar relatórios e auditorias

O Azure AD fornece [relatórios que oferecem insights técnicos e de negócios.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) Trabalhe com seus proprietários de aplicativos técnicos e de negócios para assumir a propriedade desses relatórios e consumi-los regularmente. A tabela a seguir fornece alguns exemplos de cenários típicos de relatórios.

|   | gerenciar riscos| Aumentar a produtividade| Governança e conformidade |
|  - |- | - | - |
| Tipos de relatório|  Permissões de aplicativo e uso| Atividade de provisionamento de conta| Revise quem está acessando os aplicativos |
| Ações potenciais| Acesso à auditoria; revogar permissões| Remediar quaisquer erros de provisionamento| Revogar acesso |

O Azure AD mantém a maioria dos dados de auditoria por 30 dias. Os dados estão disponíveis através do Portal azure Admin ou API para você baixar em seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os registros de auditoria para acesso ao aplicativo estão disponíveis por 30 dias. Se a auditoria de segurança dentro de sua empresa exigir maior retenção, os logs precisam ser exportados para uma ferramenta SIEM (Security Information Event and Management, gerenciamento de informações de segurança), como Splunk ou ArcSight.

Para auditoria, emissão de relatórios e backups de recuperação de desastres, documente a frequência necessária de download, qual é o sistema de destino e quem é responsável pelo gerenciamento de cada backup. Você pode não precisar de auditoria separada e backups de relatórios. Seu backup de recuperação de desastres deve ser uma entidade separada.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Implantar aplicativos no painel Meus Aplicativos dos usuários

Depois que um aplicativo é configurado para SSO, os grupos são atribuídos acesso. Os usuários dos grupos atribuídos terão acesso e verão o aplicativo em seus Meus Aplicativos e no lançador de aplicativos Office 365.

Consulte [Atribuir usuários e grupos a um aplicativo no Active Directory](methods-for-assigning-users-and-groups.md).

Se durante o teste ou implantação você quiser adicionar os grupos, mas ainda não permitir que os aplicativos apareçam em Meus aplicativos, consulte [Ocultar um aplicativo da experiência do usuário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Implantar aplicativos do Microsoft Office 365 no Meus Aplicativos

Para aplicativos do Office 365, os usuários recebem uma cópia do Office com base em licenças atribuídas a eles. Um pré-requisito para acesso aos aplicativos do Office é que os usuários sejam atribuídos às licenças corretas vinculadas aos aplicativos do Office. Quando você atribui uma licença a um usuário, ele verá automaticamente os aplicativos associados à licença em sua página Meus Aplicativos e no launcher de aplicativos do Office 365.

Se você quiser ocultar um conjunto de aplicativos do Office dos usuários, há uma opção para ocultar aplicativos do portal Meus Aplicativos, enquanto ainda permite o acesso do portal Office 365. Encontre essas configurações na parte de configurações do aplicativo. Saiba mais: [Ocultar um aplicativo da experiência do usuário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Uma captura de tela de como configurar aplicativos](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Implantar recursos de autoatendimento de aplicativos

O acesso a aplicativos de autoatendimento permite que os usuários se autodescubram e solicitem acesso aos aplicativos. Os usuários têm a liberdade de acessar os aplicativos de que precisam sem passar por um grupo de TI cada vez para solicitar acesso. Quando um usuário solicita acesso e é aprovado, automaticamente ou manualmente por um proprietário de aplicativo, ele é adicionado a um grupo no back-end. O relatório é habilitado sobre quem solicitou, aprovou ou removeu o acesso, e lhe dá controle sobre o gerenciamento das funções atribuídas.

Você pode delegar a aprovação de solicitações de acesso de aplicativos aos aprovadores de negócios. O aprovador de negócios pode definir as senhas de acesso do aplicativo a partir da página Meus Aplicativos do aprovador de negócios.

Saiba mais: [Como usar o acesso a aplicativos de autoatendimento.](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Uma captura de tela de configuração do gerenciamento de aplicativos de autoatendimento](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Validar sua implantação

Certifique-se de que a implantação do Painel de Acesso seja completamente testada e que um plano de reversão esteja em vigor.

Os seguintes testes devem ser realizados com dispositivos corporativos e dispositivos pessoais. Esses casos de teste também devem refletir os casos de uso da sua empresa. A seguir estão alguns casos baseados nos requisitos de negócios amostrais neste documento e em cenários técnicos típicos. Adicione outros específicos às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Exemplos de casos de teste de acesso ao Aplicativo SSO:


| caso comercial| Resultado esperado |
| - | -|
| O usuário entra no portal Meus Aplicativos| O usuário pode fazer login e ver seus aplicativos |
| Usuário lança um aplicativo SSO federado| O usuário está automaticamente conectado ao aplicativo |
| Usuário lança um aplicativo SSO de senha pela primeira vez| O usuário precisa instalar a extensão Meus Aplicativos |
| Usuário lança um aplicativo SSO de senha um tempo subsequente| O usuário está automaticamente conectado ao aplicativo |
| Usuário lança aplicativo do Portal Office 365| O usuário está automaticamente conectado ao aplicativo |
| Usuário lança um aplicativo do Navegador Gerenciado| O usuário está automaticamente conectado ao aplicativo |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Recursos de autoatendimento de aplicativos testam exemplos de casos


| caso comercial| Resultado esperado |
| - | - |
| O usuário pode gerenciar a adesão ao aplicativo| O usuário pode adicionar/remover membros que tenham acesso ao aplicativo |
| O usuário pode editar o aplicativo| O usuário pode editar a descrição do aplicativo e as credenciais para aplicativos SSO de senha |

### <a name="rollback-steps"></a>Etapas de reversão

É importante planejar o que fazer se a implantação não funcionar conforme o planejado. Se a configuração do SSO falhar durante a implantação, você deve entender como [solucionar problemas de SSO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e reduzir o impacto para seus usuários. Em circunstâncias extremas, você pode precisar [reverter o SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Gerencie sua implementação

Você deve usar a função menos privilegiada para realizar uma tarefa necessária dentro do Azure Active Directory. [Revise as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolha a certa para resolver suas necessidades para cada persona para este aplicativo. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a implantação ser concluída.

| Personas| Funções| Função de AD do Azure  |
| - | -| -|
| Admin helpdesk| Suporte de nível 1| Nenhum |
| Admin de identidade| Configurar e depurar quando os problemas afetam o Azure AD| Administrador global |
| Admin de aplicação| Atestado de usuário no aplicativo, configuração em usuários com permissões| Nenhum |
| Admins de infra-estrutura| Dono de capotamento cert| Administrador global |
| Proprietário/stakeholder do negócio| Atestado de usuário no aplicativo, configuração em usuários com permissões| Nenhum |

Você pode usar o [Gerenciamento de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) para gerenciar suas funções para fornecer auditoria, controle e revisão de acesso adicionais para usuários com permissões de diretório.

### <a name="troubleshoot-access-panel-issues"></a>Solucionar problemas do Painel de Acesso

Crie guias de solução de problemas para sua organização de suporte com cenários comuns, que apontam para a documentação da Microsoft em suas resoluções. Você pode querer criar guias que quebrem o suporte nos níveis usados pela sua organização.

Consulte estes guias de solução de problemas para referência:

[Aplicativos que não aparecem](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Aplicações inesperadas aparecendo](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[O usuário não pode fazer login no Painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemas com o acesso a aplicativos de autoatendimento](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemas com a extensão do navegador](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Próximas etapas

[Planeje uma implantação da Autenticação Multifatorial do Azure](https://aka.ms/deploymentplans/mfa)
