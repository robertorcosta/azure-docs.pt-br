---
title: Padrões de segurança do Azure Active Directory
description: Políticas padrão de segurança que ajudam a proteger as organizações contra ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f307553a97973d03b0699248373e53e4845aa39a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869911"
---
# <a name="what-are-security-defaults"></a>O que são padrões de segurança?

Gerenciar a segurança pode ser difícil quando ataques comuns relacionados à identidade estão se tornando cada vez mais populares. Esses ataques incluem spray de senha, replay e phishing.

Os padrões de segurança no Azure Active Directory (Azure AD) facilitam a segurança e ajudam a proteger sua organização. Os padrões de segurança contêm configurações de segurança pré-configuradas para ataques comuns. 

A Microsoft está disponibilizando padrões de segurança para todos. O objetivo é garantir que todas as organizações tenham um nível básico de segurança ativado sem custo adicional. Você apela os padrões de segurança no portal Azure.

![Captura de tela do portal Azure com o alternador para ativar padrões de segurança](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Se o seu inquilino foi criado em ou após 22 de outubro de 2019, é possível que você esteja experimentando o novo comportamento seguro por padrão e já tenha padrões de segurança ativados em seu inquilino. Em um esforço para proteger todos os nossos usuários, os padrões de segurança estão sendo implementados para todos os novos inquilinos criados.

Mais detalhes sobre por que os padrões de segurança estão sendo disponibilizados podem ser encontrados no post do blog de Alex Weinert, [introduzindo padrões de segurança](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Registro unificado de autenticação multifatorial

Todos os usuários do seu inquilino devem se registrar para autenticação multifatorial (MFA) na forma do serviço de Autenticação Multifatorial Azure. Os usuários têm 14 dias para se registrar em Autenticação Multifatorial usando o aplicativo Microsoft Authenticator. Após os 14 dias passados, o usuário não poderá fazer login até que o registro de Autenticação Multifatorial seja concluído.

Entendemos que alguns usuários podem estar fora do escritório ou não entrardurante os 14 dias imediatamente após a habilitação de padrões de segurança. Para garantir que cada usuário tenha tempo suficiente para se registrar para autenticação multifatorial, o período de 14 dias é único para cada usuário. O período de 14 dias de um usuário começa após seu primeiro login interativo bem-sucedido depois de ativar padrões de segurança.

## <a name="multi-factor-authentication-enforcement"></a>Aplicação da autenticação multifatorial

### <a name="protecting-administrators"></a>Protegendo administradores

Usuários com acesso a contas privilegiadas aumentaram o acesso ao seu ambiente. Devido à capacidade que essas contas têm, devem ser tratadas com cuidado especial. Um método comum para melhorar a proteção de contas privilegiadas é exigir uma forma mais forte de verificação de conta para o login. No Azure AD, você pode obter uma verificação de conta mais forte, exigindo autenticação multifatorial.

Após o término do registro com a Autenticação Multifatorial, as nove funções de administrador azure AD serão obrigadas a executar autenticação adicional sempre que fizerem login:

- Administrador global
- Administrador do SharePoint
- Administrador do Exchange
- Administrador de acesso condicional
- Administrador de segurança
- Administrador de helpdesk ou administrador de senhas
- Administrador de cobrança
- Administrador de usuários
- Administrador de autenticação

### <a name="protecting-all-users"></a>Protegendo todos os usuários

Tendemos a pensar que as contas do administrador são as únicas que precisam de camadas extras de autenticação. Os administradores têm amplo acesso a informações confidenciais e podem fazer alterações em configurações de toda a assinatura. Mas os atacantes tendem a atingir usuários finais. 

Depois que esses invasores tiverem acesso, eles podem solicitar acesso a informações privilegiadas em nome do titular da conta original. Eles podem até baixar todo o diretório para realizar um ataque de phishing em toda a sua organização. 

Um método comum para melhorar a proteção de todos os usuários é exigir uma forma mais forte de verificação de conta, como a Autenticação Multifatorial, para todos. Depois que os usuários concluírem o registro de Autenticação Multifatorial, eles serão solicitados para autenticação adicional sempre que necessário.

### <a name="blocking-legacy-authentication"></a>Bloqueando a autenticação do legado

Para dar aos seus usuários acesso fácil aos seus aplicativos na nuvem, o Azure AD suporta uma variedade de protocolos de autenticação, incluindo autenticação herdada. *Autenticação legado* é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes que não usam autenticação moderna (por exemplo, um cliente office 2010).
- Qualquer cliente que use protocolos de e-mail mais antigos, como IMAP, SMTP ou POP3.

Hoje, a maioria das tentativas de login comprometedoras vem da autenticação do legado. A autenticação do legado não suporta autenticação multifatorial. Mesmo que você tenha uma política de autenticação multifatorial ativada em seu diretório, um invasor pode autenticar usando um protocolo mais antigo e contornar a autenticação multifatorial. 

Depois que os padrões de segurança forem ativados no seu inquilino, todas as solicitações de autenticação feitas por um protocolo mais antigo serão bloqueadas. Os padrões de segurança bloqueiam a autenticação básica do Exchange Active Sync.

> [!WARNING]
> Antes de ativar os padrões de segurança, certifique-se de que seus administradores não estejam usando protocolos de autenticação mais antigos. Para obter mais informações, consulte [Como se afastar da autenticação do legado](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Proteção de ações privilegiadas

As organizações usam uma variedade de serviços do Azure gerenciados através da API do Azure Resource Manager, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

Usar o Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. O Azure Resource Manager pode alterar configurações em todo o inquilino, como configurações de serviço e faturamento de assinatura. A autenticação de um único fator é vulnerável a uma variedade de ataques, como phishing e spray de senha. 

É importante verificar a identidade dos usuários que desejam acessar o Azure Resource Manager e atualizar configurações. Você verifica sua identidade exigindo autenticação adicional antes de permitir o acesso.

Depois de ativar os padrões de segurança no seu inquilino, qualquer usuário que estiver acessando o portal Azure, o Azure PowerShell ou o Azure CLI precisará completar a autenticação adicional. Essa política se aplica a todos os usuários que estão acessando o Azure Resource Manager, seja ele um administrador ou um usuário. 

> [!NOTE]
> Os inquilinos do Exchange Online pré-2017 têm autenticação moderna desativada por padrão. Para evitar a possibilidade de um loop de login enquanto autentica através desses inquilinos, você deve [habilitar a autenticação moderna](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> A conta de sincronização Do Azure AD Connect está excluída dos padrões de segurança e não será solicitada a registrar ou executar autenticação multifatorial. As organizações não devem usar essa conta para outros fins.

## <a name="deployment-considerations"></a>Considerações de implantação

As seguintes considerações adicionais estão relacionadas à implantação de padrões de segurança para o seu inquilino.

### <a name="authentication-methods"></a>Métodos de autenticação

Os padrões de segurança permitem o registro e o uso da Autenticação Multifatorial do Azure **usando apenas o aplicativo Microsoft Authenticator usando notificações**. O Conditional Access permite o uso de qualquer método de autenticação que o administrador escolher para habilitar.

|   | Padrões de segurança | Acesso Condicional |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |
| Senhas de aplicativo |   | X** |

** As senhas do aplicativo só estão disponíveis no MFA por usuário com cenários de autenticação legado somente se habilitadas pelos administradores.

### <a name="conditional-access"></a>Acesso Condicional

Você pode usar o Acesso Condicional para configurar políticas semelhantes aos padrões de segurança, mas com mais granularidade, incluindo exclusões do usuário, que não estão disponíveis em padrões de segurança. Se você estiver usando o Conditional Access e tiver as políticas de acesso condicional ativadas em seu ambiente, os padrões de segurança não estarão disponíveis para você. Se você tiver uma licença que forneça acesso condicional, mas não tenha nenhuma política de acesso condicional ativada em seu ambiente, você será bem-vindo a usar padrões de segurança até ativar políticas de acesso condicional. Mais informações sobre o licenciamento Azure AD podem ser encontradas na [página de preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Mensagem de aviso de que você pode ter padrões de segurança ou acesso condicional, não ambos](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Aqui estão guias passo a passo sobre como você pode usar o Acesso Condicional para configurar políticas equivalentes:

- [Exigir MFA para administradores](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para gerenciamento do Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Bloquear a autenticação herdada](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Exigir MFA para todos os usuários](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Exigir registro Do Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Requer proteção de identidade Azure AD

## <a name="enabling-security-defaults"></a>Habilitando padrões de segurança

Para habilitar padrões de segurança em seu diretório:

1. Faça login no  [portal Azure](https://portal.azure.com)como administrador de segurança, administrador de Acesso Condicional ou administrador global.
1. Navegue até as > **propriedades**do diretório **ativo do Azure**.
1. Selecione **Gerenciar padrões de segurança**.
1. Defina o **alternar padrão de segurança** Enable para **Sim**.
1. Clique em **Salvar**.

## <a name="disabling-security-defaults"></a>Desativar padrões de segurança

As organizações que optarem por implementar políticas de acesso condicional que substituam os padrões de segurança devem desativar os padrões de segurança. 

![Mensagem de aviso desabilita padrões de segurança para ativar acesso condicional](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Para desativar os padrões de segurança em seu diretório:

1. Faça login no  [portal Azure](https://portal.azure.com)como administrador de segurança, administrador de Acesso Condicional ou administrador global.
1. Navegue até as > **propriedades**do diretório **ativo do Azure**.
1. Selecione **Gerenciar padrões de segurança**.
1. Defina o **alternar padrão de segurança** Enable para **Não**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas de Acesso Condicional Comum](../conditional-access/concept-conditional-access-policy-common.md)
