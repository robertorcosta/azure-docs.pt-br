---
title: Fluxos dos usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre a estrutura de política extensível do Azure Active Directory B2C e como criar diversos fluxos dos usuários.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185600"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos dos usuários no Azure Active Directory B2C

A estrutura de política extensível da Azure Active Directory B2C (Azure AD B2C) é a força principal do serviço. As políticas descrevem em detalhe experiências de identidade, tais como inscrição, entrada ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos dos usuários**.

## <a name="what-are-user-flows"></a>O que são fluxos dos usuários?

Um fluxo de usuário de inscrição permite controlar comportamentos em aplicativos definindo as seguintes configurações:

- Tipos de conta usados para entrar, por exemplo, contas sociais como as do Facebook ou locais
- Atributos a serem coletados do consumidor, tais como nome, código postal e tamanho do calçado
- Autenticação Multifator do Azure
- Personalização da interface do usuário
- Informações de que o aplicativo recebe como declarações em um token

É possível criar muitos fluxos dos usuários de diferentes tipos no locatário e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos. Essa flexibilidade permite definir e modificar experiências de identidade com pouca ou nenhuma alteração no seu código. O aplicativo dispara um fluxo de usuário usando uma solicitação de autenticação HTTP padrão que inclui um parâmetro de fluxo de usuário. Um [token](tokens-overview.md) personalizado é recebido como uma resposta.

Os exemplos a seguir mostram o parâmetro de cadeia de caracteres de consulta "p" que especifica o fluxo de usuário a ser usado:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Versões de fluxo do usuário

No portal do Azure, novas [versões dos fluxos dos usuários](user-flow-versions.md) estão sendo adicionadas o tempo todo. Quando você começa a usar o Azure AD B2C, os fluxos dos usuários testados são recomendados para seu uso. Quando cria um novo fluxo de usuário, você escolhe o fluxo de usuário que precisa na guia **Recomendado**.

Atualmente, os seguintes fluxos dos usuários são recomendados:

- **Inscrever-se e entrar** – controla as experiências de inscrição e entrada do consumidor com uma única configuração. Os consumidores são conduzidos pelo caminho certo, de acordo com o contexto. É recomendável usar esse fluxo de usuário em um fluxo de usuário de **inscrição** ou de **entrada**.
- **Edição de perfil** – permite que os usuários editem suas informações de perfil.
- **Redefinição de senha** – permite configurar se e como os usuários podem redefinir sua senha.

## <a name="linking-user-flows"></a>Vinculação de fluxos dos usuários

Um fluxo de usuário de **inscrição ou de entrada** com contas locais inclui um link **Esqueceu a senha?** na primeira página da experiência. Clicar nesse link não dispara automaticamente um fluxo de usuário de redefinição de senha.

Em vez disso, o código de erro `AADB2C90118` é retornado para seu aplicativo. Seu aplicativo precisa lidar com esse código de erro, executando um fluxo de usuário específico que redefine a senha. Para ver um exemplo, dê uma olhada em um [exemplo simples de ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstra a vinculação de fluxos dos usuários.

## <a name="email-address-storage"></a>Armazenamento de endereços de email

Um endereço de email pode ser exigido como parte de um fluxo de usuário. Se o usuário é autenticado com um provedor de identidade social, o endereço de email é armazenado na propriedade **otherMails**. Se uma conta local for baseada em um nome de usuário, o endereço de email será armazenado em uma propriedade de detalhe de autenticação forte. Se uma conta local for baseada em um endereço de email, ele será armazenado na propriedade **signInNames**.

Não há garantia de que o endereço de email seja verificado em nenhum desses casos. Um administrador de locatários pode desabilitar a verificação de email nas políticas básicas para contas locais. Mesmo se a verificação de endereço de email estiver habilitada, endereços não serão verificados se eles vierem de um provedor de identidade social e não tiverem sido alterados.

Somente as propriedades **otherMails** e **signInNames** são expostas por meio da API Microsoft Graph. O endereço de email na propriedade de detalhe de autenticação forte não está disponível.

## <a name="next-steps"></a>Próximas etapas

Para criar os fluxos de usuário recomendados, siga as instruções em [tutorial: criar um fluxo de usuário](tutorial-create-user-flows.md).
