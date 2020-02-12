---
title: Exemplos de JavaScript
titleSuffix: Azure AD B2C
description: Saiba mais sobre como usar o JavaScript no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1381ddb16697b1e892794604bbfafda815bd6182
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149059"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Exemplos de JavaScript para uso no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Você pode adicionar seu próprio código do lado do cliente JavaScript aos seus aplicativos Azure Active Directory B2C (Azure AD B2C).

Para habilitar o JavaScript para seus aplicativos:

* Adicionar um elemento à [política personalizada](custom-policy-overview.md)
* Selecionar um [layout de página](page-layout.md)
* Usar [b2clogin.com](b2clogin.md) em suas solicitações

Este artigo descreve como você pode alterar sua política personalizada para habilitar a execução de script.

> [!NOTE]
> Se você quiser habilitar o JavaScript para fluxos de usuário, consulte [JavaScript e versões de layout de página em Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Prerequisites

### <a name="select-a-page-layout"></a>Selecionar um layout de página

* Selecione um [layout de página](contentdefinitions.md#select-a-page-layout) para os elementos da interface do usuário do seu aplicativo.

    Se você pretende usar o JavaScript, precisará [definir uma versão de layout de página](contentdefinitions.md#migrating-to-page-layout) com a versão de `contract` de página para *todas* as definições de conteúdo em sua política personalizada.

## <a name="add-the-scriptexecution-element"></a>Adicionar o elemento ScriptExecution

Você habilita a execução do script, adicionando o elemento **ScriptExecution** ao elemento [RelyingParty](relyingparty.md).

1. Abra o arquivo de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
2. Adicione o elemento **ScriptExecution** ao elemento **UserJourneyBehaviors** do **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Salve e carregue o arquivo.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>Exemplos de JavaScript

### <a name="show-or-hide-a-password"></a>Mostrar ou ocultar uma senha

É uma maneira comum para ajudar seus clientes a obterem êxito nas inscrições deles e permitir que eles vejam o que inseriram como a senha. Essa opção ajuda os usuários a inscrever-se, permitindo que eles vejam e façam correções em suas senhas, caso necessário. Qualquer campo de digitação de senha tem uma caixa de seleção com um rótulo **Mostrar senha**.  Isso permite que o usuário veja a senha em texto sem formatação. Inclua este snippet de código em seu modelo de inscrição ou entrada para uma página autodeclarada:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Adicionar termos de uso

Inclua o seguinte código na sua página em que você quer incluir uma caixa de seleção de **termos de uso**. Essa caixa de seleção é normalmente necessária nas páginas de criação de conta local e de criação de conta social.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

No código, substitua `termsOfUseUrl` pelo link para o contrato de termos de uso. Para seu diretório, crie um novo atributo de usuário chamado **termsOfUse** e, em seguida, inclua **termsOfUse** como um atributo de usuário.

## <a name="next-steps"></a>Próximas etapas

Encontre mais informações sobre como personalizar a interface do usuário dos seus aplicativos em [Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C](custom-policy-ui-customization.md).
