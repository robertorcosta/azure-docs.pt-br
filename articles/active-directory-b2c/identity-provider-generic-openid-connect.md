---
title: Configure a inscrição e faça login com o OpenID Connect
titleSuffix: Azure AD B2C
description: Configure o login e o login com qualquer Provedor de identidade OpenID Connect (IdP) no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188249"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a OpenID Connect usando o Azure Active Directory B2C

[OpenID Connect](openid-connect.md) é um protocolo de autenticação construído em cima do OAuth 2.0 que pode ser usado para login seguro do usuário. A maioria dos provedores de identidade que usam este protocolo são suportados no Azure AD B2C. Este artigo explica como você pode adicionar provedores de identidade do OpenID Connect personalizados em seus fluxos de usuário.

## <a name="add-the-identity-provider"></a>Adicionar o provedor de identidade

1. Faça login no [portal Azure](https://portal.azure.com/) como o administrador global do seu inquilino Azure AD B2C.
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C clicando no filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **o novo provedor OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Configurar o provedor de identidade

Cada provedor de identidade Do OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para realizar o login. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect sempre está localizado em um ponto de extremidade que termina com `.well-known\openid-configuration`. Insira a URL de metadados do provedor de identidade do OpenID Connect que você deseja adicionar.

## <a name="client-id-and-secret"></a>ID do cliente e segredo

Para permitir que os usuários entrem, o provedor de identidade exige que os desenvolvedores registrem um aplicativo em seus serviços. Este aplicativo tem uma ID que é conhecida como o **ID do cliente** e um **segredo do cliente**. Copie esses valores do provedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, você deve inserir um segredo de cliente se quiser usar o fluxo de [código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que usa o segredo para trocar o código pelo token.

## <a name="scope"></a>Escopo

O escopo define as informações e as permissões que você deseja coletar do provedor de identidade personalizado. As solicitações do OpenID Connect precisam conter o valor de escopo do `openid` para receber o token de ID do provedor de identidade. Sem o token de ID, os usuários não conseguem entrar no Azure AD B2C usando o provedor de identidade personalizado. É possível acrescentar outros escopos separados por espaço. Veja a documentação do provedor de identidade personalizado para saber quais outros escopos podem estar disponíveis.

## <a name="response-type"></a>Tipo de resposta

O tipo de resposta descreve que tipo de informação é retornada na chamada inicial para o `authorization_endpoint` do provedor de identidade personalizado. Os seguintes tipos de resposta podem ser usados:

* `code`: conforme o [fluxo de código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), um código será retornado ao Azure AD B2C. O Azure AD B2C prossegue para chamar o `token_endpoint` para trocar o código pelo token.
* `id_token`: um token de ID é devolvido do provedor de identidade personalizado para o Azure AD B2C.

## <a name="response-mode"></a>Modo de resposta

O modo de resposta define o método que deve ser usado para retornar os dados do provedor de identidade personalizado para o Azure AD B2C. Os seguintes modos de resposta podem ser usados:

* `form_post`: esse modo de resposta é recomendado para obter a melhor segurança possível. A resposta é transmitida por meio do método HTTP `POST`, com o código ou o token codificado no corpo usando o formato `application/x-www-form-urlencoded`.
* `query`: o código ou o token é devolvido como um parâmetro de consulta.

## <a name="domain-hint"></a>Dica de domínio

A dica de domínio pode ser usada para pular diretamente para a página de entrada do provedor de identidade especificado, em vez de fazer com que o usuário faça uma seleção na lista de provedores de identidade disponíveis. Para permitir esse tipo de comportamento, insira um valor para a dica de domínio. Para pular para o provedor de identidade personalizado, acrescente o parâmetro `domain_hint=<domain hint value>` ao final da solicitação ao chamar o Azure AD B2C para entrar.

## <a name="claims-mapping"></a>Mapeamento de sinistros

Depois que o provedor de identidade personalizado envia um token de ID para o Azure AD B2C, o Azure AD B2C precisa ser capaz de mapear as declarações do token recebido para as declarações que ele reconhece e usa. Para cada um dos seguintes mapeamentos, veja a documentação do provedor de identidade personalizado para entender as declarações que são retornadas nos tokens do provedor de identidade:

* **ID do usuário**: Digite a reclamação que fornece o *identificador exclusivo* para o usuário conectado.
* **Nome da exibição**: Digite a reivindicação que fornece o nome de *exibição* ou *nome completo* para o usuário.
* **Nome dado**: Digite a reivindicação que fornece o *primeiro nome* do usuário.
* **Sobrenome**: Digite a reivindicação que fornece o *sobrenome* do usuário.
* **E-mail**: Digite a reivindicação que fornece o endereço de *e-mail* do usuário.
