---
title: Introdução ao controle de mapa da Web no Azure Maps | Microsoft Docs
description: Saiba como usar a biblioteca JavaScript do lado do cliente do controle de mapeamento do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c764460fb65287dab0031b27291d2f332cec3b3d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756543"
---
# <a name="use-the-azure-maps-map-control"></a>Usar o controle de mapeamento do Azure Maps

A biblioteca Javascript do lado do cliente do Controle de Mapeamento permite renderizar mapas e funcionalidade inserida nos Mapas do Azure em seu aplicativo Web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode inserir um mapa em uma página da Web usando a biblioteca Javascript do lado do cliente do Controle de Mapeamento.

1. Criar um novo arquivo HTML.

2. Carregar no SDK Web do Azure Mapas. Isso pode ser feito usando uma de duas opções:

    a. Use a versão do CDN hospedada globalmente do SDK Web do Azure Mapas adicionando os pontos de extremidade da URL à folha de estilos e às referências de script no elemento `<head>` do arquivo:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Como alternativa, carregue o código-fonte do SDK Web do Azure Mapas localmente usando o pacote NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) e hospede-o com seu aplicativo. Esse pacote também inclui definições de TypeScript.

    > npm install azure-maps-control

    Em seguida, adicione as referências à folha de estilos do Azure Mapas e referências de origem de script ao elemento `<head>` do arquivo:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > As definições do typescript podem ser importadas para seu aplicativo adicionando:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Para renderizar o mapa de modo que ele preencha a página inteira, adicione o seguinte elemento `<style>` ao elemento `<head>`.

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. No corpo da página, adicione um elemento `<div>` e dê a ele um `id` de **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Para inicializar o Controle de Mapeamento, defina uma nova seção no corpo html e crie um script. Passe o `id` do `<div>` de mapa ou um `HTMLElement` (por exemplo, `document.getElementById('myMap')`) como o primeiro parâmetro ao criar uma instância da classe `Map`. Use sua própria chave de conta do Azure Mapas ou as credenciais do AAD (Azure Active Directory) para autenticar o mapa usando as [opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se você precisar criar uma conta ou encontrar sua chave, consulte [Como gerenciar sua conta e chaves do Azure Maps](how-to-manage-account-keys.md). A opção **linguagem** especifica a linguagem a ser usada nos rótulos e nos controles de mapa. Para obter mais informações sobre linguagens com suporte, confira [Linguagens com suporte](supported-languages.md). Se estiver usando uma chave de assinatura para autenticação.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Se estiver usando o AAD (Azure Active Directory) para autenticação:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    Uma lista de exemplos que mostram como integrar Azure Active Directory (AAD) com mapas do Azure pode ser encontrada [aqui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
    Para obter mais informações, consulte o documento [autenticação com o Azure Maps](azure-maps-authentication.md) .

6. Como opção, você poderá considerar útil adicionar os seguintes elementos de marcação meta ao cabeçalho da sua página:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Colocar tudo em conjunto em seu arquivo HTML deve ser semelhante ao seguinte código:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado. Ele deve ser semelhante ao seguinte código:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Como usar o controle de mapeamento" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>como usar o controle de mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'> @azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Localizando o mapa

O mapas do Azure fornece duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace global `atlas`, o que resultará em todas as instâncias de controle de mapa em seu aplicativo padronizando essas configurações. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa, como:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Com o SDK da Web, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, essas configurações podem ser atualizadas depois que o mapa é carregado usando a função `setStyle` do mapa. 

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr-FR" e a exibição regional definida como "auto".

![Imagem do mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Uma lista completa de idiomas com suporte e exibições regionais está documentada [aqui](supported-languages.md).

## <a name="next-steps"></a>Próximos passos

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como definir o estilo de um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Para obter uma lista de exemplos que mostram como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do AAD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)