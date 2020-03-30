---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126722"
---
## <a name="disable-email-verification"></a>Desabilitar a verificação por email

Por padrão, o Azure Active Directory B2C (Azure AD B2C) verifica o endereço de e-mail do cliente para contas locais (contas para usuários que se cadasitam com endereço de e-mail ou nome de usuário). O Azure AD B2C garante endereços de e-mail válidos, exigindo que os clientes os verifiquem durante o processo de inscrição. Também impede que atores mal-intencionados usem processos automatizados para gerar contas fraudulentas em seus aplicativos.

Alguns desenvolvedores de aplicativos preferem pular a verificação de e-mail durante o processo de inscrição e, em vez disso, fazer com que os clientes verifiquem seu endereço de e-mail mais tarde. Para dar suporte a isso, o Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade de diferenciar clientes que verificaram seu endereço de e-mail de clientes que não o fizeram.

> [!WARNING]
> Desabilitar a verificação de email no processo de inscrição pode gerar spam. Se você desativar a verificação de e-mail padrão fornecida pelo Azure AD B2C, recomendamos que você implemente um sistema de verificação de substituição.
