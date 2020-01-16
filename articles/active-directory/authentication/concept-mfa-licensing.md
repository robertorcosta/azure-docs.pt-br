---
title: Planos de consumo e versões do Azure MFA-Azure Active Directory
description: Informações sobre o cliente de autenticação multifator e os diferentes métodos e as versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1528bffc613d2e8ab2c0150095d90791b649198a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979494"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Como obter a Autenticação Multifator do Azure

Quando se trata de proteger suas contas, verificação em duas etapas deve ser padrão em sua organização. Esse recurso é especialmente importante para contas que tenham acesso privilegiado a recursos. Por esse motivo, a Microsoft oferece recursos básicos de verificação em duas etapas aos administradores do Office 365 e Azure AD (Azure Active Directory) sem nenhum custo adicional. Se você quiser atualizar os recursos para os administradores ou estender a verificação em duas etapas para o restante dos usuários, poderá adquirir a Autenticação Multifator do Microsoft Azure de várias maneiras.

> [!IMPORTANT]
> Este artigo deve ser um guia para ajudá-lo a entender as diferentes maneiras de comprar autenticação multifator do Azure. Para obter detalhes específicos sobre preços e cobrança, você deve sempre consultar a [página de preços da Autenticação Multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multifator do Azure

A tabela a seguir descreve as diferenças entre as versões de autenticação multifator:

| Versão | Description |
| --- | --- |
| Opção gratuita | Os clientes que estão utilizando os benefícios gratuitos do Azure AD podem usar os [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator em seu ambiente. |
| Autenticação Multifator para Office 365 | Esta versão é gerenciada no portal do Office 365 ou Microsoft 365. Os administradores podem [proteger recursos do Office 365 com a verificação de duas etapas](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Esta versão faz parte de uma assinatura do Office 365. |
| Autenticação Multifator para Administradores do Azure AD | Os usuários atribuídos à função Administrador global do Azure AD nos locatários do Azure AD podem habilitar a verificação em duas etapas sem custo adicional. |
| Autenticação Multifator do Azure | Geralmente conhecida como a versão "completa", a Autenticação Multifator do Azure oferece o conjunto mais avançado de recursos. Ela fornece opções de configuração adicionais por meio do [portal do Azure](https://portal.azure.com), relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. A autenticação multifator do Azure é um recurso de [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) e [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> Novos clientes não poderão mais comprar Autenticação Multifator do Microsoft Azure como uma oferta independente a partir de 1º de setembro de 2018. A autenticação multifator continuará disponível como um recurso em licenças Azure AD Premium ou Microsoft 365 Business.

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões

A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure.

> [!NOTE]
> Esta tabela de comparação aborda os recursos que fazem parte de cada versão da Autenticação Multifator. Se você tiver o serviço completo da Autenticação Multifator do Azure, alguns recursos podem não estar disponíveis caso você use [MFA na nuvem ou MFA no local](concept-mfa-whichversion.md).
>

| Recurso | Autenticação Multifator para Office 365 | Autenticação Multifator para Administradores do Azure AD | Autenticação Multifator do Azure | Padrões de segurança |
| --- |:---:|:---:|:---:|:---:|
| Proteger contas de administrador do Azure AD com MFA |● |● (Apenas para contas de Administrador Global do Azure AD) |● |● |
| Aplicativos móveis como um fator secundário |● |● |● |● |
| Chamada telefônica como um fator secundário |● |● |● |   |
| SMS como um fator secundário |● |● |● |   |
| Senhas de aplicativos para clientes que não oferecem suporte a MFA |● |● |● |   |
| Controle do administrador sobre métodos de verificação |● |● |● |   |
| Proteger contas que não são de administrador com MFA |● | |● |● |
| Modo PIN | | |● |   |
| Alerta de fraude | | |● |   |
| Relatórios de MFA | | |● |   |
| Bypass avulso | | |● |   |
| Saudações personalizadas para chamadas telefônicas | | |● |   |
| ID do chamador personalizado para chamadas telefônicas | | |● |   |
| IPs confiáveis | | |● |   |
| Lembrar MFA para dispositivos confiáveis |● |● |● |   |
| MFA para aplicativos locais | | |● |   |

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para os usuários de MFA e SSPR em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários gratuitos/de avaliação do Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Como ativar Autenticação Multifator do Azure para administradores do Azure AD

Os usuários atribuídos à função de administrador Global nos locatários do Azure AD podem habilitar a verificação em duas etapas para suas contas de administrador global sem custo adicional. Se estiver usando uma conta Microsoft, você poderá registrar-se para a autenticação multifator usando as diretrizes encontradas no artigo de suporte da conta Microsoft, [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Se não estiver usando uma conta Microsoft, ative a autenticação multifator para Administradores Globais usando as diretrizes encontradas no artigo [Como exigir verificação em duas etapas para um usuário ou grupo](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Como comprar a autenticação de vários fatores do Azure

Comprar licenças que incluem a autenticação multifator do Azure, como Azure Active Directory Premium ou um pacote de licenças que inclui Azure AD Premium ou acesso condicional e atribuí-las a seus usuários no Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licenciamento baseado em consumo

O licenciamento baseado em consumo não estará mais disponível para novos clientes a partir de 1º de setembro de 2018.

A partir de 1º de setembro de 2018, novos provedores de autenticação podem não ser mais criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

Ao usar um Provedor de Autenticação Multifator do Azure, há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:

1. **Por usuário habilitado** – Para as empresas que desejam habilitar a verificação em duas etapas para um número fixo de funcionários que precisam regularmente de autenticação. A cobrança por usuário é baseada no número de usuários habilitados para o MFA no locatário do Azure AD e no Servidor do MFA do Azure. Se os usuários estão habilitados para MFA no Azure AD e o Azure MFA Server e sincronização de domínio (Azure AD Connect) estiver habilitada, podemos contar o maior conjunto de usuários. Se a sincronização de domínio não estiver habilitada, então podemos contar a soma de todos os usuários habilitados para MFA no Azure AD e o Azure MFA Server. O faturamento é rateado e registrado no sistema Comércio diariamente.

   > [!NOTE]
   > Exemplo de cobrança 1: você tem 5.000 usuários habilitados para MFA hoje. O sistema MFA divide esse número por 31 e relatórios 161.29 usuários para esse dia. No dia seguinte, você habilita mais 15 usuários e, portanto, o sistema MFA relata 161,77 usuários para esse dia. No final do ciclo de cobrança, o número total de usuários cobrado em sua assinatura do Azure até adiciona em torno de 5.000.
   >
   > Exemplo de cobrança 2: tiver uma mistura de usuários com licenças e os usuários sem, para que você tenha um provedor de MFA do Azure por usuário para fazer a diferença. Há 4.500 licenças do Enterprise Mobility + Security em seu locatário, mas 5.000 usuários habilitados para MFA. Sua assinatura do Azure é cobrada por 500 usuários, rateada e considerada 16.13 usuários diariamente.
   >

1. **Por Autenticação** - para empresas que querem habilitar a verificação em duas etapas para um grupo grande de usuários que raramente precisam de autenticação. A cobrança é baseada no número de solicitações de verificação em duas etapas, independentemente de essas verificações serem bem-sucedidas ou negadas. Essa cobrança é exibida no demonstrativo de uso do Azure em pacotes de 10 autenticações e é relatada diariamente.

   > [!NOTE]
   > Exemplo de cobrança 3: atualmente, o serviço do Azure MFA recebido 3,105 solicitações de verificação em duas etapas. Sua assinatura do Azure é cobrada por 310,5 pacotes de autenticação.
   >

É importante observar que você pode ter licenças, mas ainda é cobrado pela configuração com base no consumo. Se você configurar um provedor de MFA por autenticação do Azure, será cobrado para cada solicitação de verificação em duas etapas, mesmo as solicitações feitas por usuários que têm licenças. Se você configurar um provedor de MFA do Azure por usuário em um domínio que não está vinculado a seu locatário do Azure AD, você será cobrado por usuário habilitado, mesmo se os usuários possuem licenças no Azure AD.

## <a name="next-steps"></a>Próximos passos

- Para obter mais detalhes sobre preços, veja [Preços do Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
