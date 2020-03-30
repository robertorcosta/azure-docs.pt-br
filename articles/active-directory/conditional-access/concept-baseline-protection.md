---
title: Políticas de linha de base do Acesso Condicional - Diretório Ativo do Azure
description: Políticas de acesso condicional de linha de base para proteger as organizações contra ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767561"
---
# <a name="what-are-baseline-policies"></a>O que são políticas básicas?

As políticas de linha de base são um conjunto de políticas predefinidas que ajudam a proteger as organizações contra muitos ataques comuns. Esses ataques comuns podem incluir pulverização, reprodução e phishing de senha. As políticas de linha de base estão disponíveis em todas as edições do Azure AD. A Microsoft está disponibilizando essas políticas de proteção de linha de base para todos, pois o volume de ataques baseados em identidade vem aumentando nos últimos anos. O objetivo dessas quatro políticas é garantir que todas as organizações tenham um nível de segurança básico ativado sem custo adicional.

O gerenciamento de políticas personalizadas de acesso condicional requer uma licença Azure AD Premium.

> [!IMPORTANT]
> As políticas de linha de base estão sendo preteridas. Veja [o que há de novo no Azure Active Directory?](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>Políticas de linha de base

![Políticas de linha de base do Acesso Condicional no portal Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Existem quatro políticas de linha de base:

* Exigir MFA para admins (visualização)
* Proteção do usuário final (visualização)
* Bloquear autenticação de legado (visualização)
* Exigir MFA para gerenciamento de serviços (visualização)

Todas as quatro políticas impactarão fluxos de autenticação legados, como CLIENTES POP, IMAP e desktop sustais mais antigos.

### <a name="exclusions"></a>Exclusões

Quando as políticas de linha de base entraram em sua pré-visualização pública inicial, houve uma opção para excluir os usuários das políticas. Essa capacidade evoluiu através da pré-visualização e foi removida em julho de 2019. As organizações que já haviam criado exclusões puderam continuar a mantê-los novos usuários e não conseguiram adicionar exclusões às políticas.

### <a name="require-mfa-for-admins-preview"></a>Exigir MFA para admins (visualização)

Devido ao poder e acesso que as contas administradoras têm, você deve tratá-las com cuidado especial. Um método comum para melhorar a proteção de contas privilegiadas é exigir uma forma mais forte de verificação da conta quando elas são usadas para fazer login. No Azure Active Directory, você pode obter uma verificação de conta mais forte, exigindo que os administradores se registrem e usem a Autenticação Multifatorial do Azure.

Exigir MFA para administradores (visualização) é uma política de linha de base que requer autenticação multifatorial (MFA) para as seguintes funções de diretório, consideradas as funções azure AD mais privilegiadas:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança
* Administrador de assistência técnica/administrador de senha
* Administrador de cobrança
* Administrador de usuários

Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Proteção do usuário final (visualização)

Administradores privilegiados não são os únicos alvos de ataques. Atores ruins tendem a atingir usuários normais. Depois de obter acesso, esses maus atores podem solicitar acesso a informações privilegiadas em nome do titular da conta original ou baixar todo o diretório e realizar um ataque de phishing em toda a sua organização. Um método comum para melhorar a proteção de todos os usuários é exigir uma forma mais forte de verificação da conta quando um login de risco é detectado.

**A proteção do usuário final (visualização)** é uma política de linha de base que protege todos os usuários em um diretório. A habilitação desta política exige que todos os usuários se registrem para a Autenticação Multifatorial do Azure dentro de 14 dias. Depois de registrados para MFA, os usuários serão solicitados a receber MFA somente durante tentativas de inserção de credenciais arriscadas. Contas de usuário comprometidas são bloqueadas até a redefinição de senha e risco de demissão. 

> [!NOTE]
> Todos os usuários previamente sinalizados para o risco são bloqueados até que a senha seja redefinida e o risco de demissão após a ativação da diretiva.

### <a name="block-legacy-authentication-preview"></a>Bloquear autenticação de legado (visualização)

Protocolos de autenticação legados (ex: IMAP, SMTP, POP3) são protocolos normalmente usados por clientes de correio mais antigos para autenticar. Os protocolos legados não suportam autenticação multifatorial. Mesmo que você tenha uma política que exija autenticação multifatorial para o seu diretório, um ator ruim pode autenticar usando um desses protocolos legados e contornar a autenticação multifatorial.

A melhor maneira de proteger sua conta contra solicitações de autenticação maliciosas feitas por protocolos legados é bloqueá-las.

A **política de autenticação de legado do Bloco (visualização)** bloqueia as solicitações de autenticação feitas usando protocolos legados. A autenticação moderna deve ser usada para fazer login com sucesso para todos os usuários. Usado em conjunto com as outras políticas de linha de base, as solicitações provenientes de protocolos legados serão bloqueadas. Além disso, todos os usuários serão obrigados a MFA sempre que necessário. Esta diretiva não bloqueia o Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Exigir MFA para gerenciamento de serviços (visualização)

As organizações usam uma variedade de serviços do Azure e os gerenciam a partir de ferramentas baseadas no Azure Resource Manager, como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Usar qualquer uma dessas ferramentas para realizar o gerenciamento de recursos é uma ação altamente privilegiada. Essas ferramentas podem alterar configurações de toda a assinatura, como configurações de serviço e faturamento por assinatura.

Para proteger ações privilegiadas, essa política **de Gerenciamento de Serviços (preview)** exigirá autenticação de vários fatores para qualquer usuário que acesse o portal Azure, o Azure PowerShell ou o Azure CLI.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

* [Habilitando padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md)
* [Políticas de Acesso Condicional Comum](concept-conditional-access-policy-common.md)
* [Cinco etapas para proteger sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md)
