---
title: Redirecionar as restrições de URL de resposta do URI & - plataforma de identidade da Microsoft | Azure
description: Responder urls/redirecionar as restrições & limitações do &
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656731"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Limitações e restrições de URL de resposta/URI de redirecionamento

Um URI redirecionado, ou URL de resposta, é o local para o local para o quais o servidor de autorização enviará ao usuário uma vez que o aplicativo tenha sido autorizado com sucesso e concedido um código de autorização ou token de acesso. O código ou token está contido no URI redirecionamento ou no token de resposta, por isso é importante que você registre o local correto como parte do processo de registro do aplicativo.

 As seguintes restrições se aplicam às URLs de resposta:

    * A URL de resposta `https`deve começar com o esquema .
    * A URL de resposta é sensível a maiúsculas e minúsculas. Seu estojo deve coincidir com o caso do caminho de URL do seu aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc`como parte `.../ABC/response-oidc` de seu caminho, não especifique na URL de resposta. Como o navegador da Web trata os `.../abc/response-oidc` caminhos como sensíveis a maiúsculas `.../ABC/response-oidc` e minúsculas, os cookies associados podem ser excluídos se redirecionados para a URL incompatível com o caso.
    
## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

A tabela a seguir mostra o número máximo de URIs de redirecionamento que você pode adicionar ao registrar seu aplicativo.

| Contas sendo assinadas | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Contas de trabalho ou escola da Microsoft em qualquer inquilino do Azure Active Directory (Azure AD) de qualquer organização | 256 | `signInAudience`o campo no manifesto de aplicativo está definido como *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Contas pessoais da Microsoft e contas de trabalho e escola | 100 | `signInAudience`campo no manifesto de aplicativo é definido *como AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Comprimento máximo de URI

Você pode usar um máximo de 256 caracteres para cada URI redirecionado que você adicionar a um registro de aplicativo.

## <a name="supported-schemes"></a>Esquemas suportados
O modelo de aplicativo Azure AD hoje suporta esquemas HTTP e HTTPS para aplicativos que assinam contas de trabalho ou escola da Microsoft em qualquer inquilino do Azure Active Directory (Azure AD) de qualquer organização. Ou `signInAudience` seja, o campo no manifesto do aplicativo está definido como *AzureADMyOrg* ou *AzureADMultipleOrgs*. Para os aplicativos que assinam contas pessoais da Microsoft `signInAudience` e contas de trabalho e escola (que é definida como *AzureADandPersonalMicrosoftAccount)* apenas o esquema HTTPS é permitido.

> [!NOTE]
> A nova experiência [de registros de aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs com o esquema HTTP na ui. A adição de URIs HTTP para aplicativos que assinam contas no trabalho ou na escola é suportada apenas através do editor de manifestos do aplicativo. Daqui para frente, novos aplicativos não poderão usar esquemas HTTP no URI de redirecionamento. No entanto, aplicativos mais antigos que contêm esquemas HTTP em URIs de redirecionamento continuarão a funcionar. Os desenvolvedores devem usar esquemas HTTPS nas URIs de redirecionamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrições usando um curinga em URIs

URIs curinga, `https://*.contoso.com`como , são convenientes, mas devem ser evitados. O uso de curingas no URI de redirecionamento tem implicações de segurança. De acordo com a especificação OAuth 2.0[(seção 3.1.2 do RFC 6749),](https://tools.ietf.org/html/rfc6749#section-3.1.2)um URI de ponto final de redirecionamento deve ser um URI absoluto. 

O modelo de aplicativo Azure AD não suporta URIs curinga para aplicativos que são configurados para assinar contas pessoais da Microsoft e contas de trabalho ou escola. No entanto, uris curinga são permitidos para aplicativos que são configurados para assinar contas de trabalho ou escola em um inquilino Azure AD de uma organização hoje. 
 
> [!NOTE]
> A nova experiência [de registros de aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs curinga na ui. A adição do WILCARD URI para aplicativos que assinam contas no trabalho ou na escola é suportada apenas através do editor de manifestos do aplicativo. Daqui para frente, novos aplicativos não poderão usar curingas no URI de redirecionamento. No entanto, aplicativos mais antigos que contêm curingas em URIs de redirecionamento continuarão a funcionar.

Se o seu cenário exigir mais URIs de redirecionamento do que o limite máximo permitido, em vez de adicionar um URI de redirecionamento curinga, considere a seguinte abordagem.

### <a name="use-a-state-parameter"></a>Use um parâmetro de estado

Se você tiver uma série de subdomínios e se o seu cenário exigir que você redirecione os usuários após autenticação bem-sucedida para a mesma página onde eles começaram, usar um parâmetro de estado pode ser útil. 

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicativo para processar os tokens de segurança que você recebe do ponto final da autorização.
1. Seu aplicativo pode enviar parâmetros específicos do aplicativo (como URL de subdomínio onde o usuário se originou ou qualquer coisa como informações de marca) no parâmetro de estado. Ao usar um parâmetro de estado, proteja contra a proteção CSRF conforme especificado na [seção 10.12 da RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Os parâmetros específicos do aplicativo incluirão todas as informações necessárias para que o aplicativo torne a experiência correta para o usuário, ou seja, construa o estado de aplicação apropriado. O ponto final de autorização do Azure AD tira HTML do parâmetro de estado, portanto, certifique-se de que você não está passando conteúdo HTML neste parâmetro.
1. Quando o Azure AD enviar uma resposta ao URI de redirecionamento "compartilhado", ele enviará o parâmetro de estado de volta para o aplicativo.
1. O aplicativo pode então usar o valor no parâmetro de estado para determinar para qual URL enviar o usuário ainda mais. Certifique-se de validar a proteção CSRF.

> [!NOTE]
> Essa abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro de estado, redirecionando assim o usuário para uma URL diferente, que é a [ameaça de redirector aberta](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita no RFC 6819. Portanto, o cliente deve proteger esses parâmetros criptografando o estado ou verificando-o por outros meios, como validar o nome de domínio no URI de redirecionamento contra o token.

## <a name="next-steps"></a>Próximas etapas

- Conheça o [manifesto do Aplicativo](reference-app-manifest.md)
