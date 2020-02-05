---
title: URI de redirecionamento & restrições de URL de resposta-plataforma de identidade da Microsoft | Azure
description: URLs de resposta/redirecionamento de restrições de URls & limitações
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 1367bf32eea58b828c00ee23a59a32a2fec699ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983088"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Limitações e restrições de URL de resposta/URI de redirecionamento

Um URI de redirecionamento, ou URL de resposta, é o local para o qual o servidor de autorização enviará o usuário quando o aplicativo tiver sido autorizado com êxito e receberá um código de autorização ou um token de acesso. O código ou o token está contido no URI de redirecionamento ou no token de resposta, portanto, é importante que você registre o local correto como parte do processo de registro do aplicativo.

 As seguintes restrições se aplicam a URLs de resposta:

    * A URL de resposta deve começar com o esquema `https`.
    * A URL de resposta diferencia maiúsculas de minúsculas. Seu caso deve corresponder ao caso do caminho da URL do seu aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos como diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` incompatível com maiúsculas e minúsculas.
    
## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

A tabela a seguir mostra o número máximo de URIs de redirecionamento que você pode adicionar ao registrar seu aplicativo.

| Contas sendo conectadas | Número máximo de URIs de redirecionamento | Description |
|--------------------------|---------------------------------|-------------|
| Contas corporativas ou de estudante da Microsoft no locatário do Azure Active Directory (Azure AD) de qualquer organização | 256 | `signInAudience` campo no manifesto do aplicativo é definido como *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Contas pessoais da Microsoft e contas corporativas e de estudante | 100 | `signInAudience` campo no manifesto do aplicativo é definido como *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Comprimento máximo do URI

Você pode usar um máximo de 256 caracteres para cada URI de redirecionamento que você adicionar a um registro de aplicativo.

## <a name="supported-schemes"></a>Esquemas com suporte
O modelo de aplicativo do Azure AD atualmente dá suporte a esquemas HTTP e HTTPS para aplicativos que entram em contas corporativas ou de estudante da Microsoft no locatário do Azure Active Directory (Azure AD) de qualquer organização. Esse é `signInAudience` campo no manifesto do aplicativo é definido como *AzureADMyOrg* ou *AzureADMultipleOrgs*. Para os aplicativos que entram em contas pessoais da Microsoft e em contas corporativas e de estudante (`signInAudience` definido como *AzureADandPersonalMicrosoftAccount*), somente o esquema HTTPS é permitido.

> [!NOTE]
> A nova experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs com esquema http na interface do usuário. A adição de URIs HTTP para aplicativos que entram em contas corporativas ou de estudante tem suporte apenas por meio do editor de manifesto de aplicativo. No futuro, novos aplicativos não poderão usar esquemas HTTP no URI de redirecionamento. No entanto, aplicativos mais antigos que contêm esquemas HTTP em URIs de redirecionamento continuarão a funcionar. Os desenvolvedores devem usar esquemas HTTPS nos URIs de redirecionamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrições usando um curinga em URIs

URIs curinga, como `https://*.contoso.com`, são convenientes, mas devem ser evitados. O uso de curingas no URI de redirecionamento tem implicações de segurança. De acordo com a especificação do OAuth 2,0 ([seção 3.1.2 da RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), um URI de ponto de extremidade de redirecionamento deve ser um URI absoluto. 

O modelo de aplicativo do Azure AD não dá suporte a URIs curinga para aplicativos configurados para entrar em contas pessoais da Microsoft e contas corporativas ou de estudante. No entanto, URIs curinga são permitidos para aplicativos que estão configurados para entrar em contas corporativas ou de estudante no locatário do Azure AD de uma organização hoje. 
 
> [!NOTE]
> A nova experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs curinga na interface do usuário. A adição de URI curinga para aplicativos que entram em contas corporativas ou de estudante tem suporte apenas por meio do editor de manifesto de aplicativo. No futuro, novos aplicativos não poderão usar curingas no URI de redirecionamento. No entanto, os aplicativos mais antigos que contêm curingas em URIs de redirecionamento continuarão a funcionar.

Se seu cenário exigir mais URIs de redirecionamento do que o limite máximo permitido, em vez de adicionar um URI de redirecionamento curinga, considere uma das abordagens a seguir.

### <a name="use-a-state-parameter"></a>Usar um parâmetro de estado

Se você tiver um número de subdomínios e se seu cenário exigir que você redirecione os usuários após a autenticação bem-sucedida na mesma página em que eles foram iniciados, o uso de um parâmetro de estado pode ser útil. 

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicativo para processar os tokens de segurança que você recebe do ponto de extremidade de autorização.
1. Seu aplicativo pode enviar parâmetros específicos do aplicativo (como a URL de subdomínio onde o usuário originou ou qualquer coisa como informações de identidade visual) no parâmetro State. Ao usar um parâmetro de estado, proteja-se contra proteção CSRF conforme especificado na [seção 10,12 da RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Os parâmetros específicos do aplicativo incluirão todas as informações necessárias para que o aplicativo processe a experiência correta para o usuário, ou seja, construa o estado do aplicativo apropriado. O ponto de extremidade de autorização do Azure AD retira o HTML do parâmetro de estado, portanto, certifique-se de que você não está passando o conteúdo HTML nesse parâmetro.
1. Quando o Azure AD envia uma resposta para o URI de redirecionamento "compartilhado", ele envia o parâmetro de estado de volta para o aplicativo.
1. Em seguida, o aplicativo pode usar o valor no parâmetro State para determinar a URL para a qual enviar o usuário. Certifique-se de validar para proteção do CSRF.

> [!NOTE]
> Essa abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro de estado, redirecionando, assim, o usuário para uma URL diferente, que é a [ameaça de redirecionamento aberta](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita na RFC 6819. Portanto, o cliente deve proteger esses parâmetros criptografando o estado ou verificando-o por outros meios, como validar o nome de domínio no URI de redirecionamento em relação ao token.

### <a name="add-redirect-uris-to-service-principals"></a>Adicionar URIs de redirecionamento às entidades de serviço

Outra abordagem é adicionar URIs de redirecionamento às [entidades de serviço](app-objects-and-service-principals.md#application-and-service-principal-relationship) que representam o registro do aplicativo em qualquer locatário do Azure AD. Você pode usar essa abordagem quando não pode usar um parâmetro de estado ou seu cenário requer que você adicione novos URIs de redirecionamento ao registro do aplicativo para cada novo locatário ao qual dá suporte. 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [manifesto do aplicativo](reference-app-manifest.md)
