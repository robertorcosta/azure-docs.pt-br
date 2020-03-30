---
title: Personalizar reivindicações de token do aplicativo Azure AD
titleSuffix: Microsoft identity platform
description: Aprenda a personalizar as declarações emitidas no token SAML para aplicativos empresariais no Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 52779b7ffea0f33676426f145a700c7181cf0bf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263082"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: personalizar sinistros emitidos no token SAML para aplicativos corporativos

Hoje, o Azure Active Directory (Azure AD) suporta o sso de login único (SSO) com a maioria dos aplicativos corporativos, incluindo ambos os aplicativos pré-integrados na galeria de aplicativos Azure AD, bem como aplicativos personalizados. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando o protocolo SAML 2.0, o Azure AD envia um token ao aplicativo (por um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML contêm informações sobre o usuário conhecidas como *reclamações*.

Uma *declaração* são informações que um provedor de identidade declara sobre um usuário dentro do token que emite para esse usuário. No [Token SAML](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na Instrução de Atributo SAML. A ID única do usuário é normalmente representada na SAML Subject, também denominada Identificador de Nome.

Por padrão, o Azure AD emite um token `NameIdentifier` SAML para o seu aplicativo que contém uma reclamação com um valor do nome de usuário do usuário (também conhecido como nome principal do usuário) no Azure AD, que pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o aplicativo no Portal do Azure. Em seguida, abra a seção **Atributos do usuário & Reivindicações.**

![Abra a seção Atributos do usuário & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML:

* O aplicativo `NameIdentifier` requer a alegação ou NameID de ser algo diferente do nome de usuário (ou nome principal do usuário) armazenado no Azure AD.
* O aplicativo foi escrito para exigir um conjunto diferente de URIs ou valores de declaração.

## <a name="editing-nameid"></a>Edição de nameID

Para editar o NameID (valor identificador de nome):

1. Abra a página **de valor do identificador Nome.**
1. Selecione o atributo ou a transformação que deseja aplicar ao atributo. Opcionalmente, você pode especificar o formato que deseja que a reivindicação NameID tenha.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se a solicitação SAML contiver o elemento NameIDPolicy com um formato específico, o Azure AD honrará o formato na solicitação.

Se a solicitação SAML não contiver um elemento para NameIDPolicy, o Azure AD emitirá o NameID com o formato especificado. Se nenhum formato for especificado, o Azure AD usará o formato de origem padrão associado à fonte de sinistro selecionada.

Na **parada do formato de identificador de nome Choose,** você pode selecionar uma das seguintes opções.

| Formato NameID | Descrição |
|---------------|-------------|
| **Padrão** | O Azure AD usará o formato de origem padrão. |
| **Persistente** | O Azure AD usará o Persistent como o formato NameID. |
| **EmailAddress** | O Azure AD usará o EmailAddress como o formato NameID. |
| **Unspecified** | O Azure AD usará o formato NameID não especificado. |
| **Nome qualificado de domínio do Windows** | O Azure AD usará o WindowsDomainQualifiedName como o formato NameID. |

O NomeID transitório também é suportado, mas não está disponível no dropdown e não pode ser configurado no lado do Azure. Para saber mais sobre o atributo NameIDPolicy, consulte [protocolo SAML de inscrição única](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a fonte desejada para a declaração `NameIdentifier` (ou NameID). Você pode selecionar entre as opções a seguir.

| Nome | Descrição |
|------|-------------|
| Email | Endereço de email do usuário |
| userprincipalName | Nome principal do usuário (UPN) do usuário |
| onpremisessamaccount | Nome da conta SAM sincronizada do Azure AD local |
| objectid | Objectid do usuário no Azure AD |
| employeeid | ID do funcionário do usuário |
| Extensões de diretório | Extensões de diretório [sincronizadas do Active Directory local usando a Sincronização do Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de Extensão 1-15 | Atributos de extensão locais usados para estender o esquema do Azure AD |

Para obter mais informações, consulte [tabela 3: Valores de identificação válidos por fonte](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Você também pode atribuir qualquer valor constante (estático) a quaisquer reivindicações que você definir no Azure AD. Siga as etapas abaixo para atribuir um valor constante:

1. No [portal Azure,](https://portal.azure.com/)na seção **Atributos do Usuário & Claims,** clique no ícone **Editar** para editar as reivindicações.

1. Clique na reivindicação necessária que deseja modificar.

1. Digite o valor constante sem aspas no **atributo Origem** conforme sua organização e clique **em Salvar**.

    ![Abra a seção Atributos do usuário & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. O valor constante será exibido como abaixo.

    ![Abra a seção Atributos do usuário & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Reivindicações especiais - transformações

Você também pode usar as funções de transformação de sinistros.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de e-mail ou do nome principal do usuário. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Participe()** | Une um atributo a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para anexar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor de identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Adicionando reivindicações específicas de aplicativos

Para adicionar reivindicações específicas de aplicativos:

1. Em **Atributos de usuário & Reivindicações**, selecione **Adicionar nova reivindicação** para abrir a página Gerenciar **reivindicações de usuário.**
1. Digite o **nome** das reivindicações. O valor não precisa seguir estritamente um padrão URI, de acordo com a especificação SAML. Se você precisar de um padrão URI, você pode colocá-lo no campo **Namespace.**
1. Selecione a **Fonte** onde a reivindicação recuperará seu valor. Você pode selecionar um atributo de usuário a partir do atributo de origem ou aplicar uma transformação ao atributo do usuário antes de emiti-lo como uma reivindicação.

### <a name="claim-transformations"></a>Transformações de declaração

Para aplicar uma transformação a um atributo do usuário:

1. Em **Gerenciar a reivindicação,** selecione *Transformação* como fonte de sinistro para abrir a página **Gerenciar transformação.**
2. Selecione a função a partir da queda de transformação. Dependendo da função selecionada, você terá que fornecer parâmetros e um valor constante para avaliar na transformação. Consulte a tabela abaixo para obter mais informações sobre as funções disponíveis.
3. Para aplicar a transformação múltipla, clique em **Adicionar transformação**. Você pode aplicar um máximo de duas transformações a uma reivindicação. Por exemplo, você pode primeiro extrair `user.mail`o prefixo de e-mail do . Em seguida, faça a parte maiúscula da corda.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Você pode usar as seguintes funções para transformar reivindicações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de e-mail ou do nome principal do usuário. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Participe()** | Cria um novo valor juntando dois atributos. Opcionalmente, você pode usar um separador entre os dois atributos. Para a transformação de reivindicação NameID, a adesão é restrita a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para anexar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor de identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |
| **Contém()** | Produz um atributo ou uma constante se a entrada corresponder ao valor especificado. Caso contrário, você pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma reclamação onde o valor é@contoso.como endereço de e-mail do usuário se ele contiver o domínio " ", caso contrário, você deseja obter o nome principal do usuário. Para fazer isso, você configuraria os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.email<br/>*Valor*:@contoso.com" "<br/>Parâmetro 2 (saída): user.email<br/>Parâmetro 3 (saída se não houver correspondência): user.userprincipalname |
| **EndWith()** | Produz um atributo ou uma constante se a entrada terminar com o valor especificado. Caso contrário, você pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma reclamação onde o valor é o ID do funcionário do usuário se o ID do funcionário terminar com "000", caso contrário, você deseja produzir um atributo de extensão. Para fazer isso, você configuraria os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.employeeid<br/>*Valor*: "000"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **StartWith()** | Produz um atributo ou uma constante se a entrada começar com o valor especificado. Caso contrário, você pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma reclamação onde o valor é o ID do funcionário do usuário se o país/região começar com "US", caso contrário, você deseja produzir um atributo de extensão. Para fazer isso, você configuraria os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.country<br/>*Valor*: "US"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **Extrair() - Após correspondência** | Retorna a substring depois de corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon", o valor correspondente é "Finance_", então a saída da reivindicação é "BSimon". |
| **Extrair() - Antes de combinar** | Retorna a substring até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "BSimon_US", o valor correspondente é "_US", então a saída da reivindicação é "BSimon". |
| **Extrair() - Entre correspondência** | Retorna a substring até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon_US", o primeiro valor de correspondência for "Finance_", o segundo valor de correspondência é "_US", então a saída da declaração é "BSimon". |
| **ExtractAlpha() - Prefixo** | Retorna a parte alfabética do prefixo da seqüência.<br/>Por exemplo, se o valor da entrada for "BSimon_123", então ele retorna "BSimon". |
| **ExtractAlpha() - Sufixo** | Retorna o sufixo parte alfabética da corda.<br/>Por exemplo, se o valor da entrada for "123_Simon", então ele retorna "Simon". |
| **ExtractNumérico() - Prefixo** | Retorna a parte numérica do prefixo da seqüência.<br/>Por exemplo, se o valor da entrada for "123_BSimon", então ele retorna "123". |
| **ExtractNumérico() - Sufixo** | Retorna a parte numérica do sufixo da corda.<br/>Por exemplo, se o valor da entrada for "BSimon_123", então ele retorna "123". |
| **IfEmpty()** | Produz um atributo ou uma constante se a entrada estiver nula ou vazia.<br/>Por exemplo, se você quiser produzir um atributo armazenado em um atributo de extensão se o ID do funcionário para um determinado usuário estiver vazio. Para fazer isso, você configuraria os seguintes valores:<br/>Parâmetro 1(entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1<br/>Parâmetro 3 (saída se não houver correspondência): user.employeeid |
| **IfNotEmpty()** | Produz um atributo ou uma constante se a entrada não estiver nula ou vazia.<br/>Por exemplo, se você quiser produzir um atributo armazenado em um atributo de extensão se o ID do funcionário para um determinado usuário não estiver vazio. Para fazer isso, você configuraria os seguintes valores:<br/>Parâmetro 1(entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1 |

Se você precisar de transformações adicionais, envie sua ideia no [fórum de feedback no Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) na categoria de aplicação *SaaS.*

## <a name="emitting-claims-based-on-conditions"></a>Emitindo sinistros com base em condições

Você pode especificar a origem de uma reclamação com base no tipo de usuário e no grupo ao qual o usuário pertence. 

O tipo de usuário pode ser:
- **Any:** Todos os usuários podem acessar o aplicativo.
- **Membros**: Membro nativo do inquilino
- **Todos os convidados**: O usuário é trazido de uma organização externa com ou sem Azure AD.
- **AAD convidados**: O usuário convidado pertence a outra organização usando o Azure AD.
- **Convidados externos**: O usuário convidado pertence a uma organização externa que não tem Azure AD.


Um cenário em que isso é útil é quando a fonte de uma reclamação é diferente para um hóspede e um funcionário acessando um aplicativo. Você pode querer especificar que, se o usuário for um funcionário, o NameID é originário do user.email, mas se o usuário for um convidado, então o NameID é originado de user.extensionattribute1.

Para adicionar uma condição de reivindicação:

1. Em **Gerenciar a reivindicação,** expanda as condições de Sinistro.
2. Selecione o tipo de usuário.
3. Selecione os grupos a que o usuário deve pertencer. Você pode selecionar até 10 grupos exclusivos em todas as reivindicações para um determinado aplicativo. 
4. Selecione a **Fonte** onde a reivindicação recuperará seu valor. Você pode selecionar um atributo de usuário a partir do atributo de origem ou aplicar uma transformação ao atributo do usuário antes de emiti-lo como uma reivindicação.

A ordem em que você adiciona as condições são importantes. O Azure AD avalia as condições de cima para baixo para decidir qual valor emitimos na reivindicação. 

Por exemplo, Brita Simon é uma usuária convidada no inquilino contoso. Ela pertence a outra organização que também usa o Azure AD. Dada a configuração abaixo para o aplicativo Fabrikam, quando a Brita tenta entrar no Fabrikam, o Azure AD avaliará as condições da seguinte forma.

Primeiro, o Azure AD verifica se o `All guests`tipo de usuário da Brita é . Uma vez que isso é verdade, então o Azure AD atribui a fonte para a reivindicação de `user.extensionattribute1`. Em segundo lugar, o Azure AD verifica `AAD guests`se o tipo de usuário da Brita é , `user.mail`uma vez que isso também é verdade, então o Azure AD atribui a fonte para a reivindicação de . Finalmente, a reivindicação é `user.email` emitida com valor para a Grã-Cola.

![Configuração condicional de sinistros](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Próximas etapas

* [Gerenciamento de aplicativos no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar logon único para aplicativos que não estão na galeria de aplicativos do Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Solução de problemas de logon único baseado em SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
