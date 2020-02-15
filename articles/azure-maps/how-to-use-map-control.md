---
title: Introdução ao controle de mapa da Web | Mapas do Microsoft Azure
description: Saiba como usar a biblioteca JavaScript do lado do cliente do controle de mapeamento do Microsoft Azure Maps para renderizar mapas e a funcionalidade do Azure Maps incorporada em seu aplicativo Web ou móvel.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6701d777fb9aa16d3012baba082415bf9858e46f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209810"
---
# <a name="use-the-azure-maps-map-control"></a>Usar o controle de mapeamento do Azure Maps

A biblioteca Javascript do lado do cliente do Controle de Mapeamento permite renderizar mapas e funcionalidade inserida nos Mapas do Azure em seu aplicativo Web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode inserir um mapa em uma página da Web usando a biblioteca Javascript do lado do cliente do Controle de Mapeamento.

1. Criar um novo arquivo HTML.

2. Carregar no SDK Web do Azure Mapas. Você pode escolher uma das duas opções;

a. Use a versão do CDN hospedada globalmente do SDK Web do Azure Mapas adicionando os pontos de extremidade da URL à folha de estilos e às referências de script no elemento `<head>` do arquivo:

```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
```

b. Carregue o código-fonte do SDK da Web do Azure Maps localmente usando o pacote [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM e hospede-o com seu aplicativo. Esse pacote também inclui definições de TypeScript.

> **NPM instalar Azure-Maps-Control**

Em seguida, adicione as referências à folha de estilos do Azure Mapas e referências de origem de script ao elemento `<head>` do arquivo:

```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
```

    >[!Note]
    > Typescript definitions can be imported into your application by adding the following code:
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

5. Para inicializar o controle de mapa, defina uma nova marca de script no corpo HTML. Passe o `id` do `<div>` de mapa ou um `HTMLElement` (por exemplo, `document.getElementById('myMap')`) como o primeiro parâmetro ao criar uma instância da classe `Map`. Use sua própria chave de conta do Azure Mapas ou as credenciais do AAD (Azure Active Directory) para autenticar o mapa usando as [opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

Se você precisar criar uma conta ou encontrar sua chave, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

A opção **linguagem** especifica a linguagem a ser usada nos rótulos e nos controles de mapa. Para obter mais informações sobre os idiomas com suporte, consulte [idiomas com suporte](supported-languages.md). Se você estiver usando uma chave de assinatura para autenticação, use o seguinte:

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

Se você estiver usando o Azure Active Directory (AAD) para autenticação, use o seguinte:

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
    
Para obter mais informações, consulte o documento [autenticação com o Azure Maps](azure-maps-authentication.md) e também os [exemplos de autenticação](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)do Azure AD do Azure Maps.

6. opcionalmente, você pode achar interessante adicionar os seguintes elementos meta tag ao início da sua página:

```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```

7. colocar todos juntos seu arquivo HTML deve ser semelhante ao seguinte código:

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

8. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado. Ele deve ser semelhante à imagem abaixo:

![Imagem do mapa mostrando resultado renderizado](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Como localizar o mapa

O mapas do Azure fornece duas maneiras diferentes de definir o idioma e a exibição regional para o mapa renderizado. A primeira opção é adicionar essas informações ao namespace global `atlas`, o que resultará em todas as instâncias de controle de mapa em seu aplicativo padronizando essas configurações. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa da seguinte maneira:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

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

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Suporte de nuvem do Azure governamental

O SDK da Web do Azure Maps dá suporte à nuvem do Azure governamental. Todas as URLs de JavaScript e CSS usadas para acessar o SDK da Web do Azure Maps permanecem as mesmas. As tarefas a seguir precisarão ser feitas para se conectar à versão de nuvem do Azure governamental da plataforma do Azure Maps.

Ao usar o controle de mapa interativo, adicione a seguinte linha de código antes de criar uma instância da classe `Map`. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de usar os detalhes de autenticação do Azure Maps da plataforma de nuvem do Azure governamental ao autenticar o mapa e os serviços.

Ao usar o módulo de serviços, o domínio para os serviços precisa ser definido ao criar uma instância de um ponto de extremidade de URL de API. Por exemplo, o código a seguir cria uma instância da classe `SearchURL` e aponta o domínio para a nuvem do Azure governamental.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se acessar diretamente os serviços REST do Azure Maps, altere o domínio da URL para `atlas.azure.us`. Por exemplo, se estiver usando o serviço de API de pesquisa, altere o domínio da URL de `https://atlas.microsoft.com/search/` para `https://atlas.azure.us/search/`.

## <a name="next-steps"></a>Próximas etapas

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
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)