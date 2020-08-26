---
title: 'Início Rápido: Criar um índice de pesquisa em Python usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Explica como criar um índice, carregar dados e executar consultas usando Python, Jupyter Notebooks e a API REST da Pesquisa Cognitiva do Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: aa2357e31bf2fba97ae8547948cacdffc70cc741
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705003"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Início Rápido: Criar um índice da Pesquisa Cognitiva do Azure em Python usando Jupyter notebooks

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portal](search-get-started-portal.md)
> 

Crie um Jupyter notebook que cria, carrega e consulta um índice da Pesquisa Cognitiva do Azure usando Python e as [APIs REST da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/). Este artigo explica como criar um passo a passo do notebook. Como alternativa, você pode [baixar e executar um notebook Python Jupyter concluído](https://github.com/Azure-Samples/azure-search-python-samples).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e as ferramentas a seguir são necessários para este início rápido. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), que fornece o Python 3.x e os Notebooks Jupyter.

+ [Crie um serviço da Pesquisa Cognitiva do Azure](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar a camada gratuita deste início rápido. 

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="connect-to-azure-cognitive-search"></a>Conectar-se à Pesquisa Cognitiva do Azure

Nesta tarefa, inicie um Jupyter notebook e verifique se você pode se conectar à Pesquisa Cognitiva do Azure. Você fará isso solicitando uma lista de índices de seu serviço. No Windows com Anaconda3, você pode usar o Anaconda Navigator para iniciar um notebook.

1. Crie um notebook Python3.

1. Na primeira célula, carregue as bibliotecas usadas para trabalhar com JSON e formular solicitações HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Na segunda célula, insira os elementos de solicitação que serão constantes em cada solicitação. Substitua o nome do serviço de pesquisa (NOME-DO-SERVIÇO-DE-PESQUISA) e a chave de API de administração (CHAVE-DE-API-DE-ADMINISTRAÇÃO) por valores válidos. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2020-06-30'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Se você receber ConnectionError `"Failed to establish a new connection"`, verifique se a chave de API é uma chave de administração primária ou secundária e se todos os caracteres à esquerda e à direita (`?` e `/`) estão no lugar.

1. Na terceira célula, formule a solicitação. Essa solicitação GET direciona a coleção de índices do serviço de pesquisa e seleciona a propriedade de nome dos índices existentes.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Execute cada etapa. Se existirem índices, a resposta conterá uma lista de nomes de índice. Na captura de tela abaixo, o serviço já tem um índice azureblob-index e um realestate-us-sample.

   ![Script de Python no Jupyter notebook com solicitações HTTP para a Pesquisa Cognitiva do Azure](media/search-get-started-python/connect-azure-search.png "Script de Python no Jupyter notebook com solicitações HTTP para a Pesquisa Cognitiva do Azure")

   Por outro lado, uma coleção de índice vazia retorna esta resposta: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que você esteja usando o portal, deve haver um índice no serviço antes que você possa carregar dados. Esta etapa usa a [API REST Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) para enviar por push um esquema de índice para o serviço.

Os elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A coleção de campos define a estrutura de um *documento*. Cada campo tem um nome, tipo e atributos que determinam como o campo é usado (por exemplo, se for pesquisável de texto completo, filtrável ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` deve ser designado como a *chave* para a identidade do documento.

Esse índice é denominado "hotels-quickstart" e tem as definições de campo que você vê abaixo. É um subconjunto de um [Índice de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outros passo a passos. Nós o cortamos neste guia de início rápido para fins de brevidade.

1. Na próxima célula, cole o exemplo a seguir em uma célula para fornecer o esquema. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Em outra célula, formule a solicitação. Essa solicitação POST direciona a coleção de índices de seu serviço de pesquisa e cria um índice com base no esquema de índice fornecido na célula anterior.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Execute cada etapa.

   A resposta inclui a representação JSON do esquema. A captura de tela a seguir está mostrando apenas uma parte da resposta.

    ![Solicitação para criar um índice](media/search-get-started-python/create-index.png "Solicitação para criar um índice")

> [!Tip]
> Outra maneira de verificar a criação do índice é verificar a lista de índices no portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Para efetuar push de documentos, use uma solicitação HTTP POST para o ponto de extremidade de URL do seu índice. A API REST é [Adicionar, Atualizar ou Excluir Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Os documentos são originados de [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no GitHub.

1. Em uma nova célula, forneça quatro documentos em conformidade com o esquema de índice. Especifique uma ação de upload para cada documento.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Em outra célula, formule a solicitação. Essa solicitação POST direciona a coleção de documentos do índice hotels-quickstart e efetua push dos documentos fornecidos na etapa anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Execute cada etapa para efetuar push dos documentos para um índice em seu serviço de pesquisa. Os resultados devem ser semelhantes ao exemplo a seguir. 

    ![Enviar documentos para um índice](media/search-get-started-python/load-index.png "Enviar documentos para um índice")

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Esta etapa mostra como consultar um índice usando a [API REST Pesquisar Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Em uma célula, forneça uma expressão de consulta que executa uma pesquisa vazia (search=*), retornando uma lista não classificada (pontuação de pesquisa = 1,0) de documentos arbitrários. Por padrão, a Pesquisa Cognitiva do Azure retorna 50 correspondências por vez. Como estruturada, essa consulta retorna uma estrutura e valores do documento inteiro. Adicione $count=true para obter uma contagem de todos os documentos nos resultados.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Em uma nova célula, forneça o seguinte exemplo para pesquisar nos termos “hotéis” e “Wi-Fi”. Adicione $select para especificar quais campos incluir nos resultados da pesquisa.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   Os resultados devem ser semelhantes à saída a seguir. 

    ![Pesquisar um índice](media/search-get-started-python/search-index.png "Pesquisar um índice")

1. Em seguida, aplique uma expressão $filter que selecione apenas os hotéis com uma classificação maior que 4. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Por padrão, o mecanismo de pesquisa retorna os 50 principais documentos, mas você pode usar as opções Início e Ignorar para adicionar uma paginação e escolher quantos documentos são exibidos em cada resultado. Essa consulta retorna dois documentos em cada conjunto de resultados.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Neste último exemplo, use $orderby para classificar os resultados por cidade. Este exemplo inclui campos da coleção Endereços.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>Limpar

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Como uma simplificação, este início rápido usa uma versão abreviada do índice Hotels. É possível criar a versão completa para tentar consultas mais interessantes. Para obter a versão completa e todos os 50 documentos, execute o assistente **Importar dados**, selecionando *hotels–sample* nas fontes de dados de exemplo internas.

> [!div class="nextstepaction"]
> [Início Rápido: Criar um índice no portal do Azure](search-get-started-portal.md)
