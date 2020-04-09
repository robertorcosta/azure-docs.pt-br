---
title: Planos de implantação - Azure Active Directory | Microsoft Docs
description: Fornece orientação de ponta a ponta sobre como implantar recursos do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4538afcef85c4a6eaef4213133963ecab9987e1f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876210"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implantação do Azure Active Directory
Procurando orientação de ponta a ponta sobre a implantação de recursos do Azure Active Directory (Azure AD) ? Os planos de implantação do Azure AD o investigam sobre o valor do negócio, as considerações de planejamento e os procedimentos operacionais necessários para implantar com sucesso os recursos comuns do Azure AD.

Em qualquer uma das páginas do plano, use o recurso Print to PDF do seu navegador para criar uma versão off-line atualizada da documentação.
## <a name="include-the-right-stakeholders"></a>Inclua as partes interessadas certas

Ao iniciar seu planejamento de implantação para um novo recurso, é importante incluir os principais stakeholders em toda a sua organização. Recomendamos que você identifique e documente a pessoa ou as pessoas que cumprem cada uma das seguintes funções, e trabalhe com elas para determinar seu envolvimento no projeto.  

Os papéis podem incluir o seguinte 

|Função |Descrição |
|-|-|
|Usuário final|Um grupo representativo de usuários para o qual o recurso será implementado. Muitas vezes visualiza as mudanças em um programa piloto.
|Gerente de suporte de TI|Representante da organização de suporte de TI que pode fornecer informações sobre a suportabilidade dessa mudança a partir de uma perspectiva de helpdesk.  
|Arquiteto de identidade ou administrador global do Azure|Representante da equipe de gerenciamento de identidade responsável por definir como essa mudança está alinhada com a infra-estrutura principal de gerenciamento de identidade em sua organização.|
|Proprietário de negócios de aplicativos |O proprietário geral do negócio dos aplicativos afetados, que pode incluir o gerenciamento do acesso.Também pode fornecer informações sobre a experiência do usuário e a utilidade dessa mudança a partir da perspectiva de um usuário final.
|Proprietário de Segurança|Um representante da equipe de segurança que pode assinar que o plano atenderá aos requisitos de segurança da sua organização.|
|Gerenciador de conformidade|A pessoa dentro de sua organização responsável por garantir o cumprimento das exigências corporativas, industriais ou governamentais.|

**Os níveis de envolvimento podem incluir:**

- **R**esponsible para a implementação de plano de projeto e resultado 

- **Um**pproval de plano de projeto e resultado 

- **C**ontributor para projeto plano e resultado 

- **Eu**não meforno do plano de projeto e do resultado


## <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto
Um piloto permite que você teste com um pequeno grupo antes de ligar uma capacidade para todos. Certifique-se de que, como parte de seus testes, cada caso de uso dentro de sua organização seja completamente testado. É melhor atingir um grupo específico de usuários piloto antes de passar isso para sua organização como um todo.

Na sua primeira onda, direcione a TI, a usabilidade e outros usuários apropriados que podem testar e fornecer feedback. Esse feedback deve ser usado para desenvolver ainda mais as comunicações e instruções enviadas aos seus usuários e para fornecer insights sobre os tipos de problemas que sua equipe de suporte pode ver. 

A ampliação da implantação para grupos maiores de usuários deve ser realizada aumentando o escopo dos grupos direcionados. Isso pode ser feito através da [adesão dinâmica do grupo](../users-groups-roles/groups-dynamic-membership.md)ou adicionando manualmente os usuários ao grupo-alvo( s).


## <a name="deploy-authentication"></a>Implantar autenticação

| Recurso | Descrição|
| -| -|
| [Autenticação Multifator](../authentication/howto-mfa-getstarted.md)| A Autenticação Multifator do Azure é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, o Azure MFA ajuda a proteger o acesso aos seus dados e aplicativos, ao mesmo tempo em que atende à demanda por um simples processo de login. |
| [Acesso Condicional](../conditional-access/plan-conditional-access.md)| Com o Conditional Access, você pode implementar decisões automatizadas de controle de acesso para quem pode acessar seus aplicativos na nuvem, com base nas condições. |
| [Redefinição de senha de autoatendimento](../authentication/howto-sspr-deployment.md)| A redefinição da senha de autoatendimento ajuda seus usuários a redefinir suas senhas sem intervenção do administrador, quando e onde eles precisam. |
| [Sem senha](../authentication/howto-authentication-passwordless-deployment.md) | Implemente autenticação sem senha usando o aplicativo Microsoft Authenticator ou as chaves FIDO2 Security em sua organização |

## <a name="deploy-application-management"></a>Implantar o gerenciamento de aplicativos

| Recurso | Descrição|
| -| - |
| [Logon único](../manage-apps/plan-sso-deployment.md)| O logon único ajuda seus usuários a acessar os aplicativos e recursos de que precisam para fazer negócios enquanto fazem login apenas uma vez. Depois de fazerem o registro, eles podem ir do Microsoft Office ao SalesForce to Box para aplicativos internos sem serem obrigados a inserir credenciais uma segunda vez. |
| [Painel de acesso](../manage-apps/access-panel-deployment-plan.md)| Oferece a seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Permitir que eles sejam mais produtivos com recursos de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciar o acesso a recursos em nome de terceiros. |


## <a name="deploy-hybrid-scenarios"></a>Implantar cenários híbridos

| Recurso | Descrição|
| -| -|
| [ADFS para sincronização de hash de senha](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Com a sincronização do Hash password, os hashes das senhas de usuário são sincronizados do Active Directory on-premises para o Azure AD, permitindo que o Azure AD autentique usuários sem interação com o Active Directory no local |
| [ADFS para autenticação de passagem](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| A autenticação de passagem do AD do Azure ajuda seus usuários a entrar em aplicativos no local e na nuvem usando as mesmas senhas. Esse recurso oferece aos usuários uma experiência melhor - uma senha a menos para lembrar - e reduz os custos do helpdesk de TI porque os usuários são menos propensos a esquecer como fazer login. Quando as pessoas entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente no Active Directory local. |
| [Proxy de Aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan.md) |Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Eles precisam acessar aplicativos SaaS na nuvem e aplicativos corporativos no local. O proxy do Aplicativo Azure AD permite esse acesso robusto sem vpns (vpns) ou zonas desmilitarizadas (DMZs) de redes virtuais caras e complexas. |
| [SSO contínuo](../hybrid/how-to-connect-sso-quick-start.md)| O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Com esse recurso, os usuários não precisarão digitar suas senhas para fazer login no Azure AD e geralmente não precisarão inserir seus nomes de usuário. Esse recurso fornece aos usuários autorizados acesso fácil aos seus aplicativos baseados na nuvem sem precisar de componentes adicionais no local. |

## <a name="deploy-user-provisioning"></a>Implantar provisionamento de usuários

| Recurso | Descrição|
| -| -|
| [Provisionamento do usuário](../app-provisioning/plan-auto-user-provisioning.md)| O Azure AD ajuda a automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem (SaaS) como Dropbox, Salesforce, ServiceNow e outros. |
| [Provisionamento de usuários de RH na nuvem](../app-provisioning/plan-cloud-hr-provision.md)| O provisionamento de usuários de RH em nuvem para o Active Directory cria uma base para a governança contínua da identidade e melhora a qualidade dos processos de negócios que dependem de dados de identidade autorizados. Usando esse recurso com seu produto de RH na nuvem, como Workday ou Successfactors, você pode gerenciar perfeitamente o ciclo de vida de identidade de funcionários e funcionários contingentes, configurando regras que mapeiam processos do Joiner-Mover-Leaver (como Novo Contrato, Término, Transferência) para ações de provisionamento de TI (como Criar, Ativar, Desativar) |

## <a name="deploy-governance-and-reporting"></a>Implantar governança e relatórios

| Recurso | Descrição|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| O Azure AD Privileged Identity Management (PIM) ajuda você a gerenciar funções administrativas com privilégios entre o Azure AD, recursos do Azure e outros Serviços Online da Microsoft. O PIM fornece soluções como acesso just-in-time, solicitação de fluxos de trabalho de aprovação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e impedir atividades maliciosas de funções com privilégios em tempo real. |
| [Relatórios e Monitoramento](../reports-monitoring/plan-monitoring-and-reporting.md)| O projeto da solução de emissão e monitoramento de relatórios e monitoramento do Azure AD depende de seus requisitos legais, de segurança e operacionais, bem como do ambiente e dos processos existentes. Este artigo apresenta as várias opções de design e orienta você para a estratégia de implantação correta. |
