---
title: Planos de consumo e versões da autenticação multifator do Azure
description: Saiba mais sobre o cliente de autenticação multifator do Azure e diferentes métodos e versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0d37b0cd042e6e11f8b2029b51c589c96154065
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909084"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Recursos e licenças para a autenticação multifator do Azure

Para proteger as contas de usuário em sua organização, a autenticação multifator deve ser usada. Esse recurso é especialmente importante para contas que tenham acesso privilegiado a recursos. Os recursos básicos da autenticação multifator estão disponíveis para os administradores do Office 365 e do Azure Active Directory (Azure AD) sem custo adicional. Se você quiser atualizar os recursos para seus administradores ou estender a autenticação multifator para o restante de seus usuários, poderá comprar a autenticação multifator do Azure de várias maneiras.

> [!IMPORTANT]
> Este artigo detalha as diferentes maneiras pelas quais a autenticação multifator do Azure pode ser licenciada e usada. Para obter detalhes específicos sobre preços e cobrança, consulte a [página de preços da autenticação multifator do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multifator do Azure

A autenticação multifator do Azure pode ser usada e licenciada, de algumas maneiras diferentes, dependendo das necessidades da sua organização. Talvez você já tenha direito a usar a autenticação multifator do Azure dependendo do Azure AD, Office 365, EMS ou Microsoft 365 licença que você tem atualmente. A tabela a seguir detalha as diferentes maneiras de obter a autenticação multifator do Azure e alguns dos recursos e casos de uso para cada um.

| Se você for um usuário do | Recursos e casos de uso |
| --- | --- |
| EMS ou Microsoft 365 E3 e e5 | O EMS E3 ou o Microsoft 365 E3 (que inclui o EMS e o Office 365), inclui Azure AD Premium P1. O EMS E5 ou Microsoft 365 E5 inclui Azure AD Premium P2. Você pode usar os mesmos recursos de acesso condicional observados nas seções a seguir para fornecer autenticação multifator aos usuários. |
| Azure AD Premium P1 | Você pode usar o [acesso condicional do Azure ad](../conditional-access/overview.md) para solicitar aos usuários a autenticação multifator durante determinados cenários ou eventos para atender às suas necessidades de negócios. |
| Azure AD Premium P2 | Fornece a posição de segurança mais forte e a melhor experiência do usuário. Adiciona [acesso condicional com base em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos Azure ad Premium P1 que se adapta aos padrões do usuário e minimiza os prompts de autenticação multifator. |
| Office Premium, E3 ou e5 | A autenticação multifator do Azure está habilitada ou desabilitada para todos os usuários, para todos os eventos de entrada. Não há capacidade de habilitar a autenticação multifator para um subconjunto de usuários ou somente em determinados cenários. O gerenciamento é por meio do portal do Office 365. Para uma experiência de usuário aprimorada, atualize para Azure AD Premium P1 ou P2 e use o acesso condicional. Para obter mais informações, consulte [proteger recursos do Office 365 com a autenticação multifator](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD gratuito | Você pode usar [os padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator para todos os usuários, sempre que uma solicitação de autenticação for feita. Você não tem controle granular de usuários ou cenários habilitados, mas fornece essa etapa de segurança adicional.<br /> Mesmo quando os padrões de segurança não são usados para habilitar a autenticação multifator para todos, os usuários atribuídos à função de *administrador global do Azure ad* podem ser configurados para usar a autenticação multifator. Esse recurso da camada gratuita garante que as contas de administrador críticas sejam protegidas pela autenticação multifator. |

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões

A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure. Planeje suas necessidades para proteger a autenticação do usuário e, em seguida, determine qual abordagem atende a esses requisitos. Por exemplo, embora Azure AD Gratuito forneça padrões de segurança que forneçam a autenticação multifator do Azure, somente o aplicativo do autenticador móvel pode ser usado para o prompt de autenticação, não para uma chamada telefônica ou SMS. Essa abordagem pode ser uma limitação se você não puder garantir que o aplicativo de autenticação móvel esteja instalado no dispositivo pessoal de um usuário.

| Recurso | Azure AD Gratuito-padrões de segurança | Azure AD Gratuito-administradores globais do Azure AD | Office Premium, E3 ou e5 | O Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteger contas de administrador do Azure AD com MFA | ● | ● (Somente contas de*administrador global do Azure ad* ) | ● | ● |
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
> A partir de março de 2019, as opções de chamada telefônica não estão mais disponíveis para a autenticação multifator do Azure e usuários de redefinição de senha de autoatendimento do Azure em locatários de Azure AD Gratuito/avaliação. As mensagens SMS não são afetadas por essa alteração. As chamadas telefônicas continuam disponíveis para os usuários em Azure AD Premium locatários P1 ou P2 ou usa o Office Premium, E3 ou e5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Comprar e habilitar a autenticação multifator do Azure

Para usar a autenticação multifator do Azure, registre-se ou compre uma camada qualificada do Azure AD. O Azure AD vem em quatro edições: gratuita, Office 365 apps Edition (para clientes do Office 365 Premium E3 ou E5), Premium P1 e Premium P2.

A edição Gratuita é incluída com uma assinatura do Azure. Consulte a [seção abaixo](#azure-ad-free-tier) para obter informações sobre como usar padrões de segurança ou proteger contas com a função de *administrador global do Azure ad* .

As edições Azure AD Premium estão disponíveis por meio de seu representante da Microsoft, do [programa Open Volume License](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)e do [programa provedores de soluções na nuvem](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Assinantes do Azure e do Office 365 também podem comprar o Azure Active Directory Premium P1 e P2 online. [Entre](https://portal.office.com/Commerce/Catalog.aspx) para comprar.

> [!IMPORTANT]
> O licenciamento baseado em consumo não estará mais disponível para novos clientes a partir de 1º de setembro de 2018. Os clientes existentes que usam o modelo baseado em consumo podem continuar a usar cada usuário habilitado ou cobrança por autenticação.

Depois de adquirir a camada necessária do Azure AD, [planeje e implante a autenticação multifator do Azure](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Camada de Azure AD Gratuito

Todos os usuários em um locatário Azure AD Gratuito podem usar a autenticação multifator do Azure por meio do uso de padrões de segurança. Esses padrões de segurança habilitam a autenticação multifator do Azure para todos os usuários, toda vez que entram. O aplicativo de autenticação móvel é o único método que pode ser usado para a autenticação multifator do Azure ao usar os padrões de segurança Azure AD Gratuito.

* [Saiba mais sobre os padrões de segurança do Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Habilitar padrões de segurança para usuários em Azure AD Gratuito](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se você não quiser habilitar a autenticação multifator do Azure para todos os usuários e todos os eventos de entrada, poderá optar por apenas proteger as contas de usuário com a função de *administrador global do Azure ad* . Essa abordagem fornece prompts de autenticação adicionais para contas de administrador críticas. Você habilita a autenticação multifator do Azure de uma das seguintes maneiras, dependendo do tipo de conta usada:

* Se você usar uma conta da Microsoft, [Registre-se para autenticação multifator](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se você não estiver usando uma conta da Microsoft, [ative a autenticação multifator para um usuário ou grupo no Azure ad](howto-mfa-userstates.md).

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os custos, consulte [preços da autenticação multifator do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
