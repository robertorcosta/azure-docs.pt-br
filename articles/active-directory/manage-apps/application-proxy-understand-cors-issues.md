---
title: Entenda e resolva problemas do Azure AD Application Proxy CORS
description: Fornece uma compreensão do CORS no Azure AD Application Proxy e como identificar e resolver problemas do CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025792"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Entenda e resolva problemas do Proxy do Aplicativo active do Azure

[O CORS (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada)](https://www.w3.org/TR/cors/) às vezes pode apresentar desafios para os aplicativos e APIs que você publica através do Proxy do Aplicativo de Diretório Ativo do Azure. Este artigo discute problemas e soluções do Azure AD Application Proxy CORS.

A segurança do navegador geralmente impede que uma página da Web esua solicitação ajax para outro domínio. Essa restrição é chamada *de política de mesma origem*e impede que um site mal-intencionado leia dados confidenciais de outro site. No entanto, às vezes você pode querer deixar outros sites chamarem sua API web. O CORS é um padrão W3C que permite que um servidor relaxe a política de mesma origem e permita algumas solicitações de origem cruzada enquanto rejeita outras.

## <a name="understand-and-identify-cors-issues"></a>Entenda e identifique problemas do CORS

Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos[(RFC 6454),](https://tools.ietf.org/html/rfc6454)tais como:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Os SEGUINTES URLs têm origens diferentes das duas anteriores:

-   http:\//contoso.net - Domínio diferente
-   http:\//contoso.com:9000/foo.html - Porto diferente
-   https:\//contoso.com/foo.html - Esquema diferente
-   http:\//www.contoso.com/foo.html - Subdomínio diferente

A política de mesma origem impede que os aplicativos acessem recursos de outras origens, a menos que usem os cabeçalhos de controle de acesso corretos. Se os cabeçalhos CORS estiverem ausentes ou incorretos, as solicitações de origem cruzada falharão. 

Você pode identificar problemas do CORS usando ferramentas de depuração do navegador:

1. Inicie o navegador e navegue até o aplicativo web.
1. Pressione **F12** para trazer o console de depuração.
1. Tente reproduzir a transação e rever a mensagem do console. Uma violação do CORS produz um erro no console sobre a origem.

Na captura de tela a seguir, a seleção do\/botão Tentar **É** causou uma mensagem de erro do CORS que https: /corswebclient-contoso.msappproxy.net não foi encontrado no cabeçalho Access-Control-Allow-Origin.

![Problema do CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Desafios do CORS com proxy de aplicativos

O exemplo a seguir mostra um cenário típico do Proxy Proxy do aplicativo Azure AD. O servidor interno hospeda um controlador de API web **CORSWebService** e um **CORSWebClient** que chama **CORSWebService**. Há uma solicitação AJAX do **CORSWebClient** para **o CORSWebService**.

![Solicitação de mesma origem no local](./media/application-proxy-understand-cors-issues/image1.png)

O aplicativo CORSWebClient funciona quando você o hospeda no local, mas não consegue carregar ou falhar quando publicado através do Azure AD Application Proxy. Se você publicou os aplicativos CORSWebClient e CORSWebService separadamente como aplicativos diferentes através do Proxy de aplicativos, os dois aplicativos serão hospedados em diferentes domínios. Uma solicitação AJAX do CORSWebClient para o CORSWebService é uma solicitação de origem cruzada e falha.

![Solicitação de proxy de aplicativo CORS](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluções para problemas de Proxy de Aplicativos CORS

Você pode resolver o problema cors anterior de qualquer uma das várias maneiras.

### <a name="option-1-set-up-a-custom-domain"></a>Opção 1: Configure um domínio personalizado

Use um [domínio personalizado](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) do Proxy do aplicativo Azure AD para publicar a partir da mesma origem, sem ter que fazer alterações nas origens do aplicativo, código ou cabeçalhos. 

### <a name="option-2-publish-the-parent-directory"></a>Opção 2: Publicar o diretório pai

Publique o diretório pai de ambos os aplicativos. Esta solução funciona especialmente bem se você tiver apenas dois aplicativos no servidor web. Em vez de publicar cada aplicativo separadamente, você pode publicar o diretório pai comum, o que resulta na mesma origem.

Os exemplos a seguir mostram a página proxy do aplicativo Azure AD para o aplicativo CORSWebClient.  Quando a **URL interna** é definida como *contoso.com/CORSWebClient,* o aplicativo não pode fazer solicitações bem-sucedidas para o diretório *contoso.com/CORSWebService,* porque eles são de origem cruzada. 

![Publicar aplicativo individualmente](./media/application-proxy-understand-cors-issues/image4.png)

Em vez disso, defina a **URL interna** para publicar o diretório pai, que inclui os diretórios *CORSWebClient* e *CORSWebService:*

![Publicar diretório pai](./media/application-proxy-understand-cors-issues/image5.png)

Os URLs do aplicativo resultantes resolvem efetivamente o problema do CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opção 3: Atualizar cabeçalhos HTTP

Adicione um cabeçalho de resposta HTTP personalizado no serviço web para corresponder à solicitação de origem. Para sites em execução no IIS (Internet Information Services, serviços de informação à Internet), use o IIS Manager para modificar o cabeçalho:

![Adicionar cabeçalho de resposta personalizado no IIS Manager](./media/application-proxy-understand-cors-issues/image6.png)

Esta modificação não requer nenhuma alteração de código. Você pode verificar isso nos traços do Violinista:

**Postar a adição de cabeçalho**\
HTTP/1.1 200 OK\
Controle de cache: sem cache\
Pragma: sem cache\
Tipo de conteúdo: texto/planície; charset=utf-8\
Expira: -1\
Variar: Aceita-codificação\
Servidor: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin:\:https //corswebclient-contoso.msappproxy.net**\
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Comprimento do conteúdo: 17

### <a name="option-4-modify-the-app"></a>Opção 4: Modifique o aplicativo

Você pode alterar seu aplicativo para suportar cors adicionando o cabeçalho Access-Control-Allow-Origin, com valores apropriados. A maneira de adicionar o cabeçalho depende da linguagem de código do aplicativo. Mudar o código é a opção menos recomendada, pois requer mais esforço.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opção 5: Prolongar a vida útil do token de acesso

Alguns problemas do CORS não podem ser resolvidos, como quando o aplicativo redireciona para *login.microsoftonline.com* para autenticar, e o token de acesso expira. A chamada do CORS falha. Uma solução para este cenário é estender a vida útil do token de acesso, para evitar que ele expire durante a sessão de um usuário. Para obter mais informações sobre como fazer isso, consulte [Vida útils de token configuráveis no Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Confira também
- [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativos no Diretório Ativo do Azure](application-proxy-add-on-premises-application.md) 
- [Planeje uma implantação de proxy de aplicativo AD do Azure](application-proxy-deployment-plan.md) 
- [Acesso remoto a aplicativos locais através do Proxy do Aplicativo de Diretório Ativo do Azure](application-proxy.md) 
