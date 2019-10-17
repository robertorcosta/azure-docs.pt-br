---
title: Opções de suporte e ajuda para desenvolvedores do de aplicativos do Azure AD | Microsoft Docs
description: Saiba como obter ajuda e suporte para problemas e dúvidas relacionados ao desenvolvimento ao criar aplicativos que se integram com as identidades da Microsoft (conta Azure Active Directory e Microsoft)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429971"
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para desenvolvedores

Se começar a integração ao Azure AD (Azure Active Directory), identidades da Microsoft ou API do Microsoft Graph ou quando estiver implementando um novo recurso no aplicativo, haverá vezes em que você precisará ter a ajuda da comunidade ou entender as opções de suporte que tem como desenvolvedor. Este artigo ajuda a entender essas opções, incluindo:

> [!div class="checklist"]
> * Como pesquisar para verificar se a pergunta não foi respondida pela comunidade ou se já existe uma documentação para o recurso que você está tentando implementar
> * Em alguns casos, você apenas deseja usar nossas ferramentas de suporte para ajudar a depurar um problema específico
> * Se não conseguir encontrar a resposta para o que você precisa, faça uma pergunta em *Stack Overflow*
> * Se você encontrar um problema em uma de nossas bibliotecas de autenticação, será gerado um problema do *GitHub*
> * Por fim, se precisar falar com alguém, talvez queira abrir uma solicitação de suporte

## <a name="search"></a>Pesquisa

Se tiver uma pergunta relacionada a desenvolvimento, poderá encontrar a resposta na documentação, nos [exemplos do GitHub](https://github.com/azure-samples) ou nas respostas às perguntas no [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Pesquisa com escopo

Para resultados mais rápidos, especifique sua pesquisa para o Stack Overflow, documentação e exemplos de código usando a seguinte consulta em seu mecanismo de pesquisa favorito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Em que *{Seus Termos de Pesquisa}* correspondem às palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Use as ferramentas de suporte de desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Cole uma ID ou token de acesso para decodificar os nomes das declarações e valores. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramentas que permitem fazer solicitações e ver as respostas na API do Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Postar uma pergunta no Stack Overflow

O Stack Overflow é o canal preferido para o desenvolvimento de questões relacionadas. Aqui, os membros da comunidade de desenvolvedores e da equipe da Microsoft estão diretamente envolvidos em ajudá-lo a resolver seus problemas.

Se não conseguir encontrar uma resposta para sua pergunta por meio da pesquisa, envie uma nova pergunta ao Stack Overflow. Use uma das seguintes marcas ao fazer perguntas para ajudar a comunidade a identificar e responder a pergunta com mais rapidez:

|Componente/área  | Marcas |
|---------|---------|
| Biblioteca ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteca MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [B2B do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [API do Microsoft Graph](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Qualquer outra área relacionada aos tópicos de autenticação ou autorização | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

As seguintes postagens do Stack Overflow contêm dicas sobre como fazer perguntas e adicionar o código-fonte. Siga essas diretrizes para aumentar as chances de os membros da comunidade avaliarem e responderem à sua pergunta rapidamente:

* [Como fazer uma boa pergunta](https://stackoverflow.com/help/how-to-ask)
* [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se encontrar um bug ou problema relacionado às nossas bibliotecas, gere um problema em nossos repositórios do GitHub. Como nossas bibliotecas são de software livre, você também pode enviar uma solicitação de pull.

Para obter uma lista de bibliotecas e seus repositórios GitHub, consulte o seguinte:

* Bibliotecas [ADAL](active-directory-authentication-libraries.md) e seus repositórios do GitHub
* [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. ](https://github.com/AzureAD/microsoft-authentication-library-for-android)Bibliotecas do Android e [MSAL. obj _C](https://github.com/AzureAD/microsoft-authentication-library-for-objc) e repositórios github

## <a name="open-a-support-request"></a>Abrir uma solicitação de suporte

Se precisar falar com alguém, poderá abrir uma solicitação de suporte. Se você for um cliente do Azure, há várias opções de suporte disponíveis. Para comparar os planos, confira [esta página](https://azure.microsoft.com/support/plans/). O suporte ao desenvolvedor também está disponível para os clientes do Azure. Para obter informações sobre como adquirir planos de suporte do Desenvolvedor, confira [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se você já tiver um Plano de Suporte do Azure, [abra uma solicitação de suporte aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se você não for um cliente do Azure, também poderá abrir uma solicitação de suporte com a Microsoft por meio do [nosso suporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Você também pode experimentar um [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou fazer perguntas.
