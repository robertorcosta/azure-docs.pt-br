---
title: 'Tutorial: Implementar análise espacial de IoT com o Microsoft Azure Mapas'
description: Integre o Hub IoT às APIs de serviço do Microsoft Azure Mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4150464b5c59b631afea0c788b1e351dee5185f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299331"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: Implementar análise espacial de IoT usando o Azure Mapas

Em um cenário de IoT, é comum capturar e acompanhar eventos relevantes que ocorrem no espaço e no tempo. Os cenários de exemplo incluem aplicativos de gerenciamento de frota, acompanhamento de ativos, mobilidade e cidade inteligente. Este tutorial guia você por uma solução que, usando as APIs dos Azure Mapas, acompanha a movimentação carros de aluguel usados.

Neste tutorial, você vai:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure para registrar em log os dados de acompanhamento de carros.
> * Carregar uma cerca geográfica no Serviço de Dados do Azure Mapas usando a API de Upload de Dados.
> * Criar um Hub IoT e registrar um dispositivo.
> * Crie uma função no Azure Functions implementando a lógica de negócios com base na análise espacial do Azure Mapas.
> * Assine eventos de telemetria do dispositivo de IoT usando a função Azure por meio da Grade de Eventos.
> * Filtre os eventos de telemetria usando o roteamento de mensagens do Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com).

2. [Crie uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

4. [Crie um grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). Neste tutorial, nomeamos nosso grupo de recursos *ContosoRental*, mas você pode escolher qualquer nome que desejar.

5. Baixe o projeto [rentalCarSimulation em C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="use-case-rental-car-tracking"></a>Caso de uso: acompanhamento de carros de aluguel

Este tutorial demonstra o seguinte cenário: Uma empresa de aluguel de carros deseja registrar informações de localização, distância viajada e estado de funcionamento dos próprios carros de aluguel. Além disso, a empresa deseja armazenar essas informações sempre que um carro deixa a região geográfica autorizada correta.

Em nosso caso de uso, os carros alugados estão equipados com dispositivos IoT que enviam dados telemétricos regularmente para o Hub IoT do Azure. A telemetria inclui a localização atual e indica se o motor do carro está ligado. O esquema de localização do dispositivo adere ao [esquema de Plug and Play para dados geoespaciais](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md) de IoT. O esquema de telemetria do dispositivo do carro alugado é semelhante ao seguinte código JSON:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Neste tutorial, acompanharemos apenas um veículo. Depois de configurarmos os serviços do Azure, você precisará baixar o [projeto rentalCarSimulation em C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) para executar o simulador de veículo. O processo inteiro, desde o evento até a execução da função, é resumido nas seguintes etapas:

1. O dispositivo no veículo envia dados telemétricos ao Hub IoT.

2. Se o motor do carro estiver ligado, o Hub IoT publicará os dados telemétricos na Grade de Eventos.

3. Uma função do Azure é disparada devido à respectiva assinatura de evento para eventos de telemetria do dispositivo.

4. A função registrará as coordenadas de localização do dispositivo do veículo, a hora do evento e a identificação do dispositivo. Em seguida, ele usará a [API de Obtenção de Cerca Geográfica Espacial](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) para determinar se o carro foi dirigido para fora da cerca geográfica. Se ele estiver fora dos limites da cerca geográfica, a função armazenará os dados de localização recebidos do evento em nosso contêiner de blob. Além disso, nossa função consulta a [Pesquisa de Endereço Reverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para converter a localização das coordenadas em um endereço de rua e armazená-lo com o restante dos dados de localização do dispositivo.

O diagrama a seguir apresenta uma visão geral de alto nível do sistema.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Visão geral do sistema":::

A figura a seguir mostra a área de cerca geográfica realçada em azul. A rota do carro de aluguel é indicada por uma linha verde.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Rota de delimitação geográfica":::

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Para armazenar dados de acompanhamento de violação de carros, criaremos uma [conta de armazenamento para uso geral v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) em seu grupo de recursos. Se você não tiver criado um grupo de recursos, siga as instruções em [Criar um grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). Neste tutorial, nomearemos nosso grupo de recursos como *ContosoRental*.

Para criar uma conta de armazenamento, siga as instruções em [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). Neste tutorial, nomeamos a conta de armazenamento *contosorentalstorage*, mas você pode nomeá-la como desejar.

Depois que sua conta de armazenamento tiver sido criada com êxito, precisaremos criar um contêiner para armazenamento dos dados de log.

1. Navegue para a sua conta de armazenamento recém-criada. Clique no link **Contêineres** na seção Dados básicos.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Contêineres para Armazenamento de Blobs":::

2. Clique no botão **+ Contêiner** no canto superior esquerdo. Um painel será exibido no lado direito do navegador. Nomeie seu contêiner *contoso-aluguel-logs* e clique em **Criar**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Crie um contêiner de blob":::

3. Navegue até a folha **Chaves de acesso** em sua conta de armazenamento e copie o **Nome da conta de armazenamento** e o valor da **Chave** na seção **key1**. Precisaremos de ambos os valores na seção [Criar uma função do Azure e adicionar uma assinatura da Grade de Eventos](#create-an-azure-function-and-add-an-event-grid-subscription).

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Copiar o nome e chave da conta de armazenamento":::

## <a name="upload-a-geofence"></a>Carregar uma cerca geográfica

Agora, usaremos o [aplicativo Postman](https://www.getpostman.com) para [carregar a cerca geográfica](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) para o serviço Azure Mapas. A cerca geográfica define a área geográfica autorizada para nosso veículo de aluguel.  Usaremos a cerca geográfica em nossa função do Azure para determinar se um carro se deslocou para fora da área da cerca geográfica.

Abra o aplicativo Postman e siga as etapas abaixo para carregar a cerca geográfica usando a API de Upload de Dados do Azure Mapas.  

1. Abra o aplicativo Postman. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método HTTP **POST** na guia do construtor e insira a URL a seguir para carregar a cerca geográfica na API de Upload de Dados. Lembre-se de substituir `{subscription-key}` pela chave da assinatura primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    O parâmetro "geojson" no parâmetro `dataFormat` no caminho da URL representa o formato dos dados sendo carregados.

4. Clique em **Corpo**, selecione o formato de entrada **bruto** e escolha **JSON** como o formato de entrada na lista suspensa. Abra o arquivo de dados JSON [aqui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copie o JSON na seção corpo. Clique em **Enviar**.

5. Clique no botão azul **Enviar** e aguarde a solicitação ser processada. Depois que a solicitação for concluída, vá para a guia **Cabeçalhos** da resposta. Copie o valor da chave **Local**, que é `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o status da chamada à API, crie uma solicitação HTTP **GET** em `status URL`. Você precisará acrescentar sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Criar um Hub IoT do Azure

O Hub IoT do Azure permite comunicação bidirecional segura e confiável entre um aplicativo IoT e os dispositivos gerenciados por ele.  Em nosso cenário, queremos obter informações do nosso dispositivo no veículo para determinar a localização do carro de aluguel. Nesta seção, criaremos um hub IoT dentro do grupo de recursos *ContosoRental*. O hub IoT será responsável por publicar nossos eventos de telemetria do dispositivo.

> [!NOTE]
> A funcionalidade do hub IoT para publicar eventos de telemetria do dispositivo na Grade de Eventos está em versão prévia pública. A versão prévia pública dos recursos está disponível em todas as regiões, exceto **Leste dos EUA, Oeste dos EUA, Oeste da Europa, Azure Governamental, Azure China 21Vianet** e **Azure Alemanha**.

Para criar um hub IoT no grupo de recursos *ContosoRental*, siga as etapas em [Criar um hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Registrar um dispositivo no hub IoT

Os dispositivos não podem se conectar ao hub IoT, a menos que estejam registrados no registro de identidade do hub IoT. Em nosso cenário, criaremos apenas um dispositivo com o nome *InVehicleDevice*. Para criar e registrar o dispositivo no hub IoT, siga as etapas em [Registrar um novo dispositivo no hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Lembre-se de copiar a **cadeia de conexão primária** do seu dispositivo, pois vamos usá-la em uma etapa posterior.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Criar uma função do Azure e adicionar uma assinatura da Grade de Eventos

O Azure Functions é um serviço de computação sem servidor que permite executar pequenos trechos de código, também chamados de "funções", sem a necessidade de provisionar explicitamente nem gerenciar a infraestrutura de computação. Para saber mais sobre o Azure Functions, confira a documentação do [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Uma função é "disparada" por um determinado evento. Em nosso cenário, criaremos uma função que é disparada por um gatilho da Grade de Eventos. Criamos a relação entre gatilho e função criando uma Assinatura de Evento para eventos de telemetria do dispositivo do hub IoT. Quando ocorrer um evento de telemetria do dispositivo, nossa função será chamada como um ponto de extremidade e receberá os dados relevantes para o [dispositivo registrado anteriormente no hub IoT](#register-a-device-in-iot-hub).

O código de script C# que nossa função vai conter pode ser visto [aqui](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Agora, vamos configurar nossa função do Azure.

1. No painel do portal do Azure, selecione **Criar um recurso**. Digite **Aplicativo de Funções** na caixa de texto de pesquisa. Clique em **Aplicativo de Funções**. Clique em **Criar**.

2. Na página de criação do **Aplicativo de Funções**, nomeie o aplicativo de funções. Em **Grupo de Recursos**, selecione *ContosoRental* na lista suspensa.  Selecione *.NET Core* como a **Pilha de Runtime**. Clique em **Avançar: Hospedagem >** na parte inferior da página.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Criar um aplicativo de funções":::

3. Para **Conta de armazenamento**, selecione a conta de armazenamento que você criou em [Criar uma conta de armazenamento do Azure](#create-an-azure-storage-account). Clique em **Revisar + Criar**.

4. Examine os detalhes do aplicativo de funções e clique em **Criar**.

5. Depois que o aplicativo for criado, adicionaremos uma função a ele. Vá para o aplicativo de funções. Clique na folha **Funções**. Clique em **+ Adicionar** na parte superior da página. O painel de modelo de função será exibido. Role para baixo no painel. Clique em **Gatilho da Grade de Eventos do Azure**.

     >[!WARNING]
    > O **Gatilho da Hub de Eventos do Azure** e o **Gatilho da Grade de Eventos do Azure** têm ícones semelhantes. Lembre-se de clicar no modelo **Gatilho da Grade de Eventos do Azure**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Criar uma função":::

6. Dê um nome à função. Neste tutorial, usamos o nome *GetGeoFunction*, mas você pode usar qualquer nome que desejar. Clique em **Criar função**.

7. Clique na folha **Código + Teste** no menu à esquerda. Copie e cole o [script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) na janela de código.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Copiar/colar o código na janela de função":::

8. No código C#, substitua os parâmetros a seguir. Clique em **Salvar**. Não clique em **Testar/Executar** ainda
    * Substitua **SUBSCRIPTION_KEY** pela chave de assinatura primária da conta do Azure Mapas.
    * Substitua **UDID** pelo `udid` da cerca geográfica que você carregou em [Carregar uma cerca geográfica](#upload-a-geofence).
    * A função **CreateBlobAsync** no script cria um blob por evento na conta de armazenamento de dados. Substitua **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, pelo nome da conta e pelo contêiner de armazenamento de dados. Esses valores foram gerados quando você criou sua conta de armazenamento em [Criar uma conta de Armazenamento do Azure](#create-an-azure-storage-account).

9. Clique na folha **Integração** no menu à esquerda. Clique em **Gatilho da Grade de Eventos** no diagrama. Digite um nome para o gatilho, como *eventCarTelemetry*, e clique em **Criar assinatura da Grade de Eventos**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Adicionar assinatura de evento":::

10. Preencha os detalhes da assinatura. Nomeie a assinatura do evento. Para *Esquema de Evento*, selecione *Esquema de Grade de Eventos*. Para **Tipos de Tópico**, selecione *Contas do Hub IoT do Azure*. Para **Grupo de Recursos**, selecione o grupo de recursos criado no início deste tutorial. Para **Recurso**, selecione o hub IoT criado em [Criar um Hub IoT do Azure](#create-an-azure-iot-hub). Para **Filtrar por Tipos de Evento**, selecione *Telemetria do Dispositivo*. Depois de escolher essas opções, você verá o **Tipo de Tópico** se alterar para *Hub IoT*. Para **Nome do Tópico do Sistema**, é possível usar o mesmo nome que o recurso.  Por fim, clique em **Selecionar um ponto de extremidade** na seção **Detalhes do ponto de extremidade**. Aceite todas as configurações e clique em **Confirmar Seleção**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Criar assinatura de evento":::

11. Examine suas configurações. Lembre-se de que o ponto de extremidade especifica a função que você criou no início desta seção. Clique em **Criar**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Criar uma confirmação de assinatura de evento":::

12. Agora você está de volta no painel **Editar Gatilho**. Clique em **Salvar**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando o roteamento de mensagens do Hub IoT

Quando você adiciona uma assinatura de Grade de Eventos à Função do Azure, uma rota de mensagens é criada automaticamente no hub IoT especificado. O roteamento de mensagens permite que você roteie diferentes tipos de dados para vários pontos de extremidade. Por exemplo, você pode rotear mensagens de telemetria do dispositivo, eventos do ciclo de vida do dispositivo e eventos de alteração de dispositivo gêmeo. Para saber mais sobre o roteamento de mensagens do Hub IoT, confira [Usar o roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Roteamento de mensagens no hub IoT":::

Em nosso cenário de exemplo, desejamos apenas receber mensagens quando o carro de aluguel está em movimento. Criaremos uma consulta de roteamento para filtrar os eventos em que a propriedade `Engine` é igual a **"ON"** . Para criar uma consulta de roteamento, clique na rota **RouteToEventGrid** e substitua a **Consulta de roteamento** por **"Engine='ON'"** e clique em **Salvar**. Agora, o Hub IoT apenas publicará a telemetria do dispositivo em que o Motor estiver LIGADO.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtrar mensagens de roteamento":::

>[!TIP]
>Há várias maneiras de consultar mensagens do dispositivo para a nuvem do IoT. Para saber mais sobre a sintaxe de roteamento de mensagens, confira [Roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

Depois que a nossa Função do Azure estiver em funcionamento, podemos enviar dados telemétricos ao hub IoT, que os roteará para a Grade de Eventos. Vamos usar um aplicativo C# para simular dados de localização para um dispositivo no veículo de um carro de aluguel. Para executar o aplicativo, é necessário ter o SDK do .NET Core 2.1.0 ou superior no computador de desenvolvimento. Siga as etapas abaixo para enviar dados de telemetria simulados para o Hub IoT.

1. Se você ainda não fez isso, baixe o projeto [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) em C#.

2. Abra o arquivo simulatedCar.cs em um editor de texto de sua escolha e substitua o valor do `connectionString` pelo que você salvou quando registrou o dispositivo e salve as alterações no arquivo.

3. Verifique se você tem o .NET Core instalado no computador. Na janela do terminal local, navegue até a pasta raiz do projeto em C# e execute o seguinte comando para instalar os pacotes necessários para o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, execute o seguinte comando para compilar e executar o aplicativo de simulação de carros alugados:

    ```cmd/sh
    dotnet run
    ```

  Seu terminal local deve ser semelhante ao mostrado abaixo.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Saída do terminal":::

Se você abrir o contêiner de Armazenamento de Blobs agora, verá quatro blobs para locais em que o veículo estava fora da cerca geográfica.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Exibir blobs em um contêiner":::

O mapa abaixo mostra quatro pontos de localização de veículo fora da cerca geográfica. Cada localização foi registrada em intervalos de tempo regulares.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Mapa de violações":::

## <a name="explore-azure-maps-and-iot"></a>Explorar o Azure Mapas e o IoT do Azure

Para explorar as APIs do Azure Mapas usadas neste tutorial, confira:

* [Obter Reversão do Endereço de Pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Obter Cerca Geográfica](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs REST do Azure Mapas, confira:

* [APIs REST do Azure Mapas](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para saber mais sobre o IoT Plug and Play, confira:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Para obter uma lista de dispositivos certificados pelo Azure para IoT, visite:

* [Dispositivos certificados pelo Azure](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como enviar o dispositivo para a telemetria de nuvem e vice-versa, confira:

> [!div class="nextstepaction"]
> [Enviar telemetria por meio de um dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
