---
title: Verificar reclamações com controles de exibição
titleSuffix: Azure AD B2C
description: Saiba como usar os controles de exibição Azure AD B2C para verificar as reclamações nas jornadas de usuário fornecidas por suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188776"
---
# <a name="verification-display-control"></a>Controle de exibição de verificação

Use um [controle de exibição](display-controls.md) de verificação para verificar uma reclamação, por exemplo, um endereço de e-mail ou número de telefone, com um código de verificação enviado ao usuário.

## <a name="verificationcontrol-actions"></a>VerificaçãoAções de controle

O controle de exibição de verificação consiste em duas etapas (ações):

1. Solicite um destino do usuário, como um endereço de e-mail ou número de telefone, para o qual o código de verificação deve ser enviado. Quando o usuário seleciona o botão **Enviar código,** a **Ação De Envio** do controle de exibição de verificação é executada. O **SendCode Action** gera um código, constrói o conteúdo a ser enviado e envia-o ao usuário. O valor do endereço pode ser pré-preenchido e servir como uma autenticação de segundo fator.

    ![Página de exemplo para enviar ação de código](media/display-control-verification/display-control-verification-email-action-01.png)

1. Depois que o código é enviado, o usuário lê a mensagem, insere o código de verificação no controle fornecido pelo controle de exibição e **seleciona Verificar Código**. Ao selecionar **Código de Verificação,** a **Ação VerifyCode** é executada para verificar o código associado ao endereço. Se o usuário selecionar **Enviar novo código,** a primeira ação será executada novamente.

    ![Página de exemplo para verificar ação de código](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificaçãoControle elementos necessários

O **VerificationControl** deve conter os seguintes elementos:

- O tipo `DisplayControl` de `VerificationControl`é.
- `DisplayClaims`
  - **Enviar para** - Uma ou mais reivindicações especificando para onde enviar o código de verificação. Por exemplo, *e-mail* ou *código de país* e número de *telefone*.
  - **Código de verificação** - O código de verificação afirma que o usuário fornece após o código ter sido enviado. Esta reivindicação deve ser definida `ControlClaimType` conforme necessário, `VerificationCode`e a deve ser definida como .
- Reivindicação de saída (opcional) a ser devolvida à página auto-afirmada após o usuário concluir o processo de verificação. Por exemplo, *e-mail* ou *código de país* e número de *telefone*. O perfil técnico auto-afirmado usa as reivindicações para persistir os dados ou borbulhar as reivindicações de saída para a próxima etapa de orquestração.
- Dois `Action`s com os seguintes nomes:
  - **SendCode** - Envia um código para o usuário. Esta ação geralmente contém dois perfis técnicos de validação, para gerar um código e enviá-lo.
  - **VerifyCode** - Verifica o código. Esta ação geralmente contém um único perfil técnico de validação.

No exemplo abaixo, uma caixa de **texto de e-mail** é exibida na página. Quando o usuário insere seu endereço de e-mail e seleciona **SendCode,** a ação **SendCode** é acionada no back-end Ad B2C do Azure.

Em seguida, o usuário insere a **verificaçãoCódigo** e seleciona **VerifyCode** para ativar a ação **VerifyCode** no back-end. Se todas as validações passarem, o **Controle de Verificação** será considerado completo e o usuário poderá continuar até a próxima etapa.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
