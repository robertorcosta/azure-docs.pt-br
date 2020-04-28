---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79126722"
---
## <a name="disable-email-verification"></a>Desabilitar a verificação por email

Por padrão, Azure Active Directory B2C (Azure AD B2C) verifica o endereço de email do cliente para contas locais (contas para usuários que se inscrevem com endereço de email ou nome de usuário). O Azure AD B2C garante endereços de email válidos exigindo que os clientes os verifiquem durante o processo de inscrição. Ele também impede que os atores mal-intencionados usem processos automatizados para gerar contas fraudulentas em seus aplicativos.

Alguns desenvolvedores de aplicativos preferem ignorar a verificação de email durante o processo de inscrição e, em vez disso, fazer com que os clientes verifiquem seu endereço de email posteriormente. Para dar suporte a isso, o Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade para diferenciar os clientes que verificaram seu endereço de email dos clientes que não têm.

> [!WARNING]
> Desabilitar a verificação de email no processo de inscrição pode gerar spam. Se você desabilitar a verificação de email padrão fornecida pelo Azure AD B2C, recomendamos que implemente um sistema de verificação de substituição.
