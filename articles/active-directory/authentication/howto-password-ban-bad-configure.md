---
title: Como proibir senhas fracas no Azure AD – Azure Active Directory
description: Proibir senhas fracas do seu ambiente com senhas proibidas dinamicamente pelo Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155039"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configurar a lista de senhas proibidas personalizada

Muitas organizações descobrem que seus usuários criam senhas usando palavras locais comuns, como uma escola, uma equipe esportiva ou uma pessoa famosa, deixando-as fáceis de adivinhar. A lista de senhas proibidas personalizada da Microsoft permite que as organizações adicionem cadeia de caracteres para avaliar e bloquear, além da lista global de senhas proibidas, quando usuários e administradores tentam alterar ou redefinir uma senha.

## <a name="add-to-the-custom-list"></a>Adicionar à lista personalizada

A configuração da lista de senhas proibidas personalizada exige uma licença do Azure Active Directory Premium P1 ou P2. Para obter informações mais detalhadas sobre Azure Active Directory licenciamento, consulte a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory** > **métodos de autenticação** > de **segurança** > **proteção por senha**.
1. Defina a opção **Impor lista personalizada** para **Sim**.
1. Adicione cadeias de caracteres à **Lista de senhas proibidas personalizada**, uma cadeia de caracteres por linha
   * A lista de senhas excluídas personalizada pode conter até 1000 termos.
   * A lista de senhas proibidas personalizada não diferencia maiúsculas de minúsculas.
   * A lista de senhas proibidas personalizada considera a substituição de caracteres comuns.
      * Exemplo: "o" e "0" ou "a" e "\@"
   * O comprimento mínimo da cadeia de caracteres é de quatro caracteres e o máximo é de 16 caracteres.
1. Depois de adicionar todas as cadeia de caracteres, clique em **Salvar**.

> [!NOTE]
> É possível que demore várias horas até que as atualizações da lista de senhas proibidas sejam aplicadas.

> [!NOTE]
> A lista de senhas excluídas personalizada é limitada a ter no máximo 1000 termos. Ele não foi projetado para bloquear listas de senhas extremamente grandes. Para aproveitar totalmente os benefícios da lista personalizada de senhas banidas, a Microsoft recomenda que você primeiro revise e entenda o design e o uso pretendidos da lista de senhas excluídas personalizadas (consulte a [lista de senhas excluídas personalizada](concept-password-ban-bad.md#custom-banned-password-list)) e também o algoritmo de avaliação de senha (consulte [como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Modificar a lista de senhas proibidas personalizada em Métodos de Autenticação no portal do Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Como funciona

Cada vez que um usuário ou administrador redefinir ou alterar uma senha do Azure AD, ela passará pelas listas de senhas proibidas para confirmar se não está em uma lista. Essa verificação está incluída em todas as senhas definidas ou alteradas usando o Azure AD.

## <a name="what-do-users-see"></a>O que os usuários veem

Quando um usuário tenta redefinir uma senha para algo que seria banido, ele vê uma das seguintes mensagens de erro:

* Infelizmente, sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com uma senha diferente.
* Infelizmente, você não pode usar essa senha porque ela contém palavras ou caracteres que foram bloqueados pelo administrador. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Próximos passos

[Visão geral conceitual das listas de senhas proibidas](concept-password-ban-bad.md)

[ Visão geral conceitual da proteção por senha do Azure AD ](concept-password-ban-bad-on-premises.md)

[Habilitar integração local com as listas de senhas proibidas](howto-password-ban-bad-on-premises.md)
