---
title: Restrições de URI de redirecionamento (URL de resposta) | Azure
titleSuffix: Microsoft identity platform
description: Uma descrição das restrições e limitações do formato URI de redirecionamento (URL de resposta) imposto pela plataforma de identidade da Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: bd6f88db2b55a5f0f445659e4b5ef609d3e146e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030303"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Restrições e limitações do URI de redirecionamento (URL de resposta)

Um URI de redirecionamento, ou URL de resposta, é o local onde o servidor de autorização envia o usuário depois que o aplicativo é autorizado com êxito e recebe um código de autorização ou um token de acesso. O servidor de autorização envia o código ou o token para o URI de redirecionamento, portanto, é importante que você registre o local correto como parte do processo de registro do aplicativo.

 As restrições a seguir se aplicam a URLs de redirecionamento:

* O URI de redirecionamento deve começar com o esquema `https` .

* O URI de redirecionamento diferencia maiúsculas de minúsculas. As letras maiúsculas e minúsculas devem corresponder às letras maiúsculas e minúsculas do caminho da URL do aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc` , não especifique `.../ABC/response-oidc` no URI de redirecionamento. Como o navegador da Web trata os caminhos diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` com maiúsculas e minúsculas não correspondentes.

## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

Esta tabela mostra o número máximo de URIs de redirecionamento que você pode adicionar a um registro de aplicativo na plataforma de identidade da Microsoft.

| Contas sendo conectadas | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Contas corporativas ou de estudante da Microsoft no locatário do Azure Active Directory (Azure AD) de qualquer organização | 256 | O campo `signInAudience` no manifesto do aplicativo é definido como *AzureADMyOrg* ou *AzureADMultipleOrgs*. |
| Contas pessoais e contas corporativas e de estudante da Microsoft | 100 | O campo `signInAudience` no manifesto do aplicativo é definido como *AzureADandPersonalMicrosoftAccount*. |

## <a name="maximum-uri-length"></a>Tamanho máximo do URI

Você pode usar um máximo de 256 caracteres para cada URI de redirecionamento que você adicionar a um registro de aplicativo.

## <a name="supported-schemes"></a>Esquemas compatíveis

O modelo de aplicativo Azure Active Directory (Azure AD) atualmente dá suporte a esquemas HTTP e HTTPS para aplicativos que entram em contas corporativas ou de estudante no locatário do Azure AD de qualquer organização. Esses tipos de conta são especificados pelos `AzureADMyOrg` `AzureADMultipleOrgs` valores e no `signInAudience` campo do manifesto do aplicativo. Para aplicativos que entram em contas pessoais da Microsoft (MSA) *e* contas corporativas e de estudante (ou seja, o `signInAudience` é definido como `AzureADandPersonalMicrosoftAccount` ), somente o esquema HTTPS é permitido.

Para adicionar URIs de redirecionamento com um esquema HTTP aos registros de aplicativo que entram em contas corporativas ou de estudante, você precisa usar o editor de manifesto do aplicativo no [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) no portal do Azure. No entanto, embora seja possível definir um URI de redirecionamento baseado em HTTP usando o editor de manifesto, é *altamente* recomendável que você use o esquema HTTPS para seus URIs de redirecionamento.

## <a name="localhost-exceptions"></a>Exceções de localhost

De acordo com as [seções RFC 8252 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) e [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), os URIs de redirecionamento "Loopback" ou "localhost" vêm com duas considerações especiais:

1. `http` Os esquemas de URI são aceitáveis porque o redirecionamento nunca deixa o dispositivo. Assim, ambos são aceitáveis:
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. Devido a intervalos de porta efêmeras geralmente exigidos por aplicativos nativos, o componente de porta (por exemplo, `:5001` ou `:443` ) é ignorado para fins de correspondência de um URI de redirecionamento. Como resultado, todos eles são considerados equivalentes:
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

Do ponto de vista do desenvolvimento, isso significa algumas coisas:

* Não registre vários URIs de redirecionamento onde apenas a porta difere. O servidor de logon escolherá um arbitrariamente e usará o comportamento associado a esse URI de redirecionamento (por exemplo, se for `web` -, `native` -ou `spa` -Type Redirecionado).
* Se você precisar registrar vários URIs de redirecionamento no localhost para testar fluxos diferentes durante o desenvolvimento, diferencie-os usando o componente de *caminho* do URI. Por exemplo, `http://127.0.0.1/MyWebApp` não corresponde `http://127.0.0.1/MyNativeApp` .
* Por diretrizes da RFC, você não deve usar `localhost` o no URI de redirecionamento. Em vez disso, use o endereço IP de loopback real, `127.0.0.1` . Isso impede que seu aplicativo seja interrompido por firewalls configurados incorretamente ou por interfaces de rede renomeadas.

    Para usar o `http` esquema com o endereço de loopback (127.0.0.1) em vez de localhost, você deve editar o [manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#replyurls-attribute). 

    O endereço de loopback IPv6 ( `[::1]` ) não tem suporte no momento.

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Restrições em curingas em URIs de redirecionamento

URIs de curinga como `https://*.contoso.com` podem parecer convenientes, mas devem ser evitados devido a implicações de segurança. De acordo com a especificação OAuth 2.0 ([seção 3.1.2 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), um URI de ponto de extremidade de redirecionamento deve ser um URI absoluto.

Atualmente, não há suporte para URIs curinga em registros de aplicativo configurados para entrar em contas pessoais da Microsoft e contas corporativas ou de estudante. Os URIs curinga são permitidos, no entanto, para aplicativos configurados para entrar somente em contas corporativas ou de estudante no locatário do Azure AD de uma organização.

Para adicionar URIs de redirecionamento com caracteres curinga aos registros de aplicativo que entram em contas corporativas ou de estudante, você precisa usar o editor de manifesto do aplicativo no [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) no portal do Azure. Embora seja possível definir um URI de redirecionamento com um curinga usando o editor de manifesto, é *altamente* recomendável aderir à [seção 3.1.2 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) e usar apenas URIs absolutos.

Se seu cenário exigir mais URIs de redirecionamento do que o limite máximo permitido, considere a [abordagem a seguir](#use-a-state-parameter) , em vez de adicionar um URI de redirecionamento de caractere curinga.

### <a name="use-a-state-parameter"></a>Usar um parâmetro de estado

Se você tiver vários subdomínios e seu cenário exigir que, após a autenticação bem-sucedida, você redirecione os usuários para a mesma página da qual eles foram iniciados, o uso de um parâmetro de estado pode ser útil.

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicativo para processar os tokens de segurança que você recebe do ponto de extremidade da autorização.
1. Seu aplicativo pode enviar parâmetros específicos do aplicativo (como a URL do subdomínio em que o usuário originou ou qualquer coisa como informações de identidade visual) no parâmetro State. Ao usar um parâmetro de estado, considere a proteção contra CSRF, como especificado na [seção 10.12 da RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)).
1. Os parâmetros específicos do aplicativo incluirão todas as informações necessárias para que o aplicativo processe a experiência correta para o usuário, ou seja, construa o estado apropriado do aplicativo. O ponto de extremidade da autorização do Azure AD retira o HTML do parâmetro de estado; portanto, certifique-se de que você não está passando o conteúdo HTML nesse parâmetro.
1. Quando o Azure AD envia uma resposta para o URI de redirecionamento "compartilhado", ele envia o parâmetro de estado de volta para o aplicativo.
1. Assim, o aplicativo pode usar o valor do parâmetro de estado para determinar a URL de destino do usuário. Não se esqueça de garantir a proteção contra CSRF.

> [!WARNING]
> Essa abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro de estado, redirecionando, assim, o usuário para uma URL diferente, que é a [ameaça de redirecionamento aberta](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita na RFC 6819. Portanto, o cliente deve proteger esses parâmetros criptografando o estado ou verificando-o por outros meios, como validar o nome de domínio no URI de redirecionamento em relação ao token.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [manifesto do aplicativo](reference-app-manifest.md)de registro de aplicativo.
