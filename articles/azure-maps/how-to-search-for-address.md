---
title: Pesquisar um local usando os serviços de pesquisa do Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a procurar um local usando a Serviço de Pesquisa de mapas de Microsoft Azure para geocodificação e geocodificação geográfica.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335422"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Pesquisar um local usando os serviços de pesquisa do Azure Maps

O Azure Maps [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) é um conjunto de APIs RESTful projetado para ajudar os desenvolvedores a pesquisar endereços, locais, listagens de negócios por nome ou categoria e outras informações geográficas. Além de oferecer suporte à geocodificação tradicional, os serviços também podem reverter endereços geocódigo e entre ruas com base em latitudes e longitudes. Os valores de latitude e longitude retornados pela pesquisa podem ser usados como parâmetros em outros serviços do Azure Maps, como [rota](https://docs.microsoft.com/rest/api/maps/route) e serviços [meteorológicos](https://docs.microsoft.com/rest/api/maps/weather) .

Neste artigo, você aprenderá a:

* Solicitar coordenadas de latitude e longitude para um endereço (local de endereço geocódigo) usando a [API de endereço de pesquisa]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Procurar um endereço ou ponto de interesse (POI) usando a [API de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Pesquisar um endereço juntamente com propriedades e coordenadas
* Faça uma [pesquisa de endereço reverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para converter o local da coordenada em endereço
* Procurar uma cruzada usando o [endereço de pesquisa API de várias ruas inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, primeiro você precisa criar uma conta do Azure Maps e fazer com que você mapeie a chave de assinatura da conta. Siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps e siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocodificação)

Neste exemplo, estamos usando o Azure Maps [obter API de endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) para converter endereço de rua em coordenadas de latitude e longitude. Você pode passar um endereço de rua completo ou parcial para a API e receber uma resposta que inclui propriedades de endereço detalhadas, como rua, CEP e país/região, bem como valores posicionais na latitude e longitude.

Se você tiver um conjunto de endereços para geocódigo, poderá usar a [API do lote do endereço de pesquisa de postagem](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas em uma única chamada à API.

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a **pesquisa de endereço**.

2. Na guia Criador, selecione o método HTTP **GET**, insira a URL de solicitação para o ponto de extremidade de API e selecione um protocolo de autorização, se houver.

![Pesquisa de Endereço](./media/how-to-search-for-address/address_search_url.png)

| Parâmetro | Valor sugerido |
|---------------|------------------------------------------------| 
| Método HTTP | GET |
| URL de Solicitação | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorização | Sem autenticação |

3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação: 

![Pesquisa de Endereço](./media/how-to-search-for-address/address_search_params.png) 

| Chave | Valor | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<sua chave de mapas do Azure\> | 
| Consulta | Rua Cosme Velho, 513, Rio de Janeiro, 22241-090 | 

4. Clique em **Enviar** e analise o corpo da resposta. 

Nesse caso, você especificou uma consulta de endereço completo e recebe um único resultado no corpo da resposta. 

5. Em Params, edite a cadeia de consulta com o seguinte valor: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**: 

| Chave | Valor | 
|-----|------------| 
| typeahead | true | 

O sinalizador **typeahead** informa à API de Pesquisa de Endereço para tratar a consulta como uma entrada parcial e retornar uma matriz de valores de previsão.

## <a name="using-fuzzy-search-api"></a>Usando a API de pesquisa difusa

A[ API de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) do Azure Maps é um serviço recomendado para uso quando você não sabe quais são suas entradas de usuário para uma consulta de pesquisa. A API combina a pesquisa de POI (ponto de interesse) e geocodificação em uma ' pesquisa de linha única ' canônica. Por exemplo, a API pode manipular entradas de qualquer combinação de endereço ou token POI. Também pode ser ponderado com uma posição contextual (lat./lon. par), totalmente restrito por uma coordenada e raio, ou executado de forma mais geral sem qualquer ponto de ancoragem de polarização geográfica.

A maioria das consultas de pesquisa é padronizada para `maxFuzzyLevel=1` para obter desempenho e reduzir resultados incomuns. Esse padrão pode ser substituído, conforme necessário, por solicitação, passando o parâmetro de consulta `maxFuzzyLevel=2` ou `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Procurar um endereço usando a Pesquisa Difusa

1. Abra o aplicativo Postman, clique em Novo | Criar Novo e selecione **Solicitação GET**. Insira um nome de solicitação de **Pesquisa difusa**, selecione uma coleção ou uma pasta onde salvá-la e clique em **Salvar**.

2. Na guia Construtor, selecione o método HTTP **GET** e insira a URL de solicitação para o ponto de extremidade de API.

    ![Pesquisa Difusa](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorização | Sem autenticação |

    O atributo **json** no caminho da URL determina o formato da resposta. Este artigo usa o JSON para facilitar o uso e a legibilidade. Você pode encontrar os formatos de resposta disponíveis na **obter pesquisa difusa** definição da [referência da API de mapas funcionais](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:

    ![Pesquisa Difusa](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | Consulta | pizza |

4. Clique em **Enviar** e analise o corpo da resposta.

    A cadeia de caracteres de consulta ambígua para "pizza" retornou 10 [resultados de POI (ponto de interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) ) nas categorias "pizza" e "restaurante". Cada resultado retorna um endereço de rua, valores de latitude e longitude, porta de exibição e pontos de entrada para o local.
  
    Os resultados variam para essa consulta, não associados a nenhum local de referência específico. Você pode usar o parâmetro **countryset** para especificar apenas os países/regiões para os quais seu aplicativo precisa de cobertura. O comportamento padrão é Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários.

5. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |------------------|-------------------------|
    | countrySet | EUA |
  
    Os resultados agora são limitados pelo código do país e a consulta retorna as pizzarias no Brasil.
  
    Para fornecer resultados para um local, você pode consultar um ponto de interesse e usar os valores de latitude e longitude retornados em sua chamada para o serviço de pesquisa difusa. Nesse caso, você usou o Serviço de pesquisa para retornar a localização do Corcovado e usou os valores de lat. /long. para orientar a pesquisa.
  
6. Em Params, insira os seguintes pares Chave/Valor e clique em **Enviar**:

    ![Pesquisa Difusa](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chave | Valor |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procurar um endereço usando a Pesquisa Invertida de Endereço

A [API de obtenção de endereço de pesquisa]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) do Azure Maps ajuda a converter uma coordenada (exemplo: 37,786505,-122,3862) em um endereço de compreensão humana. Geralmente, isso é necessário no rastreamento de aplicativos em que você recebe um feed GPS do dispositivo ou ativo e deseja saber qual endereço em que a coordenada está localizada.
Se você tiver um conjunto de locais de coordenadas para reverter o código, você poderá usar a [API de postagem inversa do endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) para enviar um lote de consultas em uma única chamada à API.


1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a **pesquisa de endereço inversa**.

2. Na guia Construtor, selecione o método HTTP **GET** e insira a URL de solicitação para o ponto de extremidade de API.
  
    ![URL de Pesquisa Invertida de Endereço](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorização | Sem autenticação |
  
3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:
  
    ![Parâmetros de Pesquisa Invertida de Endereço](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | Consulta | 47.591180,-122.332700 |
  
4. Clique em **Enviar** e analise o corpo da resposta.

    A resposta inclui informações importantes sobre o endereço do Safeco Field.
  
5. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | número | true |

    Se o parâmetro de consulta [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) for enviado com a solicitação, a resposta poderá incluir o lado da rua (esquerda ou direita) e também uma posição de deslocamento para esse número.
  
6. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando o parâmetro de consulta [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta retorna o limite de velocidade lançado.

7. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnRoadUse | true |

    Quando o [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parâmetro de consulta for definido, a resposta retorna a matriz de uso de estrada para códigos geográficos reversos no nível da rua.

8. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | roadUse | true |

    Você pode restringir a consulta de geocódigo inversa a um tipo específico de estrada usando o parâmetro de consulta [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Pesquisar entre as ruas usando a pesquisa de postagem de endereço reverso

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a como **endereço inverso de pesquisa entre ruas**.

2. Na guia Construtor, selecione o método HTTP **GET** e insira a URL de solicitação para o ponto de extremidade de API.
  
    ![Pesquisa Invertida de Endereço de Cruzamento](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorização | Sem autenticação |
  
3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | Consulta | 47.591180,-122.332700 |
  
4. Clique em **Enviar** e analise o corpo da resposta.

## <a name="next-steps"></a>Próximas etapas

- Explore a [documentação da API REST do Azure Maps serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search).
- Saiba mais sobre o [Azure Maps serviço de pesquisa práticas recomendadas](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) e como otimizar suas consultas.
