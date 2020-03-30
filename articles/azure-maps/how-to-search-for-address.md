---
title: Procure um local usando os serviços de pesquisa do Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a procurar um local usando o Serviço de Pesquisa do Microsoft Azure Maps para geocodificação e geocodificação reversa.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335422"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Procure um local usando os serviços de pesquisa do Azure Maps

O Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) é um conjunto de APIs RESTful projetadas para ajudar os desenvolvedores a pesquisar endereços, lugares, listagens de negócios por nome ou categoria e outras informações geográficas. Além de suportar a geocodificação tradicional, os serviços também podem reverter endereços de geocódigo e atravessar ruas com base em latitudes e longitudes. Os valores de latitude e longitude retornados pela pesquisa podem ser usados como parâmetros em outros serviços do Azure Maps, como serviços [de Rota](https://docs.microsoft.com/rest/api/maps/route) e [Clima.](https://docs.microsoft.com/rest/api/maps/weather)

Neste artigo você aprenderá, como:

* Solicite coordenadas de latitude e longitude para um endereço (localização do endereço de geocódigo) usando [a API de endereço de pesquisa]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Procure um endereço ou Ponto de Interesse (POI) usando [a API de pesquisa fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Procure um endereço junto com propriedades e coordenadas
* Faça uma [pesquisa de endereço reverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para traduzir o local de coordenadas para o endereço da rua
* Procure por uma rua transversal usando [a API de Rua De Frente Reversa de Endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para completar as etapas deste artigo, você precisa primeiro criar uma conta do Azure Maps e obter a chave de assinatura da conta. Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps e siga os passos para obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal da sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocodificação)

Neste exemplo, estamos usando a [API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) Azure Maps Get Search Address para converter o endereço de rua em coordenadas de latitude e longitude. Você pode passar um endereço de rua completo ou parcial para a API e receber uma resposta que inclui propriedades de endereço detalhadas, como rua, código postal e país/região, bem como valores posicionais em latitude e longitude.

Se você tiver um conjunto de endereços para geocode, você pode usar [a API do lote de endereço de pesquisa pós-poste](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas em uma única chamada de API.

1. Em Carteiro, clique em **Nova solicitação** | **OBTENHA solicitação** e nomeie-a Pesquisa de **endereços**.

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

## <a name="using-fuzzy-search-api"></a>Usando a API de pesquisa fuzzy

A Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) é recomendado para usar quando você não sabe quais são as entradas do usuário para uma consulta de pesquisa. A API combina pesquisa de Ponto de Interesse (POI) e geocodificação em uma "pesquisa de linha única" canônica. Por exemplo, a API pode manipular entradas de qualquer combinação de endereço ou token POI. Também pode ser ponderado com uma posição contextual (lat./lon. par), totalmente restrito por uma coordenada e raio, ou executado de forma mais geral sem qualquer ponto de ancoragem de polarização geográfica.

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

    O atributo **json** no caminho da URL determina o formato da resposta. Este artigo usa json para facilitar o uso e legibilidade. Você pode encontrar os formatos de resposta disponíveis na **obter pesquisa difusa** definição da [referência da API de mapas funcionais](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:

    ![Pesquisa Difusa](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | Consulta | pizza |

4. Clique em **Enviar** e analise o corpo da resposta.

    A corda de consulta ambígua para "pizza" retornou 10 [pontos de juros resultado](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) nas categorias "pizza" e "restaurante". Cada resultado retorna um endereço de rua, valores de latitude e longitude, porta de exibição e pontos de entrada para a localização.
  
    Os resultados variam para essa consulta, não associados a nenhum local de referência específico. Você pode usar o parâmetro **countrySet** para especificar apenas os países/regiões para os quais seu aplicativo precisa de cobertura. O comportamento padrão é pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários.

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

Mapas do Azure [Obter endereço de pesquisa A API reversa]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) ajuda a traduzir uma coordenada (exemplo: 37.786505, -122.3862) em um endereço de rua humano e compreensível. Na maioria das vezes, isso é necessário no rastreamento de aplicativos onde você recebe um feed GPS do dispositivo ou ativo e deseja saber qual endereço onde a coordenada está localizada.
Se você tiver um conjunto de locais de coordenadas para reverter o geocódigo, você pode usar [a API de lote reverso do pós-pesquisa para](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) enviar um lote de consultas em uma única chamada de API.


1. Em Carteiro, clique em **Nova solicitação** | **OBTENHA solicitação** e nomeie-a Pesquisa de endereço **reverso**.

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

    Se o parâmetro de consulta [numérica](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) for enviado com a solicitação, a resposta pode incluir o lado da rua (esquerda ou direita) e também uma posição de deslocamento para esse número.
  
6. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando o parâmetro de consulta [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta retorna o limite de velocidade postado.

7. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnRoadUse | true |

    Quando o [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parâmetro de consulta for definido, a resposta retorna a matriz de uso de estrada para códigos geográficos reversos no nível da rua.

8. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | roadUse | true |

    Você pode restringir a consulta de geocódigo reverso a um tipo específico de estrada usando o parâmetro de consulta [roadUse.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Busca por rua transversal usando reverse address cross street search

1. Em Carteiro, clique em **Nova solicitação** | **OBTENHA solicitação** e nomeie-a De endereço **inverso Pesquisa de Rua Cross**.

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

- Explorar a documentação da [API do Azure Maps Search Service](https://docs.microsoft.com/rest/api/maps/search).
- Saiba mais sobre as [práticas recomendadas do Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) e como otimizar suas consultas.
