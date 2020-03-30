---
title: Começando com o controle do mapa web | Mapas do Microsoft Azure
description: Aprenda a usar a biblioteca JavaScript do controle de mapa do Microsoft Azure Maps para renderizar mapas e incorporar a funcionalidade do Azure Maps em seu aplicativo web ou móvel.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335242"
---
# <a name="use-the-azure-maps-map-control"></a>Use o controle do mapa do Azure Maps

A biblioteca JavaScript do lado do cliente do Controle de Mapas permite que você renderizamapas e incorpore a funcionalidade do Azure Maps em seu aplicativo web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode incorporar um mapa em uma página da Web usando a biblioteca JavaScript do lado do cliente do Controle de Mapas.

1. Criar um novo arquivo HTML.

2. Carregar no SDK Web do Azure Mapas. Você pode escolher uma das duas opções;

    * Use a versão CDN hospedada globalmente do Azure Maps Web SDK adicionando referências ao JavaScript e à folha de estilos no `<head>` elemento do arquivo HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Carregue o código-fonte do Azure Maps Web SDK localmente usando o pacote NPM [de controle de mapas azure](https://www.npmjs.com/package/azure-maps-control) e hospede-o com o seu aplicativo. Esse pacote também inclui definições de TypeScript.

        > **npm install azure-maps-control**

       Em seguida, adicione as referências à folha de estilos do Azure Mapas e referências de origem de script ao elemento `<head>` do arquivo:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Definições de script de digitação podem ser importadas para o seu aplicativo adicionando o seguinte código:
    >
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

5. Para inicializar o controle do mapa, defina uma nova tag de script no corpo html. Passe no `id` mapa `<div>` ou `HTMLElement` um (por `document.getElementById('myMap')`exemplo) como o primeiro parâmetro `Map` ao criar uma instância da classe. Use sua própria chave de conta do Azure Mapas ou as credenciais do AAD (Azure Active Directory) para autenticar o mapa usando as [opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Se você precisar criar uma conta ou encontrar sua chave, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   A opção **linguagem** especifica a linguagem a ser usada nos rótulos e nos controles de mapa. Para obter mais informações sobre idiomas suportados, consulte [idiomas suportados](supported-languages.md). Se você estiver usando uma chave de assinatura para autenticação, use o seguinte:

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

   Se você estiver usando o AAD (AAD) Active Directory (AAD) para autenticação, use o seguinte:

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

   Uma lista de amostras mostrando como integrar o Azure Active Directory (AAD) com o Azure Maps pode ser encontrada [aqui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Para obter mais informações, consulte o documento [Autenticação com o Azure Maps](azure-maps-authentication.md) e também as [amostras de autenticação Azure Maps Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Como opção, você poderá considerar útil adicionar os seguintes elementos de marcação meta ao cabeçalho da sua página:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Juntando tudo o seu arquivo HTML deve parecer algo como o seguinte código:

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

8. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado. Deve se parecer com a imagem abaixo:

   ![Imagem do mapa mostrando resultado renderizado](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Como localizar o mapa

O Azure Maps fornece duas maneiras diferentes de definir o idioma e a visualização regional para o mapa renderizado. A primeira opção é adicionar essas `atlas` informações ao namespace global, o que resultará em todas as instâncias de controle de mapa em seu aplicativo padrão a essas configurações. O seguinte define o idioma para francês ("fr-FR") e a visão regional para "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa assim:

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
> Com o Web SDK é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, essas configurações podem ser atualizadas `setStyle` após as cargas do mapa usando a função do mapa. 

Aqui está um exemplo do Azure Maps com o idioma definido como "fr-FR" e a exibição regional definida como "Auto".

![Imagem do mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Apoio à nuvem do governo azure

O Azure Maps Web SDK suporta a nuvem do Governo Azure. Todos os JAVAScript e CSS URLs usados para acessar o Azure Maps Web SDK permanecem os mesmos. As seguintes tarefas precisarão ser feitas para se conectar à versão em nuvem do Azure Government da plataforma Azure Maps.

Ao usar o controle de mapa interativo, adicione a `Map` seguinte linha de código antes de criar uma instância da classe. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de usar os detalhes de autenticação do Azure Maps da plataforma de nuvem do Azure Government ao autenticar o mapa e os serviços.

Ao usar o módulo de serviços, o domínio para os serviços precisa ser definido ao criar uma instância de um ponto final de URL da API. Por exemplo, o código a `SearchURL` seguir cria uma instância da classe e aponta o domínio para a nuvem do Governo Azure.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se acessar diretamente os serviços Azure Maps `atlas.azure.us`REST, altere o domínio url para . Por exemplo, se usar o serviço de API de pesquisa, altere o domínio url de `https://atlas.microsoft.com/search/` para `https://atlas.azure.us/search/`.

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

Para obter uma lista de amostras mostrando como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Amostras de autenticação Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
