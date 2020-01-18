---
title: Pesquisar locais usando o Azure Maps Serviço de Pesquisa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a procurar um local usando os mapas de Microsoft Azure Serviço de Pesquisa.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 53856b4157afa5976947c451952fc26eefcdd0ea
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264179"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Encontrar um endereço usando o serviço de pesquisa do Azure Maps

O serviço de pesquisa de mapas é um conjunto de APIs RESTful projetado para desenvolvedores. O serviço pode procurar endereços, locais, pontos de interesse, listas de negócios e outras informações geográficas. Cada um dos itens a seguir tem valores de latitude e Longitude: um endereço específico, uma cruz, um recurso geográfico ou um POI (ponto de interesse). Você pode usar os valores de latitude e longitude retornados de uma consulta como parâmetros em outros serviços de mapa. Por exemplo, os valores retornados podem se tornar parâmetros para o serviço de rota ou o serviço de fluxo de tráfego. 

Vamos aprender, como:

* Pesquisar um endereço usando a [API de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Pesquisar um endereço juntamente com propriedades e coordenadas
* Fazer uma [pesquisa de endereço inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para pesquisar um endereço
* Procurar uma cruzada usando o [endereço de pesquisa API de várias ruas inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs de serviço do Maps, você precisa de uma conta e uma chave do Maps. Para fazer uma conta para o Azure Maps, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps). Siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), se precisar de ajuda para obter a chave primária. Para obter mais informações sobre autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.

## <a name="using-fuzzy-search"></a>Usando a Pesquisa Difusa

A API padrão para o serviço de pesquisa é a [pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Esse serviço é útil quando você não tem certeza do formato de entrada do usuário em uma consulta de pesquisa. A API combina pesquisa e geo codificação de POI em uma 'pesquisa de linha única' canônica. Por exemplo, a API pode manipular entradas de qualquer combinação de endereço ou token POI. Além disso, ele pode ser ponderado com uma posição contextual (lat./Lon. par), totalmente restrito por uma coordenada e raio, ou executado de forma mais geral sem qualquer ponto de ancoragem de polarização geográfica.

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

## <a name="search-for-address-properties-and-coordinates"></a>Pesquisar propriedades e coordenadas de endereço

Você pode passar um endereço de rua completo ou parcial para a API de endereço de pesquisa. Você ainda receberá uma resposta que inclui propriedades de endereço detalhadas. As propriedades de endereço detalhadas são valores como valores posicionais em altitude e longitude, município ou subdivisão.

1. No Postman, clique em **Nova Solicitação** | **Solicitação GET** e nomeie-a **Pesquisa de Endereço**.
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

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procurar um endereço usando a Pesquisa Invertida de Endereço

1. No Postman, clique em **Nova Solicitação** | **Solicitação GET** e nomeie-a **Pesquisa Invertida de Endereço**.

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

    Você pode restringir a consulta de geocódigo inversa a um tipo específico de estrada, use o parâmetro de consulta [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Pesquise o cruzamento usando Pesquisa Invertida de Endereço de Cruzamento

1. No Postman, clique em **Nova Solicitação** | **Solicitação GET** e nomeie-a **Pesquisa Invertida de Endereço de Cruzamento**.

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

## <a name="next-steps"></a>Próximos passos

- Explore a documentação da [API do serviço de pesquisa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search).
