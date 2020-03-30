---
title: Render dados personalizados em um mapa de raster | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a renderizar dados personalizados em um mapa de rasterização usando o Serviço de imagem estática do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335510"
---
# <a name="render-custom-data-on-a-raster-map"></a>Renderizar dados personalizados em um mapa de raster

Este artigo explica como usar o [serviço de imagem estática,](https://docs.microsoft.com/rest/api/maps/render/getmapimage)com funcionalidade de composição de imagem, para permitir sobreposições em cima de um mapa de raster. A composição da imagem inclui a capacidade de obter um ladrilho rasterer de volta, com dados adicionais como pushpins personalizados, etiquetas e sobreposições de geometria.

Para renderizar pushpins, rótulos e sobreposições de geometria personalizadas, você pode usar o aplicativo Carteiro. Você pode usar [APIs do Azure Data Service](https://docs.microsoft.com/rest/api/maps/data) para armazenar e renderizar sobreposições.

> [!Tip]
> Muitas vezes é muito mais econômico usar o Azure Maps Web SDK para mostrar um mapa simples em uma página da Web do que usar o serviço de imagem estática. O Web SDK usa telhas de mapa e, a menos que o usuário pane e amplie o mapa, eles muitas vezes gerarão apenas uma fração de uma transação por carga de mapa. Observe que o Web SDK do Azure Maps tem opções para desativar panorâmicas e zoom. Além disso, o SDK web do Azure Maps fornece um conjunto mais rico de opções de visualização de dados do que um serviço web de mapa estático.  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para completar os procedimentos deste artigo, primeiro você precisa criar uma conta do Azure Maps e obter a chave da sua conta de mapas. Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps e siga os passos para obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal da sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderizar pushpins com etiquetas e uma imagem personalizada

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no nível de preços S0 ou S1.

O nível S0 do Azure Maps suporta `pins` apenas uma única instância do parâmetro. Ele permite renderizar até cinco pushpins, especificados na solicitação de URL, com uma imagem personalizada.

Para renderizar pushpins com etiquetas e uma imagem personalizada, complete estas etapas:

1. Crie uma coleção na qual armazenar os pedidos. No aplicativo Carteiro, selecione **Novo**. Na **janela Criar nova** janela, selecione **Coleção**. Nomeie a coleção e selecione o botão **Criar.** 

2. Para criar a solicitação, selecione **Novo** novamente. Na **janela Criar nova** janela, selecione **'Solicitar'.** Digite um **nome de solicitação** para os pinos. Selecione a coleção criada na etapa anterior, como o local para salvar a solicitação. Em seguida, **selecione Salvar**.
    
    ![Crie uma solicitação no Carteiro](./media/how-to-render-custom-data/postman-new.png)

3. Selecione o método GET HTTP na guia construtor e digite a SEGUINTE URL para criar uma solicitação GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Aqui está a imagem resultante:

    ![Um pushpin personalizado com um rótulo](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obter dados do armazenamento de dados do Azure Mapas

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no nível de preços S1.

Você também pode obter as informações de localização do caminho e do pino usando a [API de upload de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Siga as etapas abaixo para carregar o caminho e os dados dos marcadores.

1. No aplicativo Carteiro, abra uma nova guia na coleção que você criou na seção anterior. Selecione o método POST HTTP na guia do construtor e digite a seguinte URL para fazer uma solicitação post:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na **guia Params,** digite os seguintes pares de tecla/valor, que são usados para a URL de solicitação de POST. Substitua `subscription-key` o valor pela sua chave de assinatura do Azure Maps.
    
    ![Params de chave/valor em Carteiro](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na guia **Corpo,** selecione o formato de entrada bruta e escolha JSON como o formato de entrada da lista de saque. Forneça este JSON como dados a serem carregados:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selecione **Enviar** e revisar o cabeçalho de resposta. Após uma solicitação bem-sucedida, o cabeçalho Local conterá o URI de status para verificar o status atual da solicitação de upload. O status URI seria do seguinte formato.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Copie seu URI de status e apêndice o parâmetro de chave de assinatura a ele com o valor da sua chave de assinatura da conta do Azure Maps. Use a mesma chave de assinatura de conta que você usou para carregar os dados. O formato URI de status deve ser semelhante ao abaixo:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Para obter o udId, abra uma nova guia no aplicativo Carteiro. Selecione o método GET HTTP na guia construtor. Faça uma solicitação GET no URI de status. Se o upload de dados foi bem sucedido, você receberá um udId no órgão de resposta. Copie o udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Use `udId` o valor recebido da API de upload de dados para renderizar recursos no mapa. Para isso, abra uma nova guia na coleção criada na seção anterior. Selecione o método GET HTTP na guia do construtor, substitua a {assinatura-key} e {udId} por seus valores e digite esta URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Aqui está a imagem de resposta:

    ![Obter dados do armazenamento de dados do Azure Mapas](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderize um polígono com cor e opacidade

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no nível de preços S1.


É possível modificar a aparência de um polígono, usando modificadores de estilo com o [parâmetro de caminho](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. No aplicativo Carteiro, abra uma nova guia na coleção que você criou anteriormente. Selecione o método GET HTTP na guia construtor e digite a seguinte URL para configurar uma solicitação GET para renderizar um polígono com cor e opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderize um polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderizar um círculo e pinos com etiquetas personalizadas

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no nível de preços S1.


Você pode modificar a aparência dos pinos adicionando modificadores de estilo. Por exemplo, para fazer pushpins e seus rótulos `sc` maiores ou menores, use o modificador "estilo escala". Este modificador tem um valor maior que zero. Um valor de 1 é a escala padrão. Valores maiores que 1 tornarão os marcadores maiores e valores menores que 1 os tornarão menores. Para obter mais informações sobre modificadores de estilo, consulte [parâmetros de caminho de serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Siga estas etapas para renderizar um círculo e os pushpins com etiquetas personalizadas:

1. No aplicativo Carteiro, abra uma nova guia na coleção que você criou anteriormente. Selecione o método GET HTTP na guia construtor e digite esta URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderizar um círculo com pinos personalizados](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Para alterar a cor dos pinos da última etapa, altere o modificador de estilo "co". Veja, `pins=default|la15+50|al0.66|lc003C62|co002D62|`a cor atual seria especificada como #002D62 em CSS. Digamos que você queira mudar para #41d42a. Escreva o novo valor de cor após o `pins=default|la15+50|al0.66|lc003C62|co41D42A|`especificador "co", assim: . Faça uma nova solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta depois de alterar as cores dos pinos:

    ![Renderize um círculo com pushpins atualizados](./media/how-to-render-custom-data/circle-updated-pins.png)

Da mesma forma, você pode alterar, adicionar e remover outros modificadores de estilo.

## <a name="next-steps"></a>Próximas etapas


* Explore a documentação [Obter API de Imagem de Mapa do Azure Mapas](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Para saber mais sobre o Azure Maps Data Service, consulte a [documentação](https://docs.microsoft.com/rest/api/maps/data)do serviço .

