---
title: Restrições de URI de redirecionamento e URL de resposta - plataforma de identidade da Microsoft | Azure
description: Restrições e limitações de URLs de resposta/URIs de redirecionamento
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/17/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 4fdeb0018e27a2557161b2ec1c4794d975403523
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311612"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Limitações e restrições de URL de resposta/URI de redirecionamento

Um URI de redirecionamento, ou URL de resposta, é o local para o qual o servidor de autorização enviará o usuário quando o aplicativo for autorizado e receber um código de autorização ou um token de acesso. O código ou token está contido no URI de redirecionamento ou no token de resposta; portanto, é importante que você registre o local correto como parte do processo de registro do aplicativo.

 As restrições a seguir se aplicam a URLs de resposta:

* A URL de resposta deve começar com o esquema `https`.

* A URL de resposta diferencia maiúsculas de minúsculas. As letras maiúsculas e minúsculas devem corresponder às letras maiúsculas e minúsculas do caminho da URL do aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc` como parte de seu caminho, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` com maiúsculas e minúsculas não correspondentes.
    
## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

A tabela a seguir mostra o número máximo de URIs de redirecionamento que você pode adicionar ao registrar seu aplicativo.

| Contas sendo conectadas | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Contas corporativas ou de estudante da Microsoft no locatário do Azure Active Directory (Azure AD) de qualquer organização | 256 | O campo `signInAudience` no manifesto do aplicativo é definido como *AzureADMyOrg* ou *AzureADMultipleOrgs*. |
| Contas pessoais e contas corporativas e de estudante da Microsoft | 100 | O campo `signInAudience` no manifesto do aplicativo é definido como *AzureADandPersonalMicrosoftAccount*. |

## <a name="maximum-uri-length"></a>Tamanho máximo do URI

Você pode usar no máximo de 256 caracteres para cada URI de redirecionamento que adicionar a um registro de aplicativo.

## <a name="supported-schemes"></a>Esquemas compatíveis

O modelo de aplicativo do Azure AD atualmente é compatível com esquemas HTTP e HTTPS para aplicativos que acessam contas corporativas ou de estudante da Microsoft no locatário do Azure Active Directory (Azure AD) de qualquer organização. Esse é o campo `signInAudience` no manifesto do aplicativo definido como *AzureADMyOrg* ou *AzureADMultipleOrgs*. Para os aplicativos que acessa, contas pessoais da Microsoft e contas corporativas e de estudante (ou seja, `signInAudience` definido como *AzureADandPersonalMicrosoftAccount*), somente o esquema HTTPS é permitido.

> [!NOTE]
> A nova experiência de [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs com o esquema HTTP na interface do usuário. A adição de URIs HTTP em aplicativos que acessam contas corporativas ou de estudante é compatível apenas por meio do editor de manifesto de aplicativo. Futuramente, novos aplicativos não poderão usar esquemas HTTP no URI de redirecionamento. No entanto, aplicativos mais antigos que contenham esquemas HTTP em URIs de redirecionamento continuarão a funcionar. Os desenvolvedores devem usar esquemas HTTPS nos URIs de redirecionamento.

## <a name="localhost-exceptions"></a>Exceções de localhost

De acordo com as [seções RFC 8252 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) e [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), os URIs de redirecionamento "Loopback" ou "localhost" vêm com duas considerações especiais:

1. `http`Os esquemas de URI são aceitáveis, pois o redirecionamento nunca deixa o dispositivo.  Isso significa que `http://127.0.0.1/myApp` é aceitável, bem como `https://127.0.0.1/myApp` . 
1. Devido a intervalos de porta efêmeras geralmente necessários para aplicativos nativos, o componente de porta (por exemplo `:5001` , ou `:443` ) é ignorado para fins de correspondência de um URI de redirecionamento.  Como resultado, `http://127.0.0.1:5000/MyApp` e `http://127.0.0.1:1234/MyApp` ambas as correspondências, `http://127.0.0.1/MyApp` bem como`http://127.0.0.1:8080/MyApp`

Do ponto de vista do desenvolvimento, isso significa algumas coisas:

1. Não registre vários URIs de resposta onde apenas a porta difere.  O servidor de logon escolherá um arbitrariamente e usará o comportamento associado a esse URI de resposta (por exemplo, se for um `web` `native` `spa` redirecionamento, e-Type.
1. Se você precisar registrar vários URIs de redirecionamento no localhost para testar fluxos diferentes durante o desenvolvimento, diferencie-os usando o componente de *caminho* do URI.  `http://127.0.0.1/MyWebApp`não corresponde `http://127.0.0.1/MyNativeApp` .  
1. Por diretrizes da RFC, você não deve usar `localhost` o no URI de redirecionamento.  Em vez disso, use o endereço IP de loopback real- `127.0.0.1` . Isso impede que seu aplicativo seja interrompido por firewalls configurados incorretamente ou por interfaces de rede renomeadas.

>[!NOTE]
> Neste momento, o loopback IPv6 ( `[::1]` ) não tem suporte no momento.  Isso será adicionado em uma data posterior.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrições usando um curinga em URIs

URIs com curinga, como `https://*.contoso.com`, são convenientes, mas devem ser evitados. O uso de curingas no URI de redirecionamento tem implicações de segurança. De acordo com a especificação OAuth 2.0 ([seção 3.1.2 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), um URI de ponto de extremidade de redirecionamento deve ser um URI absoluto.

O modelo de aplicativo do Azure AD não é compatível com URIs curinga em aplicativos configurados para acessar contas pessoais da Microsoft e contas corporativas ou de estudante. No entanto, atualmente, URIs com curinga são permitidos em aplicativos configurados para acessar contas corporativas ou de estudante no locatário do Azure AD de uma organização.

> [!NOTE]
> A nova experiência de [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs com curinga na interface do usuário. A adição de URIs com curinga em aplicativos que acessam contas corporativas ou de estudante é compatível apenas por meio do editor de manifesto de aplicativo. Futuramente, novos aplicativos não poderão usar curingas no URI de redirecionamento. No entanto, aplicativos mais antigos que contenham curingas em URIs de redirecionamento continuarão a funcionar.

Se o seu cenário exigir mais URIs de redirecionamento do que o limite máximo permitido, em vez de adicionar um URI de redirecionamento com curinga, considere a abordagem a seguir.

### <a name="use-a-state-parameter"></a>Usar um parâmetro de estado

Se você tiver um número de subdomínios e se o seu cenário exigir que redirecione os usuários após a autenticação bem-sucedida para a mesma página em que eles foram iniciados, o uso de um parâmetro de estado pode ser útil.

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicativo para processar os tokens de segurança que você recebe do ponto de extremidade da autorização.
1. Seu aplicativo pode enviar parâmetros específicos do aplicativo (como a URL de subdomínio de origem do usuário ou qualquer coisa, como informações de identidade visual) no parâmetro de estado. Ao usar um parâmetro de estado, considere a proteção contra CSRF, como especificado na [seção 10.12 da RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Os parâmetros específicos do aplicativo incluirão todas as informações necessárias para que o aplicativo processe a experiência correta para o usuário, ou seja, construa o estado apropriado do aplicativo. O ponto de extremidade da autorização do Azure AD retira o HTML do parâmetro de estado; portanto, certifique-se de que você não está passando o conteúdo HTML nesse parâmetro.
1. Quando o Azure AD envia uma resposta para o URI de redirecionamento "compartilhado", ele envia o parâmetro de estado de volta para o aplicativo.
1. Assim, o aplicativo pode usar o valor do parâmetro de estado para determinar a URL de destino do usuário. Não se esqueça de garantir a proteção contra CSRF.

> [!NOTE]
> Essa abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro de estado, redirecionando, assim, o usuário para uma URL diferente, que é a [ameaça de redirecionamento aberta](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita na RFC 6819. Portanto, o cliente deve proteger esses parâmetros criptografando o estado ou verificando-o por outros meios, como validar o nome de domínio no URI de redirecionamento em relação ao token.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [manifesto do aplicativo](reference-app-manifest.md).
