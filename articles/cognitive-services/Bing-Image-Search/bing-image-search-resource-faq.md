---
title: FAQ (perguntas frequentes) - API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Encontre respostas para perguntas frequentes sobre conceitos, códigos e cenários relacionados à API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 19c5c5280b8ff4fb997366375b4300958d694d6c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293652"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>FAQ (perguntas frequentes) sobre a API de Pesquisa de Imagem do Bing

Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados à API de Pesquisa de Imagem do Bing para Serviços Cognitivos da Microsoft no Azure.

## <a name="response-headers-in-javascript"></a>Cabeçalhos de resposta em JavaScript

Os cabeçalhos a seguir podem ocorrer nas respostas da API de Pesquisa de Imagem do Bing.

| Atributo           | Descrição   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |A ID exclusiva que o Bing atribuiu ao usuário |
| `BingAPIs-Market`   |O mercado que foi usado para atender à solicitação |
| `BingAPIs-TraceId`  |A entrada de log no servidor da API do Bing para esta solicitação (para suporte) |

É particularmente importante persistir a ID do cliente e retorná-la com solicitações subsequentes. Quando você fizer isso, a pesquisa usará o contexto passado na classificação dos resultados da pesquisa e também fornecerá uma experiência de usuário consistente.

No entanto, quando você chama a API de Pesquisa de Imagem do Bing a partir do JavaScript, os recursos de segurança incorporados do navegador (CORS) podem impedi-lo de acessar os valores desses cabeçalhos.

Para obter acesso aos cabeçalhos, é possível fazer a solicitação da API de Pesquisa de Imagem do Bing por meio de um proxy CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra os cabeçalhos de resposta e os torna disponíveis para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo de tutorial](tutorial-bing-image-search-single-page-app.md) acesse os cabeçalhos opcionais do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, insira o comando a seguir em um prompt de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de extremidade API de Pesquisa de Imagem do Bing no arquivo HTML para: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Por fim, inicie o proxy CORS com o seguinte comando:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto você usa o aplicativo de tutorial, já que se fechar a janela irá parar o proxy. Na seção Cabeçalhos HTTP expansíveis abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta na produção

A abordagem do proxy CORS descrita na resposta anterior é apropriada para desenvolvimento, teste e aprendizado.

Em um ambiente de produção, no entanto, é possível hospedar um script do lado do servidor no mesmo domínio da página da Web que usa a API de Pesquisa na Web do Bing. Esse script deve, na verdade, fazer as chamadas da API mediante solicitação do JavaScript da página da Web e passar todos os resultados, incluindo cabeçalhos, de volta para o cliente. Como os dois recursos (página e script) compartilham uma origem, o CORS não funciona e os cabeçalhos especiais são acessíveis ao JavaScript na página da Web.

Essa abordagem também protege a chave de API da exposição ao público, já que apenas o script do lado do servidor precisa dela. O script pode usar outro método (como o referenciador HTTP) para garantir que a solicitação seja autorizada.

## <a name="next-steps"></a>Próximas etapas

A sua pergunta é sobre a falta de um recurso ou funcionalidade? Considere solicitar ou votar no nosso [site Voz do Usuário](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Confira também

 [Stack Overflow: Serviços Cognitivos](https://stackoverflow.com/questions/tagged/bing-api)
