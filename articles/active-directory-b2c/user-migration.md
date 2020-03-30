---
title: Abordagens de migração de usuários
titleSuffix: Azure AD B2C
description: Migre as contas de usuário de outro provedor de identidade para o Azure AD B2C usando os métodos de importação em massa ou migração perfeita.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332344"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrar usuários para Azure AD B2C

Migrar de outro provedor de identidade para o Azure Active Directory B2C (Azure AD B2C) também pode exigir a migração de contas de usuários existentes. Dois métodos de migração são discutidos aqui, *importação a granel* e *migração perfeita.* Com qualquer abordagem, você é obrigado a escrever um aplicativo ou script que use a [API](manage-user-accounts-graph-api.md) do Microsoft Graph para criar contas de usuário no Azure AD B2C.

## <a name="bulk-import"></a>Importação a granel

No fluxo de importação em massa, seu aplicativo de migração executa essas etapas para cada conta de usuário:

1. Leia a conta de usuário do antigo provedor de identidade, incluindo suas credenciais atuais (nome de usuário e senha).
1. Crie uma conta correspondente no diretório Ad B2C do Azure com as credenciais atuais.

Use o fluxo de importação a granel em qualquer uma dessas duas situações:

- Você tem acesso às credenciais de texto simples de um usuário (seu nome de usuário e senha).
- As credenciais são criptografadas, mas você pode decifrá-las.

Para obter informações sobre a criação programática de contas de usuários, consulte [Gerenciar contas de usuário AD B2C do Azure com o Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="seamless-migration"></a>Migração perfeita

Use o fluxo de migração perfeito se senhas de texto simples no antigo provedor de identidade não estiverem acessíveis. Por exemplo, quando:

- A senha é armazenada em um formato criptografado unidirecional, como com uma função hash.
- A senha é armazenada pelo provedor de identidade legado de uma forma que você não pode acessar. Por exemplo, quando o provedor de identidade valida credenciais ligando para um serviço web.

O fluxo de migração contínuo ainda requer migração em massa de contas de usuário, mas depois usa uma [política personalizada](custom-policy-get-started.md) para consultar uma [API REST](custom-policy-rest-api-intro.md) (que você cria) para definir a senha de cada usuário no primeiro login.

O fluxo migratório contínuo tem, portanto, duas fases: *importação a granel* e *credenciais definidas.*

### <a name="phase-1-bulk-import"></a>Fase 1: Importação a granel

1. Seu aplicativo de migração lê as contas de usuário do antigo provedor de identidade.
1. O aplicativo de migração cria contas de usuário correspondentes no diretório Azure AD B2C, mas *não define senhas*.

### <a name="phase-2-set-credentials"></a>Fase 2: Definir credenciais

Depois que a migração em massa das contas estiver concluída, sua política personalizada e a API REST executam o seguinte quando um usuário faz o sinal:

1. Leia a conta de usuário Azure AD B2C correspondente ao endereço de e-mail inserido.
1. Verifique se a conta está sinalizada para migração avaliando um atributo de extensão booleana.
    - Se o atributo de extensão retornar, `True`ligue para a API REST para validar a senha contra o provedor de identidade legado.
      - Se a API REST determinar que a senha está incorreta, retorne um erro amigável ao usuário.
      - Se a API REST determinar que a senha está correta, escreva a senha na conta Azure AD B2C e altere o atributo de extensão booleana para `False`.
    - Se o atributo `False`de extensão booleana retornar, continue o processo de login normalmente.

Para ver um exemplo de política personalizada e API REST, consulte a [amostra perfeita de migração de usuários](https://aka.ms/b2c-account-seamless-migration) no GitHub.

![Diagrama de fluxograma da abordagem de migração perfeita para migração de usuários](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagrama: Fluxo de migração sem emendas*

## <a name="best-practices"></a>Práticas recomendadas

### <a name="security"></a>Segurança

A abordagem de migração perfeita usa sua própria API REST personalizada para validar as credenciais de um usuário contra o provedor de identidade legado.

**Você deve proteger sua API REST contra ataques de força bruta.** Um invasor pode enviar várias senhas na esperança de eventualmente adivinhar as credenciais de um usuário. Para ajudar a derrotar tais ataques, pare de atender solicitações à sua API REST quando o número de tentativas de login passar por um certo limite. Além disso, proteja a comunicação entre o Azure AD B2C e sua API REST. Para saber como proteger suas APIs RESTful para produção, consulte [API Secure RESTful](secure-rest-api.md).

### <a name="user-attributes"></a>Atributos de usuário

Nem todas as informações no provedor de identidade legado devem ser migradas para o diretório Azure AD B2C. Identifique o conjunto apropriado de atributos do usuário para armazenar no Azure AD B2C antes de migrar.

- **LOJA DO** em Azure AD B2C
  - Nome de usuário, senha, endereços de e-mail, números de telefone, números de membros/identificadores.
  - Marcadores de consentimento para política de privacidade e contratos de licença de usuário final.
- **NÃO armazene** no Azure AD B2C
  - Dados confidenciais como números de cartão de crédito, números de segurança social (SSN), registros médicos ou outros dados regulados por órgãos de conformidade do governo ou do setor.
  - Preferências de marketing ou comunicação, comportamentos do usuário e insights.

### <a name="directory-clean-up"></a>Limpeza de diretórios

Antes de iniciar o processo de migração, aproveite para limpar seu diretório.

- Identifique o conjunto de atributos de usuário a serem armazenados no Azure AD B2C e migre apenas o que você precisa. Se necessário, você pode criar [atributos personalizados](custom-policy-custom-attributes.md) para armazenar mais dados sobre um usuário.
- Se você estiver migrando de um ambiente com várias fontes de autenticação (por exemplo, cada aplicativo tem seu próprio diretório de usuário), migre para uma conta unificada no Azure AD B2C.
- Se vários aplicativos tiverem nomes de usuário diferentes, você pode armazenar todos eles em uma conta de usuário Azure AD B2C usando a coleção de identidades. Com relação à senha, deixe o usuário escolher uma e configurá-la no diretório. Por exemplo, com a migração perfeita, apenas a senha escolhida deve ser armazenada na conta Azure AD B2C.
- Remova contas de usuários não utilizadas antes da migração ou não migre contas velhas.

### <a name="password-policy"></a>Política de senha

Se as contas que você está migrando tiverem uma força de senha mais fraca do que a [forte força de senha](../active-directory/authentication/concept-sspr-policy.md) imposta pelo Azure AD B2C, você pode desativar o forte requisito de senha. Para obter mais informações, consulte [propriedade de diretiva de senha](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Próximas etapas

O repositório [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) no GitHub contém um exemplo de política personalizada de migração perfeita e uma amostra de código REST API:

[Política personalizada de migração de usuário perfeita & amostra de código REST API](https://aka.ms/b2c-account-seamless-migration)
