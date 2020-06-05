---
title: Versões e planos de consumo da Autenticação Multifator do Azure
description: Saiba mais sobre o cliente de autenticação multifator do Azure e os diferentes métodos e as versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 165b84ee6b124d3f6a04c8db177ef17e32784ff9
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757408"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Recursos e Licenças para a Autenticação Multifator do Azure

Para proteger as contas de usuário em sua organização, a autenticação multifator deverá ser usada. Esse recurso é especialmente importante para contas que tenham acesso privilegiado a recursos. Os recursos básicos de autenticação multifator estão disponíveis para os administradores do Office 365 e Azure AD (Azure Active Directory) sem nenhum custo adicional. Se você quiser atualizar os recursos para os administradores ou estender a autenticação multifator para o restante dos usuários, poderá adquirir a Autenticação Multifator do Microsoft Azure de várias maneiras.

> [!IMPORTANT]
> Este artigo detalha as diferentes maneiras pelas quais a Autenticação Multifator do Microsoft Azure pode ser licenciada e usada. Para obter detalhes específicos sobre preços e cobrança, confira a [página de preços da Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multifator do Azure

A Autenticação Multifator do Azure pode ser usada e licenciada de algumas maneiras diferentes, dependendo das necessidades da sua organização. Talvez você já tenha direito a usar a autenticação Multifator do Azure dependendo da licença do Azure AD, Office 365, EMS ou Microsoft 365 que você tem atualmente. A tabela a seguir detalha as diferentes maneiras de obter a Autenticação Multifator do Azure e alguns dos recursos e casos de uso para cada um.

| Se você for usuário de | Recursos e casos de uso |
| --- | --- |
| EMS ou Microsoft 365 E3 e E5 | O EMS E3 ou Microsoft 365 E3 (que inclui o EMS e o Office 365), inclui Azure AD Premium P1. O EMS E5 ou Microsoft 365 E5 inclui Azure AD Premium P2. Você pode usar os mesmos recursos de acesso condicional observados nas seções a seguir para fornecer autenticação multifator aos usuários. |
| Azure AD Premium P1 | Você pode usar [Acesso condicional do Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) para solicitar aos usuários a autenticação multifator durante determinados cenários ou eventos para atender às suas necessidades de negócios. |
| Azure AD Premium P2 | Fornece a posição de segurança mais forte e a melhor experiência do usuário. Adiciona [Acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos do Azure AD Premium P1 que se adapta aos padrões do usuário e minimiza os prompts da autenticação multifator. |
| Office 365 Business Premium, E3 ou E5 | A autenticação multifator do Azure pode ser [habilitada por usuário](howto-mfa-userstates.md) ou habilitada ou desabilitada para todos os usuários, para todos os eventos de entrada, usando padrões de segurança. O gerenciamento da Autenticação Multifator do Azure ocorre por meio do portal do Office 365. Para ter uma experiência de usuário aprimorada, atualize para o Azure AD Premium P1 ou P2 e use o acesso condicional. Para saber mais, confira [proteger os recursos do Office 365 com autenticação multifator](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD gratuito | Você pode usar os [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator para todos os usuários, sempre que uma solicitação de autenticação for feita. Você não tem controle granular de usuários ou cenários habilitados, mas ela fornece essa etapa de segurança adicional.<br /> Mesmo quando os padrões de segurança não são usados para habilitar a autenticação multifator para todos, os usuários que atribuíram a função de *Administrador global do Azure AD* poderão ser configurados para usar a autenticação multifator. Esse recurso da camada gratuita garante que as contas de administrador críticas sejam protegidas pela autenticação multifator. |

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões

A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure. Planeje suas necessidades para proteger a autenticação do usuário e, em seguida, determine qual abordagem atende a esses requisitos. Por exemplo, embora o Azure AD Gratuito forneça padrões de segurança que forneçam a Autenticação Multifator do Azure, somente o aplicativo do autenticador móvel pode ser usado para o prompt de autenticação, não para uma chamada telefônica ou SMS. Essa abordagem pode ser uma limitação se você não puder garantir que o aplicativo de autenticação móvel esteja instalado no dispositivo pessoal de um usuário.

| Recurso | Azure AD Gratuito - padrões de segurança | Azure AD Gratuito - Administradores Globais do Azure AD | Office 365 Business Premium, E3 ou E5 | O Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteger contas de administrador de locatário do Azure AD com MFA | ● | ● (Apenas para contas de *Administrador Global do Azure AD*) | ● | ● |
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
> A partir de março de 2019, as opções de chamada telefônica não estão mais disponíveis para a Autenticação Multifator do Azure e usuários de redefinição de senha de autoatendimento do Azure em locatários de avaliação ou Azure AD Gratuito. As mensagens SMS não são afetadas por essa alteração. As chamadas telefônicas continuam disponíveis para os usuários nos locatários do Azure AD Premium P1 ou P2 ou Office 365 Business Premium, E3 ou E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Comprar e habilitar a Autenticação Multifator do Microsoft Azure

Para usar a Autenticação Multifator do Azure, inscreva-se ou compre uma camada qualificada do Azure AD. O Azure AD vem em quatro edições – gratuita, edições de aplicativo do Office 365 (para clientes do Office 365 Business Premium E3 ou E5), Premium P1 e Premium P2.

A edição gratuita é fornecida com uma assinatura do Azure. Confira a [seção abaixo](#azure-ad-free-tier) para saber mais sobre como usar padrões de segurança ou proteger contas com a função de *Administrador global do Azure AD*.

As edições do Azure AD Premium estão disponíveis por meio de um representante da Microsoft, o [Programa de Licenciamento Open Volume](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) e o [programa Provedores de Soluções de Nuvem](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Os assinantes do Azure e do Office 365 também podem comprar o Microsoft Azure Active Directory Premium P1 e P2 online. [Entrar](https://portal.office.com/Commerce/Catalog.aspx) para comprar.

> [!IMPORTANT]
> O licenciamento baseado em consumo não está mais disponível para novos clientes em vigor em 1 de setembro de 2018. Os clientes que usam o modelo baseado em consumo podem continuar a usar a cobrança por usuário habilitado ou por autenticação.

Depois de adquirir a camada necessária do Azure AD, [planeje e implante a Autenticação Multifator do Azure](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Camada gratuita do Azure AD

Todos os usuários em um locatário Azure AD Gratuito podem usar a autenticação multifator do Azure por meio do uso de padrões de segurança. Esses padrões de segurança habilitam a Autenticação Multifator do Azure para todos os usuários toda vez que entram. O aplicativo de autenticação móvel é o único método que pode ser usado para a Autenticação Multifator do Azure ao usar os padrões de segurança do Azure AD Gratuito.

* [Saiba mais sobre os padrões de segurança do Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Habilitar padrões de segurança para usuários no Azure AD Gratuito](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se você não quiser habilitar a Autenticação Multifator do Azure para todos os usuários e todos os eventos de entrada, poderá optar por apenas proteger as contas de usuário com a função de *Administrador global do Azure AD*. Essa abordagem fornece prompts de autenticação adicionais para contas de administrador críticas. Você habilita a Autenticação Multifator do Azure de uma das seguintes maneiras, dependendo do tipo de conta usada:

* Se usar uma Conta Microsoft, [inscreva-se na autenticação multifator](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se você não estiver usando uma Conta Microsoft, [ative a autenticação multifator para um usuário ou grupo no Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre custos, confira [Definir preços da Autenticação Multifator do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [O que é Acesso Condicional](../conditional-access/overview.md)

