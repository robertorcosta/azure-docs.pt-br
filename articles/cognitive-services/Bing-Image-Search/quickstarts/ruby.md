---
title: 'Início Rápido: pesquisar imagens usando a API REST de Pesquisa de Imagem do Bing e Ruby'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações de pesquisa de imagens para a API REST de Pesquisa de Imagem do Bing usando Ruby e receba respostas JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: ee1308f08b5ca2192bb429acbb5bdaec199fb26f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85603258"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-ruby"></a>Início Rápido: pesquisar imagens usando a API REST de Pesquisa de Imagem do Bing e Ruby

Use este início rápido para fazer sua primeira chamada à API de Pesquisa de Imagem do Bing e receber uma resposta JSON. Este aplicativo simples do Ruby envia uma consulta de pesquisa para a API e exibe os resultados brutos.

Embora esse aplicativo seja escrito no Ruby, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingImageSearchv7.rb).

## <a name="prerequisites"></a>Pré-requisitos

* [A versão mais recente do Ruby](https://www.ruby-lang.org/en/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Para obter mais informações, confira [Preço dos Serviços Cognitivos – API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Importe os seguintes pacotes em seu arquivo de código:

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. Crie variáveis para o ponto de extremidade da API, caminho de pesquisa da API de imagem, sua chave de assinatura e o termo de pesquisa. Para `uri`, você pode usar o ponto de extremidade global no código a seguir ou usar o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```ruby
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/images/search"
    term = "puppies"
    ```

## <a name="format-and-make-an-api-request"></a>Formatar e fazer uma solicitação de API

Use as variáveis da etapa anterior para formatar uma URL de pesquisa para a solicitação da API. Depois, envie a solicitação.

```ruby
uri = URI(uri + path + "?q=" + URI.escape(term))


request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end
```

## <a name="process-and-print-the-json"></a>Processar e imprimir o JSON

Depois que a resposta é recebida, você pode analisar o JSON e obter valores dele. Por exemplo, a URL em miniatura para o primeiro resultado e o número total de imagens retornadas.

```ruby
response.each_header do |key, value|
    # header names are lowercased
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

parsed_json = JSON.parse(response.body)
total_returned_images = parsed_json["totalEstimatedMatches"]
first_result = parsed_json["value"][0]["thumbnailUrl"]

puts "total number of returned matches: #{total_returned_images}"
puts "Url to the thumbnail of the first returned search result: #{first_result}"
```

## <a name="example-json-response"></a>Resposta JSON de exemplo

As respostas da API de Pesquisa de Imagem do Bing são retornadas como JSON. Este exemplo de resposta foi truncado para mostrar um único resultado.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Confira também

* [O que é a API de Pesquisa de Imagem do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)   
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
