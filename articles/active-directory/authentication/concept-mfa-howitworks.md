---
title: Como funciona o Azure MFA-Azure Active Directory
description: A Autenticação Multifator do Azure ajuda a proteger o acesso aos seus dados e aplicativos, ao mesmo tempo em que atende à demanda dos usuários de um processo de logon simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0982f6fb70cd6866af48feab640d5dc36bcb6b28
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848673"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: autenticação multifator do Azure

A segurança da verificação em duas etapas baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga descobrir a senha do usuário, ela será inútil se ele também não estiver de posse do método de autenticação adicional. Isso funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe (normalmente, uma senha)
* Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
* Algo seu (biometria)

<center>

![imagem dos métodos de autenticação conceitual](./media/concept-mfa-howitworks/methods.png)</center>

A MFA (Autenticação Multifator do Azure) ajuda a proteger o acesso a dados e aplicativos enquanto mantém a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de [métodos de autenticação](concept-authentication-methods.md) fáceis de usar. Os usuários podem ou não ser desafiados para MFA com base em decisões de configuração tomadas por um administrador.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a Autenticação Multifator?

A autenticação multifator é fornecida como parte das seguintes ofertas:

* **Azure Active Directory Premium** ou **Microsoft 365 Business** -uso completo da autenticação multifator do Azure usando políticas de acesso condicional para exigir a autenticação multifator.

* **Azure ad gratuito** ou licenças autônomas **do Office 365** – use [políticas de proteção de linha de base de acesso condicional](../conditional-access/concept-baseline-protection.md) pré-criado para exigir a autenticação multifator para seus usuários e administradores.

* **Administradores Globais do Azure Active Directory** – um subconjunto das funcionalidades de Autenticação Multifator do Azure está disponível como um meio para proteger as contas de administrador global.

> [!NOTE]
> Novos clientes não poderão mais comprar Autenticação Multifator do Microsoft Azure como uma oferta independente a partir de 1º de setembro de 2018. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

## <a name="supportability"></a>Capacidade de suporte

Como a maioria dos usuários está acostumada a usar apenas senhas para a autenticação, é importante que a sua empresa comunique todos os usuários sobre esse processo. A conscientização pode reduzir a probabilidade de que os usuários liguem para o seu suporte técnico para resolver pequenos problemas relacionados a MFA. No entanto, existem alguns cenários em que é necessário desabilitar temporariamente o MFA. Use as diretrizes abaixo para entender como lidar com esses cenários:

* Treine sua equipe de suporte para lidar com cenários em que o usuário não consegue entrar porque não têm acesso a seus métodos de autenticação ou esses métodos não estão funcionando corretamente.
   * Usando políticas de acesso condicional para o serviço do Azure MFA, sua equipe de suporte pode adicionar um usuário a um grupo que é excluído de uma política que requer MFA.
* Considere o uso de locais nomeados de acesso condicional como uma maneira de minimizar prompts de verificação em duas etapas. Com essa funcionalidade, os administradores podem ignorar a verificação em duas etapas para usuários que estão entrando de um local de rede confiável seguro, como um segmento de rede usado para integração de novo usuário.
* Implante [Azure ad Identity Protection](../active-directory-identityprotection.md) e dispare a verificação em duas etapas com base nas detecções de risco.

## <a name="next-steps"></a>Próximos passos

- [Implantação passo a passo da autenticação multifator do Azure](howto-mfa-getstarted.md)
