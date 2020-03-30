---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186974"
---
## <a name="ropc-flow-notes"></a>Notas de fluxo ROPC
No Azure Active Directory B2C (Azure AD B2C), as seguintes opções são suportadas:

- **Native Client**: interação do usuário durante a autenticação ocorre quando o código é executado em um dispositivo do lado do usuário. O dispositivo pode ser um aplicativo móvel que está sendo executado em um sistema operacional nativo, como Android e iOS.
- **Fluxo de cliente público**: Somente credenciais de usuário, coletadas por um aplicativo, são enviadas na chamada de API. As credenciais do aplicativo não são enviadas.
- **Adicionar novas declarações**: O conteúdo do token de ID pode ser alterado para adicionar novas declarações.

Não há suporte para os fluxos a seguir:

- **Servidor-para-servidor**: O sistema de proteção de identidade precisa de um endereço IP confiável coletado pelo chamador (o cliente nativo) como parte da interação. Em uma chamada de API do lado do servidor, somente o endereço IP do servidor é usado. Se um limite dinâmico de autenticações com falha for excedido, o sistema de proteção de identidade pode identificar um endereço IP repetido como um invasor.
- **Fluxo de cliente confidencial**: A ID do cliente do aplicativo é validada, mas o segredo do aplicativo não é validado.

Ao utilizar o fluxo ROPC, considere o seguinte:

- O ROPC não funciona quando há qualquer interrupção no fluxo de autenticação que precisa de interação do usuário. Por exemplo, quando uma senha expirou ou precisa ser alterada, a autenticação multifatorial é necessária ou quando mais informações precisam ser coletadas durante o login (por exemplo, o consentimento do usuário).
- O ROPC suporta apenas contas locais. Os usuários não podem fazer login com provedores de identidade federados como Microsoft, Google+, Twitter, AD-FS ou Facebook.
- O Gerenciamento de Sessões, incluindo o "keep me signed-in" (KMSI), não é aplicável.
