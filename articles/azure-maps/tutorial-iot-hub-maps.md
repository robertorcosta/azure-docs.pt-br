---
title: 'Tutorial: Implementar análise espacial de IoT usando o Azure Mapas'
description: 'Tutorial: Integre o Hub IoT às APIs de serviço do Azure Mapas.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b876b27d0eb24a9eabcffe0d131ea0ef5bb79bad
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107048"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: Implementar análise espacial de IoT usando o Azure Mapas

Acompanhar e capturar eventos relevantes que ocorrem em espaço e no tempo é um cenário comum de IoT. Por exemplo, em aplicativos de gerenciamento de frota, acompanhamento de ativos, mobilidade e cidade inteligente. Este tutorial orienta você em um padrão de solução para usar APIs do Azure Mapas em relação a eventos relevantes capturados pelo Hub IoT usando o modelo de assinatura de evento fornecido pela Grade de Eventos.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Crie um Hub IoT.
> * Carregue a área do limite geográfico no Azure Mapas, serviço de dados usando a API de Upload de Dados.
> * Crie uma função no Azure Functions implementando a lógica de negócios com base na análise espacial do Azure Mapas.
> * Assine eventos de telemetria do dispositivo de IoT usando a função Azure por meio da Grade de Eventos.
> * Filtre os eventos de telemetria usando o roteamento de mensagens do Hub IoT.
> * Crie uma conta de armazenamento para armazenar dados de eventos relevantes.
> * Simule um dispositivo de IoT no veículo.
    

## <a name="use-case"></a>Caso de uso

Exemplificaremos a solução para um cenário em que uma empresa de aluguel de carros planeja monitorar e registrar eventos para seus carros alugados. Muitas vezes, as empresas de aluguel de carros alugam carros para uma região geográfica específica e precisam manter o controle da localização do veículo durante o período do aluguel. Qualquer caso que envolva um carro sair da região geográfica designada precisa ser registrada para que as políticas, as tarifas e outros aspectos comerciais possam ser tratados adequadamente.

Em nosso caso de uso, os carros alugados estão equipados com dispositivos IoT que enviam dados de telemetria para o Hub IoT do Azure regularmente. A telemetria inclui o local atual e indica se o motor do carro está operando ou não. O esquema de localização do dispositivo adere ao [esquema de Plug and Play para dados geoespaciais](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md) de IoT. O esquema de telemetria do dispositivo do carro alugado é semelhante a:

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

A telemetria do dispositivo no veículo pode ser usada para atingir a meta. Nossa meta é executar regras de delimitação geográfica e acompanhar adequadamente sempre que um evento indicando que o carro foi movido é recebido. Para fazer isso, vamos assinar os eventos de telemetria do dispositivo do Hub IoT por meio da Grade de Eventos de modo que a lógica de negócios do cliente desejada possa ser executada somente quando apropriado. Há várias maneiras de assinar a Grade de Eventos. Neste tutorial, vamos usar o Azure Functions. O Azure Functions reage a eventos publicados na Grade de Eventos e implementa a lógica de negócios de aluguel de carros com base na análise espacial do Azure Mapas. A função do Azure consiste em verificar se o veículo saiu da cerca geográfica e, em caso afirmativo, reunir mais informações, como o endereço associado ao local atual. A função também implementa a lógica para armazenar dados de eventos significativos em um armazenamento de blobs de dados que ajuda a fornecer uma descrição precisa das circunstâncias do evento para o analista de aluguel de carro, bem como para o cliente de aluguel.

O diagrama a seguir apresenta uma visão geral de alto nível do sistema.

 
  <center>

  ![Visão geral do sistema](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

A figura a seguir representa a área deda cerca geográfica destacada em azul e a rota do veículo alugado como uma linha verde.

  ![Rota de delimitação geográfica](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Pré-requisitos 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para concluir as etapas deste tutorial, primeiro você precisa criar um grupo de recursos no portal do Azure. Para criar um grupo de recursos, siga as etapas abaixo:

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. Selecione **Grupos de recursos**.
    
   ![Grupos de recursos](./media/tutorial-iot-hub-maps/resource-group.png)

3. Em Grupo de Recursos, selecione **Adicionar**.
    
   ![Adicionar grupo de recursos](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Insira os seguintes valores de propriedade:
    * **Assinatura:** Selecione sua assinatura do Azure.
    * **Grupo de recursos:** Insira "ContosoRental" como o nome do grupo de recursos.
    * **Região:** Selecione uma região para o grupo de recursos.  

    ![Detalhes do grupo de recursos](./media/tutorial-iot-hub-maps/resource-details.png)

    Clique em **Examinar + criar** e escolha **Criar** na próxima página.

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure 

Para implementar a lógica de negócios com base na análise espacial do Azure Mapas, precisamos criar uma conta do Azure Mapas no grupo de recursos que criamos. Siga as instruções em [gerenciar conta](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) para criar uma assinatura de conta do Azure Mapas com tipo de preço S1 e siga as etapas em [obter chave primária](./tutorial-search-location.md#getkey) para obter a chave primária da assinatura da conta.


### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para registrar dados de evento, criaremos uma conta **v2storage** de uso geral no grupo de recursos "ContosoRental" para armazenar dados como blobs. Para criar uma conta de armazenamento, siga a instrução em [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Em seguida, precisaremos criar um contêiner para armazenar os blobs. Siga as etapas abaixo para fazer isso:

1. Em sua conta de armazenamento, navegue até Blobs.

    ![blobs](./media/tutorial-iot-hub-maps/blobs.png)

2. Clique no botão contêiner no canto superior esquerdo, dê ao contêiner o nome de "contoso-aluguel-logs" e clique em "OK".

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navegue até a folha **Chaves de acesso** em sua conta de armazenamento e copie o nome e a chave de acesso da conta. Esses dados serão usados mais adiante.

    ![access-keys](./media/tutorial-iot-hub-maps/access-keys.png)


Agora que temos uma conta de armazenamento e um contêiner para registrar dados de evento, vamos criar um hub IoT.

### <a name="create-an-iot-hub"></a>Crie um Hub IoT

Um Hub IoT é um serviço gerenciado e hospedado na nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre um aplicativo de IoT e os dispositivos que ele gerencia. Para rotear mensagens de telemetria de dispositivo para uma grade de eventos, criaremos um hub IoT dentro do grupo de recursos "ContosoRental" e configuraremos uma integração de rota de mensagem em que vamos filtrar as mensagens com base no status do motor do carro e enviar a telemetria do dispositivo mensagens para a Grade de Eventos sempre que o carro estiver em movimento. 

> [!Note] 
> A funcionalidade do Hub IoT para publicar eventos de telemetria de dispositivo na Grade de Eventos está em versão prévia pública. A versão prévia pública dos recursos está disponível em todas as regiões, exceto **Leste dos EUA, Oeste dos EUA, Oeste da Europa, Azure Governamental, Azure China 21Vianet** e **Azure Alemanha**. 

Crie um Hub IOT seguindo as etapas na [seção criar um Hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registrar um dispositivo 

Para se conectar ao Hub IoT, um dispositivo deve ser registrado. Para registrar um dispositivo com o Hub IoT, siga as etapas abaixo:

1. No Hub IoT, clique na folha "dispositivos IoT" e clique em "Novo".

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. Na página Criar um dispositivo, dê um nome ao dispositivo IoT e clique em "Salvar".
    
    ![register-device](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Carregar cerca geográfica

Usaremos o [aplicativo de Postmaster](https://www.getpostman.com) para [carregar a cerca geográfica](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) para o serviço Azure Mapas usando a API de Upload de Dados do Azure Mapas. Qualquer evento quando o carro estiver fora dessa cerca geográfica será registrado.

Abra o aplicativo Postman e siga as etapas abaixo para carregar a cerca geográfica usando o Azure Mapas, API de Upload de Dados.  

1. No aplicativo Postman, clique em novo | Criar novo e selecione Solicitação. Insira um nome de solicitação para o upload de dados de limite geográfico, selecione uma coleção ou uma pasta em que salvá-los e clique em Salvar.

    ![Carregar limites geográficos usando o Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecione o método HTTP POST na guia compilador e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro "geojson" no parâmetro `dataFormat` no caminho da URL representa o formato dos dados sendo carregados.

3. Clique em **Parâmetros** e insira os seguintes pares de chave/valor a serem usados para a URL da solicitação POST. Substitua o valor subscription-key pela chave de assinatura primária do Azure Mapas.
   
    ![Postman de parâmetros de chave-valor](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Clique em **Corpo** e selecione o formato de entrada **bruto** e escolha **JSON (aplicativo/texto)** como o formato de entrada na lista suspensa. Abra o arquivo de dados JSON [aqui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copie o Json para a seção do corpo no Postman com os dados a serem carregados e clique em **Enviar**.
    
    ![postar dados](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Examine os cabeçalhos de resposta. Após uma solicitação bem-sucedida, o cabeçalho **Local** conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status estará no formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie seu URI de status e acrescente um parâmetro `subscription-key` a ele com seu valor sendo sua chave de assinatura de conta do Azure Mapas. O formato do URI de status deve ser semelhante ao mostrado abaixo:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o `udId`, abra uma nova guia no aplicativo de postmaster e selecione obter método GET HTTP na guia construtor e faça uma solicitação GET no URI de status. Se o upload de dados tiver sido bem-sucedido, você receberá um udId no corpo da resposta. Copie o udId para uso posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Em seguida, criaremos uma função do Azure dentro do grupo de recursos "ContosoRental" e configuraremos uma rota de mensagem no Hub IoT para filtrar as mensagens de telemetria do dispositivo.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Criar uma função do Azure e adicionar uma assinatura da Grade de Eventos

O Azure Functions é um serviço de computação sem servidor que permite executar o código sob demanda sem necessidade de provisionar explicitamente nem gerenciar a infraestrutura de computação. Para saber mais sobre o Azure Functions, confira a documentação do [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). A lógica que implementamos na função é usar os dados de localização provenientes da telemetria do dispositivo no veículo para avaliar o status da cerca geográfica. Caso um determinado veículo fique fora da cerca geográfico, a função coletará mais informações, como endereço do local, por meio da [API Obter Endereço de Pesquisa Reversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) que traduz uma determinada coordenada de local em um endereço compreensível por humanos. Todas as informações de evento relevantes são armazenadas no repositório de blob. A Etapa 5 abaixo aponta para o código executável que implementa essa lógica. Siga as etapas abaixo para criar uma função do Azure que envia logs de dados para o contêiner de blob na conta de armazenamento e adicione uma assinatura de grade de eventos a ela.

1. No dashboard do portal do Azure, selecione Criar um recurso. Selecione **Computação** na lista de tipos de recursos disponíveis e, em seguida, selecione o **aplicativo de Funções**.

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na página criação do Aplicativo de funções, dê um nome a seu aplicativo de funções em **Grupo de Recursos**, selecione **Usar existente** e selecione "ContosoRental" na lista suspensa. Selecione ".NET Core" como a Pilha de Runtime, em **Armazenamento**, selecione **Usar existente**, selecione "contosorentaldata" na lista suspensa e clique em **Criar**.
    
    ![create-app](./media/tutorial-iot-hub-maps/rental-app.png)

3. Depois que o aplicativo tiver sido criado, precisaremos adicionar uma função a ele. Vá para o aplicativo de funções e clique em **Nova função** para adicionar uma função, escolha **No Portal** como o ambiente de desenvolvimento e selecione **Continuar**.

    ![create-function](./media/tutorial-iot-hub-maps/function.png)

4. Escolha **Mais modelos** e clique em **Concluir e exibir modelos**. 

5. Selecione o modelo com um **Gatilho de Grade de Eventos do Azure**. Instale extensões, se solicitado, dê um nome à função e clique em **Criar**.

    ![function-template](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Copie o [código c#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) para sua função e clique em **Salvar**.
 
7. No script C#, substitua os seguintes parâmetros:
    * Substitua **SUBSCRIPTION_KEY** pela chave primária de assinatura da conta do Azure Mapas.
    * Substitua **UDID** pelo udId da cerca geográfica que você carregou. 
    * A função **CreateBlobAsync** no script cria um blob por evento na conta de armazenamento de dados. Substitua **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** pela chave de acesso da conta de armazenamento e o contêiner de armazenamento de dados e nome da conta.

10. Clique em **Adicionar assinatura da Grade de Eventos**.
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Preencha os detalhes da assinatura, dê um nome à sua assinatura em **DETALHES DA ASSINATURA DO EVENTO** e, para o Esquema de Evento, escolha "Esquema de Grade de Eventos". Em **DETALHES DO TÓPICO**, selecione "Contas do Hub IoT do Azure" como Tipo de tópico, escolha a mesma assinatura usada para criar o grupo de recursos, selecione "ContosoRental" como o "Grupo de Recursos" e escolha o Hub IoT criado como um "Recurso" ". Escolha **Telemetria do Dispositivo** como Tipo de Evento. Depois de escolher essas opções, você verá "Tipo de Tópico" mudar para "Hub IoT" automaticamente.

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando o roteamento de mensagens do Hub IoT

Depois de adicionar uma assinatura de Grade de Eventos à Função do Azure, será possível ver uma rota de mensagem padrão para a Grade de Eventos na folha **Roteamento de Mensagens** do Hub IoT. O roteamento de mensagens permite rotear diferentes tipos de dados, como mensagens de telemetria de dispositivo, eventos de ciclo de vida do dispositivo e eventos de alteração de dispositivo gêmeo para vários pontos de extremidade. Para saber mais sobre o roteamento de mensagens do Hub IoT, confira [Usar o roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

Em nosso cenário de exemplo, desejamos filtrar todas as mensagens nas quais o veículo alugado está em movimento. Para publicar esses eventos de telemetria de dispositivo na Grade de Eventos, vamos usar a consulta de roteamento para filtrar os eventos em que a propriedade `Engine` está **"ATIVADA"** . Há várias maneiras de consultar mensagens do dispositivo para a nuvem do IoT. Para saber mais sobre a sintaxe de roteamento de mensagens, confira [Roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Para criar uma consulta de roteamento, clique na rota **RouteToEventGrid** e substitua a **Consulta de roteamento** por **"Engine='ON'"** e clique em **Salvar**. Agora, o Hub IoT apenas publicará a telemetria do dispositivo em que o Motor estiver LIGADO.

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

Depois que nossa Função do Azure estiver em execução, enviaremos dados de telemetria para o Hub IoT, que os encaminhará para a grade de eventos. Usaremos um aplicativo C# para simular dados de localização para um dispositivo no veículo de um carro alugado. Para executar o aplicativo, é necessário ter o SDK do .NET Core 2.1.0 ou superior no computador de desenvolvimento. Siga as etapas abaixo para enviar dados de telemetria simulados para o Hub IoT.

1. Baixe o projeto [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) em C#. 

2. Abra o arquivo simulatedCar.cs em um editor de texto de sua escolha e substitua o valor do `connectionString` pelo que você salvou quando registrou o dispositivo e salve as alterações no arquivo.
 
3. Na janela do terminal local, navegue até a pasta raiz do projeto em C# e execute o seguinte comando para instalar os pacotes necessários para o aplicativo de dispositivo simulado:
    
    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, execute o seguinte comando para compilar e executar o aplicativo de simulação de carros alugados:

    ```cmd/sh
    dotnet run
    ```

  Seu terminal local deve ser semelhante ao mostrado abaixo.

  ![Saída do terminal](./media/tutorial-iot-hub-maps/terminal.png)

Se você abrir o contêiner de armazenamento de blobs agora, poderá ver quatro blobs para locais em que o veículo estava fora da cerca geográfica.

![Inserir blob](./media/tutorial-iot-hub-maps/blob.png)

O mapa abaixo mostra quatro pontos em que o veículo estava fora da cerca geográfica, registrados a intervalos de tempo regulares.

![mapa de violação](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Próximas etapas

Para explorar as APIs do Azure Mapas usadas neste tutorial, confira:

* [Obter Reversão de Endereço de Pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Obter Cerca Geográfica](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs REST do Azure Mapas, confira:

* [APIs REST do Azure Mapas](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para saber mais sobre o IoT Plug and Play, confira:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Para obter uma lista de dispositivos certificados pelo Azure para IoT, visite:

* [Dispositivos certificados pelo Azure](https://catalog.azureiotsolutions.com/)

Para saber mais sobre como enviar o dispositivo para a telemetria de nuvem e vice-versa, confira:

* [Enviar telemetria por meio de um dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
