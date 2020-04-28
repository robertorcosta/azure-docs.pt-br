---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186974"
---
## <a name="ropc-flow-notes"></a>Notas de fluxo do ROPC
No Azure Active Directory B2C (Azure AD B2C), há suporte para as seguintes opções:

- **Native Client**: interação do usuário durante a autenticação ocorre quando o código é executado em um dispositivo do lado do usuário. O dispositivo pode ser um aplicativo móvel em execução em um sistema operacional nativo, como Android e iOS.
- **Fluxo de cliente público**: Somente credenciais de usuário, coletadas por um aplicativo, são enviadas na chamada de API. As credenciais do aplicativo não são enviadas.
- **Adicionar novas declarações**: O conteúdo do token de ID pode ser alterado para adicionar novas declarações.

Não há suporte para os fluxos a seguir:

- **Servidor-para-servidor**: O sistema de proteção de identidade precisa de um endereço IP confiável coletado pelo chamador (o cliente nativo) como parte da interação. Em uma chamada de API do lado do servidor, somente o endereço IP do servidor é usado. Se um limite dinâmico de autenticações com falha for excedido, o sistema de proteção de identidade pode identificar um endereço IP repetido como um invasor.
- **Fluxo de cliente confidencial**: A ID do cliente do aplicativo é validada, mas o segredo do aplicativo não é validado.

Ao usar o fluxo ROPC, considere o seguinte:

- ROPC não funciona quando há qualquer interrupção no fluxo de autenticação que precisa de interação do usuário. Por exemplo, quando uma senha expira ou precisa ser alterada, a autenticação multifator é necessária ou quando mais informações precisam ser coletadas durante a entrada (por exemplo, consentimento do usuário).
- O ROPC dá suporte apenas a contas locais. Os usuários não podem entrar com provedores de identidade federada como Microsoft, Google +, Twitter, AD-FS ou Facebook.
- O gerenciamento de sessão, incluindo Mantenha-me conectado (KMSI), não é aplicável.
