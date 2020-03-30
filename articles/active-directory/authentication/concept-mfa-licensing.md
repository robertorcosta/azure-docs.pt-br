---
title: Versões de autenticação multifatorial do Azure e planos de consumo
description: Conheça o cliente de autenticação multifatorial do Azure e diferentes métodos e versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647995"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Recursos e licenças para autenticação multifatorial do Azure

Para proteger as contas de usuário em sua organização, a autenticação multifatorial deve ser usada. Esse recurso é especialmente importante para contas que tenham acesso privilegiado a recursos. Os recursos básicos de autenticação multifatorial estão disponíveis para os administradores do Office 365 e do Azure Active Directory (Azure AD) sem custo adicional. Se você quiser atualizar os recursos para seus administradores ou estender a autenticação multifatorial para o resto de seus usuários, você pode comprar a Autenticação Multifatorial do Azure de várias maneiras.

> [!IMPORTANT]
> Este artigo detalha as diferentes maneiras pelas quais a Autenticação Multifatorial do Azure pode ser licenciada e usada. Para obter detalhes específicos sobre preços e faturamento, consulte a [página de preços de autenticação multifatorial do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multifator do Azure

A Autenticação Multifatorial do Azure pode ser usada e licenciada de algumas maneiras diferentes, dependendo das necessidades da sua organização. Você já pode ter o direito de usar a autenticação multifatorial do Azure, dependendo da licença Azure AD, Office 365, EMS ou Microsoft 365 que você possui atualmente. A tabela a seguir detalha as diferentes maneiras de obter a autenticação multifatorial do Azure e alguns dos recursos e casos de uso para cada um.

| Se você é um usuário de | Recursos e casos de uso |
| --- | --- |
| EMS ou Microsoft 365 E3 e E5 | Ems E3 ou Microsoft 365 E3 (que inclui EMS e Office 365), inclui Azure AD Premium P1. Ems E5 ou Microsoft 365 E5 inclui Azure AD Premium P2. Você pode usar os mesmos recursos de Acesso Condicional observados nas seções a seguir para fornecer autenticação multifatorial aos usuários. |
| Azure AD Premium P1 | Você pode usar [o Azure AD Conditional Access](../conditional-access/overview.md) para solicitar aos usuários a autenticação multifatorial durante determinados cenários ou eventos para atender às suas necessidades de negócios. |
| Azure AD Premium P2 | Fornece a posição de segurança mais forte e melhor experiência do usuário. Adiciona [acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos Azure AD Premium P1 que se adapta aos padrões do usuário e minimiza solicitações de autenticação de vários fatores. |
| Office 365 Business Premium, E3 ou E5 | A Autenticação Multifatorial do Azure está ativada ou desativada para todos os usuários, para todos os eventos de login. Não há capacidade de habilitar apenas autenticação multifatorial para um subconjunto de usuários, ou apenas em determinados cenários. A gestão é através do portal Office 365. Para uma melhor experiência do usuário, atualize para Azure AD Premium P1 ou P2 e use o Conditional Access. Para obter mais informações, consulte [recursos seguros do Office 365 com autenticação multifatorial](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD gratuito | Você pode usar [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para permitir a autenticação de vários fatores para todos os usuários, toda vez que uma solicitação de autenticação é feita. Você não tem controle granular de usuários ou cenários habilitados, mas fornece essa etapa adicional de segurança.<br /> Mesmo quando os padrões de segurança não são usados para permitir a autenticação de vários fatores para todos, os usuários atribuídos à função Administrador Global AD do *Azure* podem ser configurados para usar autenticação multifatorial. Esse recurso do nível livre garante que as contas de administrador crítico estejam protegidas por autenticação multifatorial. |

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões

A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure. Planeje suas necessidades para garantir a autenticação do usuário e determine qual abordagem atende a esses requisitos. Por exemplo, embora o Azure AD Free forneça padrões de segurança que fornecem autenticação multifatorial do Azure, apenas o aplicativo autenticador móvel pode ser usado para o prompt de autenticação, não uma chamada telefônica ou SMS. Essa abordagem pode ser uma limitação se você não puder garantir que o aplicativo de autenticação móvel esteja instalado no dispositivo pessoal do usuário.

| Recurso | Azure AD Free - Padrões de segurança | Azure AD Free - Administradores Globais azure AD | Office 365 Business Premium, E3 ou E5 | O Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteger contas de administrador de inquilinos azure AD com MFA | ● | ● ( Somente contas*azure AD Global Administrator)* | ● | ● |
| Aplicativos móveis como um fator secundário | ● | ● | ● | ● |
| Chamada telefônica como um fator secundário | | ● | ● | ● |
| SMS como um fator secundário | | ● | ● | ● |
| Controle do administrador sobre métodos de verificação | | ● | ● | ● |
| Alerta de fraude | | | | ● |
| Relatórios de MFA | | | | ● |
| Saudações personalizadas para chamadas telefônicas | | | | ● |
| ID do chamador personalizado para chamadas telefônicas | | | | ● |
| IPs confiáveis | | | | ● |
| Lembrar MFA para dispositivos confiáveis | | ● | ● | ● |
| MFA para aplicativos locais | | | | ● |

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamadas telefônicas não estão mais disponíveis para os usuários de autenticação multifatorial do Azure e reset de senha de autoatendimento do Azure no Azure AD Free / inquilinos de avaliação. As mensagens SMS não são impactadas por essa mudança. As chamadas telefônicas continuam disponíveis para os usuários em inquilinos Azure AD Premium P1 ou P2 ou usos ou Office 365 Business Premium, E3 ou E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Comprar e habilitar a autenticação multifatorial do Azure

Para usar a Autenticação Multifatorial do Azure, registre-se ou compre um nível AD azure elegível. O Azure AD vem em quatro edições — Edição gratuita, de aplicativos Office 365 (para clientes Office 365 Business Premium E3, ou E5), Premium P1 e Premium P2.

A edição Gratuita está incluída com uma assinatura do Azure. Consulte a [seção abaixo](#azure-ad-free-tier) para obter informações sobre como usar padrões de segurança ou proteger contas com a função Administrador Global AD do *Azure.*

As edições Azure AD Premium estão disponíveis através do seu representante da Microsoft, do [Open Volume License Program](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)e do programa Cloud Solution [Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Os assinantes do Azure e do Office 365 também podem comprar o Active Directory Premium P1 e P2 online. [Faça login](https://portal.office.com/Commerce/Catalog.aspx) para comprar.

> [!IMPORTANT]
> O licenciamento baseado em consumo não está mais disponível para novos clientes a partir de 1º de setembro de 2018. Os clientes existentes que usam o modelo baseado no consumo podem continuar a usar o faturamento por usuário habilitado ou por cobrança de autenticação.

Depois de ter comprado o nível AD do Azure necessário, [planeje e implante a Autenticação Multifatorial do Azure](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Nível Azure AD Free

Todos os usuários de um inquilino Azure AD Free podem usar a autenticação Multi-Factor do Azure através do uso de padrões de segurança. Esses padrões de segurança permitem a autenticação do Azure Multi-Factor para todos os usuários, sempre que eles fazem login. O aplicativo de autenticação móvel é o único método que pode ser usado para autenticação multifatorial do Azure ao usar padrões de segurança Azure AD Free.

* [Saiba mais sobre os padrões de segurança do Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Habilitar padrões de segurança para usuários no Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se você não quiser ativar a autenticação multifatorial do Azure para todos os usuários e todos os eventos de login, você pode optar por proteger apenas as contas de usuário com a função *Administrador Global do Azure AD.* Essa abordagem fornece solicitações adicionais de autenticação para contas de administrador esnocéias. Você habilita a Autenticação Multifatorial do Azure de uma das seguintes maneiras, dependendo do tipo de conta que você usa:

* Se você usar uma conta Microsoft, [registre-se para autenticação de vários fatores](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se você não estiver usando uma conta Microsoft, ative a [autenticação multifatorial para um usuário ou grupo no Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre custos, consulte [os preços de autenticação multifatorial do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
