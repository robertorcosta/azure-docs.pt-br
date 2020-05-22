---
title: O que são Aplicativos Web Estáticos do Azure?
description: Os principais recursos e a funcionalidade dos Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: d416044599535e8acd363d09099e8667bba59a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599060"
---
# <a name="what-is-azure-static-web-apps-preview"></a>O que é a versão prévia do Aplicativos Web Estáticos do Azure?

O Aplicativos Web Estáticos do Azure é um serviço que compila e implanta automaticamente aplicativos Web de pilha completa no Azure de um repositório GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Visão geral de Aplicativos Web Estáticos":::

O fluxo de trabalho de Aplicativos Web Estáticos do Azure é personalizado para o fluxo de trabalho diário de um desenvolvedor. Os aplicativos são criados e implantados com base nas interações do GitHub.

Quando você cria um recurso no serviço Aplicativos Web Estáticos do Azure, o Azure configura um fluxo de trabalho do GitHub Actions no repositório de código-fonte do aplicativo que monitora um branch de sua escolha. Sempre que você envia confirmações por push ou aceita solicitações de pull para o branch observado, o GitHub Actions cria e implanta automaticamente seu aplicativo e sua API no Azure.

Normalmente, os aplicativos Web estáticos são criados usando bibliotecas e estruturas como Angular, React, Svelte ou Vue. Esses aplicativos incluem os HTML, CSS, JavaScript e ativos de imagem que compõem o aplicativo. Com um servidor Web tradicional, esses ativos são servidos de um único servidor juntamente com quaisquer pontos de extremidade de API necessários.

Com o Aplicativos Web Estáticos, os ativos estáticos são separados de um servidor Web tradicional e, em vez disso, são servidos de pontos geograficamente distribuídos pelo mundo. Essa distribuição torna o fornecimento de arquivos muito mais rápido, pois os arquivos estão fisicamente mais próximos dos usuários finais. Além disso, os pontos de extremidade da API são hospedados usando uma [arquitetura sem servidor](../azure-functions/functions-overview.md), o que evita a necessidade de um servidor back-end completo junto.

## <a name="key-features"></a>Principais recursos

- **Hospedagem gratuita na Web** para conteúdo estático, como HTML, CSS, JavaScript e imagens.
- Suporte para **API integrada** fornecido pelo Azure Functions.
- **A integração de GitHub interno** em que o repositório altera compilações e implantações de gatilho.
- Conteúdo estático **distribuído globalmente**, colocando o conteúdo mais perto dos usuários.
- **Certificados SSL gratuitos**, que são renovados automaticamente.
- **Domínios personalizados**\* para fornecer personalizações da marca no seu aplicativo.
- **Modelo de segurança contínua** com um proxy reverso ao chamar APIs, o que não requer nenhuma configuração de CORS.
- **Integrações do provedor de autenticação** com Azure Active Directory, Facebook, Google, GitHub e Twitter.
- **Definição personalizável de função de autorização** e atribuições.
- **Regras de roteamento de back-end** permitindo controle total sobre o conteúdo e as rotas que você atende.
- **Versões de preparo geradas** alimentadas por solicitações de pull que permitem versões prévias do site antes da publicação.

## <a name="what-you-can-do-with-static-web-apps"></a>O que você pode fazer com o serviço Aplicativos Web Estáticos

- **Criar aplicativos JavaScript modernos** com estruturas e bibliotecas como [Angular](https://angular.io/), [React](https://reactjs.org/), [Svelte](https://svelte.dev/) e [Vue](https://vuejs.org/), com um back-end do [Azure Functions](https://azure.microsoft.com/services/functions/).
- **Publicar sites estáticos** com estruturas como [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md) e [VuePress](publish-vuepress.md).
- **Implantar aplicativos Web** com estruturas como [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

\* Não há suporte para os registros de domínio Apex durante a versão prévia.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar seu primeiro aplicativo estático](getting-started.md)
