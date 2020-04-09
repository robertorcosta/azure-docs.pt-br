---
title: Guia de referência de operações de gerenciamento de autenticação de diretório ativo do Azure
description: Este guia de referência de operações descreve as verificações e ações que você deve tomar para garantir o gerenciamento de autenticação
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: f25abb70a95f559cf0cc14efa6cf9f0e81ec9ec0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876285"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guia de referência de operações de gerenciamento de autenticação de diretório ativo do Azure

Esta seção do guia de referência de [operações do Azure AD](active-directory-ops-guide-intro.md) descreve as verificações e ações que você deve tomar para proteger e gerenciar credenciais, definir experiência de autenticação, delegar atribuição, medir o uso e definir políticas de acesso com base na postura de segurança da empresa.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar com o tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

O gerenciamento do Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de implantação. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Gerenciar o ciclo de vida da configuração de so (single sign-on) no Azure AD | Equipe de Operações IAM |
| Designe políticas de acesso condicional para aplicativos AD do Azure | Equipe de Arquitetura InfoSec |
| Arquive a atividade de login em um sistema SIEM | Equipe de Operações InfoSec |
| Arquive eventos de risco em um sistema SIEM | Equipe de Operações InfoSec |
| Trietrie e investigue relatórios de segurança | Equipe de Operações InfoSec |
| Triagem e investigação de eventos de risco | Equipe de Operações InfoSec |
| Triagem e investigação de usuários sinalizados para relatórios de risco e vulnerabilidade do Azure AD Identity Protection | Equipe de Operações InfoSec |

> [!NOTE]
> A Proteção de Identidade Azure AD requer uma licença Azure AD Premium P2. Para encontrar a licença certa para seus requisitos, consulte [Comparando recursos geralmente disponíveis das edições Azure AD Free e Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Ao revisar sua lista, você pode achar que precisa atribuir um proprietário para tarefas que estão faltando a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada do proprietário

- [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governança no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Gerenciamento de credenciais

### <a name="password-policies"></a>Políticas de senha

Gerenciar senhas com segurança é uma das partes mais críticas do gerenciamento de identidade e acesso e, muitas vezes, o maior alvo de ataques. O Azure AD suporta vários recursos que podem ajudar a evitar que um ataque seja bem sucedido.

Use a tabela abaixo para encontrar a solução recomendada para mitigar o problema que precisa ser resolvido:

| Problema | Recomendação |
| :- | :- |
| Nenhum mecanismo para proteger contra senhas fracas | Habilite a redefinição de senha de autoatendimento do Azure AD [(SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) e [proteção por senha](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Nenhum mecanismo para detectar senhas vazadas | Habilite o PHS [(Password Hash Sync, sincronização de hash)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) para obter insights |
| Usando AD FS e incapaz de se mover para autenticação gerenciada | Habilitar [o Bloqueio Inteligente AD FS Extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e/ou [o Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| A política de senha usa regras baseadas em complexidade, como comprimento, vários conjuntos de caracteres ou expiração | Reconsidere a favor das [Práticas Recomendadas](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) da Microsoft e mude sua abordagem para o gerenciamento de senhas e implante [a proteção por senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Os usuários não estão registrados para usar autenticação multifatorial (MFA) | [Registre todas as informações de segurança do usuário](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) para que ela possa ser usada como um mecanismo para verificar a identidade do usuário junto com sua senha |
| Não há revogação de senhas com base no risco do usuário | Implantar políticas de [risco de usuário do](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) Azure AD Identity Protection para forçar alterações de senha em credenciais vazadas usando SSPR |
| Não há mecanismo de bloqueio inteligente para proteger a autenticação maliciosa de maus atores provenientes de endereços IP identificados | Implantar autenticação gerenciada na nuvem com sincronização de hash de senha ou [autenticação de passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Políticas de senha recomendadas leitura

- [Azure AD e AD FS melhores práticas: Defendendo contra ataques de spray de senha - Mobilidade Corporativa + Segurança](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Habilite a redefinição de senha de autoatendimento e a proteção por senha

Os usuários que precisam alterar ou redefinir suas senhas é uma das maiores fontes de volume e custo de chamadas de help desk. Além do custo, alterar a senha como ferramenta para mitigar o risco de um usuário é um passo fundamental para melhorar a postura de segurança da sua organização.

No mínimo, recomenda-se que você implante o [Reset de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) do Azure AD e a [proteção](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) por senha no local para realizar:

- Desvie as chamadas de ajuda.
- Substitua o uso de senhas temporárias.
- Substitua qualquer solução de gerenciamento de senhade autoatendimento existente que dependa de uma solução no local.
- [Elimine senhas fracas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) em sua organização.

> [!NOTE]
> Para organizações com uma assinatura Azure AD Premium P2, recomenda-se implantar o SSPR e usá-lo como parte de uma Política de Risco do Usuário de [Proteção de Identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Forte gestão de credenciais

As senhas por si só não são seguras o suficiente para impedir que maus atores tenham acesso ao seu ambiente. No mínimo, qualquer usuário com uma conta privilegiada deve ser habilitado para autenticação multifatorial (MFA). Idealmente, você deve habilitar [o registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) e exigir que todos os usuários se registrem para MFA e SSPR usando a experiência de registro [combinada.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview) Eventualmente, recomendamos que você adote uma estratégia para [fornecer resiliência](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) para reduzir o risco de bloqueio devido a circunstâncias imprevistas.

![Fluxo combinado de experiência do usuário](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resiliência de autenticação de paralisação no local

Além dos benefícios da simplicidade e da habilitação da detecção de credenciais vazadas, o Azure AD Password Hash Sync (PHS) e o Azure MFA permitem que os usuários acessem aplicativos SaaS e o Office 365, apesar das paralisações no local devido a ataques cibernéticos como [o NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Também é possível habilitar o PHS em conjunto com a federação. A habilitação do PHS permite um recuo na autenticação quando os serviços da federação não estão disponíveis.

Se a sua organização local não está com uma estratégia de resiliência de paralisação ou tem uma que não esteja integrada ao Azure AD, você deve implantar o Azure AD PHS e definir um plano de recuperação de desastres que inclua PHS. A habilitação do Azure AD PHS permitirá que os usuários se autentiquem contra o Azure AD caso seu Diretório Ativo no local não esteja disponível.

![fluxo de sincronização hash senha](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Para entender melhor suas opções de autenticação, consulte [Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Uso programático de credenciais

Scripts Azure AD usando PowerShell ou aplicativos usando a API do Microsoft Graph exigem autenticação segura. O mau gerenciamento de credenciais executando esses scripts e ferramentas aumenta o risco de roubo de credenciais. Se você estiver usando scripts ou aplicativos que dependem de senhas ou senhas codificadas, você deve primeiro revisar senhas em arquivos config ou código-fonte, em seguida, substitua essas dependências e use Identidades Gerenciadas do Azure, Autenticação Integrada do Windows ou [certificados](../reports-monitoring/tutorial-access-api-with-certificates.md) sempre que possível. Para aplicações onde as soluções anteriores não são possíveis, considere usar [o Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Se você determinar que existem diretores de serviço com credenciais de senha e não tiver certeza de como essas credenciais de senha são protegidas por scripts ou aplicativos, entre em contato com o proprietário do aplicativo para entender melhor os padrões de uso.

A Microsoft também recomenda que você entre em contato com os proprietários de aplicativos para entender os padrões de uso se houver diretores de serviço com credenciais de senha.

## <a name="authentication-experience"></a>Experiência de autenticação

### <a name="on-premises-authentication"></a>Autenticação no local

Autenticação federada com autenticação integrada do Windows Authentication (IWA) ou SSO (SSO) gerenciada com sincronização de hash de senha ou autenticação de passagem é a melhor experiência do usuário quando dentro da rede corporativa com controladores de domínio de linha de visão para controladores de domínio no local. Ele minimiza a fadiga imediata da credencial e reduz o risco de os usuários serem vítimas de ataques de phishing. Se você já estiver usando autenticação gerenciada na nuvem com PHS ou PTA, mas os usuários ainda precisam digitar sua senha ao autenticar no local, então você deve implantar imediatamente [o SSO Sem Emendas](../hybrid/how-to-connect-sso.md). Por outro lado, se você está atualmente federado com planos para eventualmente migrar para autenticação gerenciada em nuvem, então você deve implementar o SSO sem emendas como parte do projeto de migração.

### <a name="device-trust-access-policies"></a>Políticas de acesso à confiança de dispositivos

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local.Autenticar o dispositivo e contabilizar seu tipo de confiança melhora sua postura de segurança e usabilidade:

- Evitando atritos, por exemplo, com MFA, quando o dispositivo é confiável
- Bloqueando o acesso de dispositivos não confiáveis
- Para dispositivos Com Windows 10, forneça [o login único nos recursos locais perfeitamente](../devices/azuread-join-sso.md).

Você pode realizar esse objetivo trazendo identidades de dispositivos e gerenciando-as no Azure AD usando um dos seguintes métodos:

- As organizações podem usar o [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) para gerenciar o dispositivo e aplicar políticas de conformidade, atestar a saúde do dispositivo e definir políticas de acesso condicional com base em se o dispositivo está em conformidade. O Microsoft Intune pode gerenciar dispositivos iOS, desktops Mac (integração Via JAMF), desktops Windows (usando nativamente o Gerenciamento de Dispositivos Móveis para Windows 10 e co-gerenciamento com o Microsoft Endpoint Configuration Manager) e dispositivos móveis Android.
- [O Ad do Hybrid Azure](../devices/hybrid-azuread-join-managed-domains.md) fornece gerenciamento com políticas de grupo ou gerenciador de configuração de ponto final do Microsoft em um ambiente com dispositivos de computador escaneados pelo domínio do Active Directory. As organizações podem implantar um ambiente gerenciado através do PHS ou do PTA com SSO sem emendas. Trazer seus dispositivos para o Azure AD maximiza a produtividade do usuário através do SSO através dos recursos de nuvem e local, permitindo que você garanta acesso seguro aos recursos da nuvem e on-premises com o [Conditional Access](../conditional-access/overview.md) ao mesmo tempo.

Se você tiver dispositivos Windows com domínio que não estão registrados na nuvem ou dispositivos Windows com domínio registrados na nuvem, mas sem políticas de acesso condicional, então você deve registrar os dispositivos não registrados e, em ambos os casos, usar o [Hybrid Azure AD como um controle](../conditional-access/require-managed-devices.md) em suas políticas de acesso condicional.

![Uma captura de tela de concessão na política de acesso condicional que exige dispositivo híbrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se você está gerenciando dispositivos com MDM ou Microsoft Intune, mas não usando controles de dispositivos em suas políticas de acesso condicional, então recomendamos que [o uso do dispositivo seja marcado como compatível](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) como um controle nessas políticas.

![Uma captura de tela de concessão na política de acesso condicional que exige a conformidade do dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Políticas de acesso de confiança de dispositivos recomendadas leitura

- [Como: Planeje seu Diretório Ativo azure híbrido aderir à implementação](../devices/hybrid-azuread-join-plan.md)
- [Configurações de acesso à identidade e ao dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

No Windows 10, [o Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) substitui senhas por forte autenticação de dois fatores em PCs. O Windows Hello for Business permite uma experiência MFA mais simplificada para os usuários e reduz sua dependência de senhas. Se você ainda não começou a lançar dispositivos Windows 10, ou apenas parcialmente implantá-los, recomendamos que você atualize para o Windows 10 e [habilite o Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) em todos os dispositivos.

Se você quiser saber mais sobre autenticação sem senha, consulte [Um mundo sem senhas com o Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Autenticação e atribuição de aplicativos

### <a name="single-sign-on-for-apps"></a>Único login para aplicativos

Fornecer um mecanismo de sinalização único padronizado para toda a empresa é crucial para a melhor experiência do usuário, redução de risco, capacidade de relatório e governança. Se você estiver usando aplicativos que suportam SSO com o Azure AD, mas estão configurados atualmente para usar contas locais, você deve reconfigurar esses aplicativos para usar o SSO com o Azure AD. Da mesma forma, se você estiver usando quaisquer aplicativos que suportam SSO com o Azure AD, mas estiver usando outro Provedor de Identidade, você deve reconfigurar esses aplicativos para usar o SSO com o Azure AD também. Para aplicativos que não suportam protocolos da federação, mas suportam autenticação baseada em formulários, recomendamos que você configure o aplicativo para usar [o cofre de senhas](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) com o Azure AD Application Proxy.

![Login baseado em senha do AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se você não tiver um mecanismo para descobrir aplicativos não gerenciados em sua organização, recomendamos implementar um processo de detecção usando uma solução de corretor de segurança de acesso à nuvem (CASB), como [o Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Finalmente, se você tem uma galeria de aplicativos Azure AD e usa aplicativos que suportam SSO com Azure AD, recomendamos [listar o aplicativo na galeria de aplicativos](../azuread-dev/howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Leitura recomendada de sinal único

- [O que é acesso ao aplicativo e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migração de aplicativos AD FS para Azure AD

[A migração de aplicativos do AD FS para o Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) permite recursos adicionais em segurança, gerenciabilidade mais consistente e uma melhor experiência de colaboração. Se você tiver aplicativos configurados no AD FS que suportam SSO com o Azure AD, então você deve reconfigurar esses aplicativos para usar o SSO com o Azure AD. Se você tiver aplicativos configurados no AD FS com configurações incomuns não suportadas pelo Azure AD, você deve entrar em contato com os proprietários do aplicativo para entender se a configuração especial é um requisito absoluto do aplicativo. Se não for necessário, então você deve reconfigurar o aplicativo para usar o SSO com o Azure AD.

![Azure AD como o principal provedor de identidade](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [O Azure AD Connect Health for ADFS](../hybrid/how-to-connect-health-adfs.md) pode ser usado para coletar detalhes de configuração sobre cada aplicativo que podem ser potencialmente migrados para o Azure AD.

### <a name="assign-users-to-applications"></a>Atribuir usuários a aplicativos

[Atribuir usuários a aplicativos](../manage-apps/assign-user-or-group-access-portal.md) é melhor mapeado usando grupos porque eles permitem maior flexibilidade e capacidade de gerenciar em escala. Os benefícios do uso de grupos incluem [a adesão de grupo dinâmico baseado em atributos](../users-groups-roles/groups-dynamic-membership.md) e [delegação aos proprietários de aplicativos](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Portanto, se você já está usando e gerenciando grupos, recomendamos que você tome as seguintes ações para melhorar a gestão em escala:

- Delegar gerenciamento de grupo e governança para proprietários de aplicativos.
- Permitir acesso de autoatendimento ao aplicativo.
- Defina grupos dinâmicos se os atributos do usuário puderem determinar consistentemente o acesso aos aplicativos.
- Implementar atestado para grupos usados para acesso a aplicativos usando [avaliações de acesso a Azure AD](../governance/access-reviews-overview.md).

Por outro lado, se você encontrar aplicativos que tenham atribuição a usuários individuais, certifique-se de implementar [a governança](https://docs.microsoft.com/azure/active-directory/governance/index) em torno desses aplicativos.

#### <a name="assign-users-to-applications-recommended-reading"></a>Atribuir usuários a aplicativos recomendados de leitura

- [Atribuir usuários e grupos a um aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegue permissões de registro de aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Associação dinâmica do Azure Active Directory para grupos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Políticas de acesso

### <a name="named-locations"></a>Localizações nomeadas

Com [localizações nomeadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) no Azure AD, você pode rotular faixas de endereçoIP confiáveis em sua organização. O Azure AD usa localizações nomeadas para:

- Evite falsos positivos em eventos de risco. Fazer login a partir de um local de rede confiável reduz o risco de login de um usuário.
- Configurar [o Acesso Condicional baseado em localização](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Local nomeado](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Com base na prioridade, use a tabela abaixo para encontrar a solução recomendada que melhor atenda às necessidades da sua organização:

| **Priority** | **Cenário** | **Recomendação** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se você usa PHS ou PTA e locais nomeados não foram definidos | Defina locais nomeados para melhorar a detecção de eventos de risco |
| 2 | Se você é federado e não usa a reivindicação "insideCorporateNetwork" e os locais nomeados não foram definidos | Defina locais nomeados para melhorar a detecção de eventos de risco |
| 3 | Se você não usar locais nomeados em políticas de acesso condicional e não houver riscos ou controles de dispositivos em políticas de acesso condicional | Configure a política de acesso condicional para incluir locais nomeados |
| 4 | Se você é federado e usa a reivindicação "insideCorporateNetwork" e os locais nomeados não foram definidos | Defina locais nomeados para melhorar a detecção de eventos de risco |
| 5 | Se você estiver usando endereços IP confiáveis com MFA em vez de locais nomeados e marcando-os como confiáveis | Defina locais nomeados e marque-os como confiáveis para melhorar a detecção de eventos de risco |

### <a name="risk-based-access-policies"></a>Políticas de acesso baseadas em riscos

O Azure AD pode calcular o risco para cada login e cada usuário. O uso do risco como critério nas políticas de acesso pode proporcionar uma melhor experiência ao usuário, por exemplo, menos solicitações de autenticação e melhor segurança, por exemplo, apenas solicitar aos usuários quando eles são necessários e automatizar a resposta e a correção.

![Política de risco de entrada](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se você já possui licenças Azure AD Premium P2 que suportam o uso de risco em políticas de acesso, mas elas não estão sendo usadas, recomendamos adicionar risco à sua postura de segurança.

#### <a name="risk-based-access-policies-recommended-reading"></a>Políticas de acesso baseadas em riscos recomendaram leitura

- [Como: configurar a política de risco de entrada](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Como configurar a política de risco do usuário](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Políticas de acesso a aplicativos de cliente

O Microsoft Intune Application Management (MAM) oferece a capacidade de empurrar controles de proteção de dados, como criptografia de armazenamento, PIN, limpeza de armazenamento remoto, etc. para aplicativos móveis compatíveis do cliente, como o Outlook Mobile. Além disso, políticas de acesso condicional podem ser criadas para [restringir o acesso](../conditional-access/app-based-conditional-access.md) a serviços em nuvem, como o Exchange Online, de aplicativos aprovados ou compatíveis.

Se seus funcionários instalarem aplicativos capazes de MAM, como aplicativos móveis do Office, para acessar recursos corporativos, como o Exchange Online ou o SharePoint Online, e você também suportar o BYOD (traga seu próprio dispositivo), recomendamos que você implante políticas mam para gerenciar a configuração do aplicativo em dispositivos de propriedade pessoal sem o registro de MDM e, em seguida, atualize suas políticas de acesso condicional para permitir apenas o acesso de clientes capazes de MAM.

![Controle de concessão de acesso condicional](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se os funcionários instalarem aplicativos capazes de MAM contra recursos corporativos e o acesso for restrito em dispositivos Gerenciados intune, então você deve considerar a implantação de políticas mam de aplicativos para gerenciar a configuração do aplicativo para dispositivos pessoais e atualizar políticas de acesso condicional para permitir apenas o acesso de clientes capazes de MAM.

### <a name="conditional-access-implementation"></a>Implementação do Acesso Condicional

O Acesso Condicional é uma ferramenta essencial para melhorar a postura de segurança da sua organização. Portanto, é importante que você siga estas melhores práticas:

- Certifique-se de que todos os aplicativos SaaS tenham pelo menos uma política aplicada
- Evite combinar o filtro **All apps** com o controle de **bloco** para evitar o risco de bloqueio
- Evite usar todos os **usuários** como filtro e adicionar inadvertidamente **convidados**
- **Migre todas as políticas de "legado" para o portal Azure**
- Veja todos os critérios para usuários, dispositivos e aplicativos
- Use políticas de acesso condicional para [implementar o MFA,](../conditional-access/plan-conditional-access.md)em vez de usar um **MFA por usuário**
- Tenha um pequeno conjunto de políticas principais que podem se aplicar a vários aplicativos
- Defina grupos de exceção vazios e adicione-os às políticas para ter uma estratégia de exceção
- Plano para quebrar contas [de vidro](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) sem controles MFA
- Garantir uma experiência consistente entre os aplicativos clientes do Office 365, por exemplo, Teams, OneDrive for Business, Outlook, etc.) implementando o mesmo conjunto de controles para serviços como Exchange Online e Sharepoint Online
- A atribuição de políticas deve ser implementada através de grupos, não indivíduos
- Faça revisões regulares dos grupos de exceção usados em políticas para limitar o tempo que os usuários estão fora da postura de segurança. Se você possui o Azure AD P2, então você pode usar avaliações de acesso para automatizar o processo

#### <a name="conditional-access-recommended-reading"></a>Leitura recomendada de Acesso Condicional

- [Melhores práticas para acesso condicional no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Configurações de acesso à identidade e ao dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referência às configurações de acesso condicionado do diretório ativo do Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Políticas de Acesso Condicional Comum](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Área de acesso à superfície

### <a name="legacy-authentication"></a>Autenticação herdada

Credenciais fortes como o MFA não podem proteger aplicativos usando protocolos de autenticação legado, o que o torna o vetor de ataque preferido por atores mal-intencionados. Bloquear a autenticação do legado é crucial para melhorar a postura de segurança de acesso.

Autenticação legado é um termo que se refere aos protocolos de autenticação usados por aplicativos como:

- Clientes antigos do Office que não usam autenticação moderna (por exemplo, cliente Office 2010)
- Clientes que usam protocolos de e-mail como IMAP/SMTP/POP

Os atacantes preferem fortemente esses protocolos - na verdade, quase [100% dos ataques de spray de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) usam protocolos de autenticação legado! Os hackers usam protocolos de autenticação legados, porque eles não suportam login interativo, o que é necessário para desafios adicionais de segurança, como autenticação multifatorial e autenticação de dispositivos.

Se a autenticação do legado for amplamente utilizada em seu ambiente, você deve planejar migrar clientes legados para clientes que suportam [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) o mais rápido possível. No mesmo token, se você já tem alguns usuários usando autenticação moderna, mas outros que ainda usam autenticação herdada, você deve tomar as seguintes etapas para bloquear clientes de autenticação legado:

1. Use [relatórios de atividade de login](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) para identificar usuários que ainda estão usando autenticação e remediação de planos legados:

   a. Atualize para clientes modernos capazes de autenticação para usuários afetados.
   
   b. Planeje um prazo de corte para bloquear por etapas abaixo.
   
   c. Identifique quais aplicativos legados têm uma forte dependência da autenticação do legado. Veja o passo 3 abaixo.

2. Desative protocolos legados na fonte (por exemplo, Exchange Mailbox) para usuários que não estão usando auth legado para evitar mais exposição.
3. Para as contas restantes (idealmente identidades não humanas, como contas de serviço), use [acesso condicional para restringir protocolos legados](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) após a autenticação.

#### <a name="legacy-authentication-recommended-reading"></a>Autenticação herdada recomendada leitura

- [Habilite ou desative o acesso POP3 ou IMAP4 a caixas de correio no Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Subvenções de consentimento

Em um ataque de concessão de consentimento ilícito, o invasor cria um aplicativo registrado no Azure AD que solicita acesso a dados como informações de contato, e-mail ou documentos. Os usuários podem estar concedendo consentimento a aplicativos maliciosos através de ataques de phishing ao pousar em sites maliciosos.

Abaixo estão uma lista de aplicativos com permissões que você pode querer examinar para serviços de nuvem da Microsoft:

- Aplicativos com aplicativo \*ou delegados . Permissões readWrite
- Aplicativos com permissões delegadas podem ler, enviar ou gerenciar e-mails em nome do usuário
- Aplicativos que recebem o uso das seguintes permissões:

| Recurso | Permissão |
| :- | :- |
| Office 365 Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| API do Microsoft Graph | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Os aplicativos concederam a personificação total do usuário do usuário inscrito. Por exemplo:

|Recurso | Permissão |
| :- | :- |
| API do Microsoft Graph| Directory.AccessAsUser.All |
| API REST do Azure | user_impersonation |

Para evitar esse cenário, você deve consultar [e remediar subvenções de consentimento ilícito no Office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) para identificar e corrigir quaisquer aplicações com subvenções ou aplicações ilícitas que tenham mais subsídios do que o necessário. Em seguida, [remova completamente o autoatendimento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) e [estabeleça procedimentos de governança.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) Finalmente, agende revisões regulares das permissões de aplicativos e remova-as quando elas não forem necessárias.

#### <a name="consent-grants-recommended-reading"></a>Bolsas de consentimento recomendam leitura

- [Permissões de API do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Configurações de usuário e grupo

Abaixo estão as configurações de usuário e grupo que podem ser bloqueadas se não houver uma necessidade explícita de negócios:

#### <a name="user-settings"></a>Configurações do usuário

- **Usuários externos** - a colaboração externa pode acontecer organicamente na empresa com serviços como Teams, Power BI, Sharepoint Online e Azure Information Protection. Se você tiver restrições explícitas para controlar a colaboração externa iniciada pelo usuário, é recomendável que você habilite usuários externos usando [o gerenciamento de direitos AD do Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) ou uma operação controlada, como através de sua help desk. Se você não quiser permitir colaboração externa orgânica para serviços, você pode [bloquear os membros de convidar usuários externos completamente](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Alternativamente, você também pode [permitir ou bloquear domínios específicos](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) em convites de usuários externos.
- **Registros de aplicativos** - quando os registros do Aplicativo são habilitados, os usuários finais podem embarcar nos próprios aplicativos e conceder acesso aos seus dados. Um exemplo típico de registro de aplicativos é os usuários que habilitam plug-ins do Outlook ou assistentes de voz, como Alexa e Siri, para ler seus e-mails e calendário ou enviar e-mails em seu nome. Se o cliente decidir desativar o registro do Aplicativo, as equipes infosec e IAM devem estar envolvidas na gestão de exceções (registros de aplicativos que são necessários com base nos requisitos do negócio), pois eles precisariam registrar os aplicativos com uma conta de administrador, e provavelmente requerem o projeto de um processo para operacionalizar o processo.
- **Portal de Administração** - as organizações podem bloquear a lâmina Azure AD no portal Azure para que os não-administradores não possam acessar o gerenciamento do Azure AD no portal Azure e ficar confusos. Vá às configurações do usuário no portal de gerenciamento do Azure AD para restringir o acesso:

![Portal de administração acesso restrito](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Os não administradores ainda podem acessar as interfaces de gerenciamento do Azure AD via linha de comando e outras interfaces programáticas.

#### <a name="group-settings"></a>Configurações de grupo

**O Gerenciamento de Grupos de Autoatendimento / Usuários podem criar grupos de segurança / grupos O365.** Se não houver uma iniciativa atual de autoatendimento para grupos na nuvem, os clientes podem decidir desligá-lo até que estejam prontos para usar esse recurso.

#### <a name="groups-recommended-reading"></a>Grupos recomendaram leitura

- [O que é a colaboração B2B do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrando aplicativos com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Aplicativos, permissões e consentimento no Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Use grupos para gerenciar o acesso aos recursos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Configuração do gerenciamento de acesso a aplicativos de autoatendimento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Tráfego de locais inesperados

Atacantes são originários de várias partes do mundo. Gerencie esse risco usando políticas de acesso condicional com a localização como condição. A condição de [localização](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) de uma política de acesso condicional permite bloquear o acesso para locais de onde não há razão comercial para fazer login.

![Criar um novo local nomeado](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponível, use uma solução de gerenciamento de informações de segurança e eventos (SIEM) para analisar e encontrar padrões de acesso entre as regiões. Se você não usar um produto SIEM ou não ingerir informações de autenticação do Azure AD, recomendamos que você use [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para identificar padrões de acesso entre regiões.

## <a name="access-usage"></a>Uso de acesso

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Logs Azure AD arquivados e integrados com planos de resposta a incidentes

Ter acesso a atividades de login, auditorias e eventos de risco para o Azure AD é crucial para a solução de problemas, análises de uso e investigações forenses. O Azure AD fornece acesso a essas fontes através de APIs REST que têm um período de retenção limitado. Um sistema de gerenciamento de informações de segurança e eventos (SIEM), ou tecnologia de arquivamento equivalente, é fundamental para o armazenamento a longo prazo de auditorias e suporte. Para habilitar o armazenamento a longo prazo do Azure AD Logs, você deve adicioná-los à solução SIEM existente ou usar [o Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Registros de arquivos que podem ser usados como parte de seus planos e investigações de resposta a incidentes.

#### <a name="logs-recommended-reading"></a>Logs recomendados de leitura

- [Referência à API de auditoria do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Referência da API de relatório de atividade de entrada do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Obter dados usando a API de Relatório do Azure AD com certificados](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Gráfico microsoft para proteção de identidade do diretório ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Referência em API de atividade de gerenciamento do Office 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Como usar o Pacote de Conteúdo do Power BI do Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Resumo

Há 12 aspectos para uma infra-estrutura de identidade segura. Essa lista ajudará você a proteger e gerenciar credenciais, definir a experiência de autenticação, delegar atribuição, medir o uso e definir políticas de acesso com base na postura de segurança corporativa.

- Designe proprietários para tarefas-chave.
- Implementar soluções para detectar senhas fracas ou vazadas, melhorar o gerenciamento e a proteção de senhas e garantir ainda mais o acesso do usuário aos recursos.
- Gerencie a identidade dos dispositivos para proteger seus recursos a qualquer momento e de qualquer local.
- Implementar autenticação sem senha.
- Forneça um mecanismo de sinalização único padronizado em toda a organização.
- Migre os aplicativos do AD FS para o Azure AD para permitir uma melhor segurança e uma capacidade de gerenciamento mais consistente.
- Atribuir usuários a aplicativos usando grupos para permitir maior flexibilidade e capacidade de gerenciar em escala.
- Configure políticas de acesso baseadas em riscos.
- Bloqueie protocolos de autenticação legado.
- Detectar e remediar concessões de consentimento ilícito.
- Bloqueie as configurações do usuário e do grupo.
- Habilite o armazenamento a longo prazo de logs AD do Azure para solução de problemas, análise de uso e investigações forenses.

## <a name="next-steps"></a>Próximas etapas

Comece com as [verificações e ações operacionais de governança de identidade.](active-directory-ops-guide-govern.md)
