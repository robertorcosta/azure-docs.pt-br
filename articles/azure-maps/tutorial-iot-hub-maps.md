---
title: 'Tutorial: Implementar análise espacial de IoT | Microsoft Azure Mapas'
description: Integre o Hub IoT às APIs de serviço do Microsoft Azure Mapas.
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a49f641561aa7a293628e914c964020145e0ae62
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486404"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: Implementar análise espacial de IoT usando o Azure Mapas

Em um cenário de IoT, é comum capturar e acompanhar eventos relevantes que ocorrem no espaço e no tempo. Os cenários de exemplo incluem aplicativos de gerenciamento de frota, acompanhamento de ativos, mobilidade e cidade inteligente. Este tutorial descreve um padrão de solução usando as APIs dos Azure Mapas. Os eventos relevantes são capturados pelo Hub IoT com o modelo de assinatura de evento fornecido pela Grade de Eventos.

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

Esta solução demonstra um cenário em que uma empresa de aluguel de carros planeja monitorar e registrar eventos para os carros de aluguel. As empresas de aluguel de carros geralmente alugam carros para uma região geográfica específica. Elas precisam acompanhar a localização dos carros enquanto eles estão alugados. As instâncias de um carro que sai da região geográfica escolhida precisam ser registradas em log. Os dados de log garantem que as políticas, os valores e outros aspectos empresariais sejam tratados corretamente.

Em nosso caso de uso, os carros alugados estão equipados com dispositivos IoT que enviam dados telemétricos regularmente para o Hub IoT do Azure. A telemetria inclui a localização atual e indica se o motor do carro está ligado. O esquema de localização do dispositivo adere ao [esquema de Plug and Play para dados geoespaciais](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md) de IoT. O esquema de telemetria do dispositivo do carro alugado é semelhante a:

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

Vamos usar telemetria de dispositivo no veículo para atingir nossa meta. Queremos executar regras de delimitação geográfica. Além disso, queremos fornecer uma resposta sempre que recebemos um evento indicando que o carro se movimentou. Para fazer isso, assinaremos os eventos de telemetria do dispositivo no Hub IoT por meio da Grade de Eventos. 

Há várias maneiras de assinar a Grade de Eventos; neste tutorial, usaremos o Azure Functions. O Azure Functions responde aos eventos publicados na Grade de Eventos. Ele também implementa a lógica de negócios do aluguel de carros, que se baseia na análise espacial dos Azure Mapas. 

O código dentro da função do Azure verifica se o veículo saiu da cerca geográfica. Se o veículo sai da cerca geográfica, a função do Azure coleta informações adicionais, como o endereço associado à localização atual. A função também implementa a lógica para armazenar dados de eventos significativos em um Armazenamento de Blobs de dados que ajuda a fornecer a descrição das circunstâncias do evento. 

As circunstâncias do evento podem ser úteis para a empresa de aluguel de carros e para o cliente de aluguel. O diagrama a seguir apresenta uma visão geral de alto nível do sistema.

 
  <center>

  ![Visão geral do sistema](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

A figura a seguir representa a área de cerca geográfica realçada em azul. A rota do veículo de aluguel é indicada por uma linha verde.

  ![Rota de delimitação geográfica](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Pré-requisitos 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para concluir as etapas deste tutorial, primeiro você precisa criar um grupo de recursos no portal do Azure. Para criar um grupo de recursos, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **Grupos de recursos**.
    
   ![Grupos de recursos](./media/tutorial-iot-hub-maps/resource-group.png)

3. Em **Grupos de recursos**, selecione **Adicionar**.
    
   ![Adicionar grupo de recursos](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Insira os seguintes valores de propriedade:
    * **Assinatura:** Selecione sua assinatura do Azure.
    * **Grupo de recursos:** Insira "ContosoRental" como o nome do grupo de recursos.
    * **Região:** Selecione uma região para o grupo de recursos.  

    ![Detalhes do grupo de recursos](./media/tutorial-iot-hub-maps/resource-details.png)

    Selecione **Examinar + criar** e, em seguida, **Criar** na próxima página.

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure 

Para implementar a lógica de negócios com base na análise espacial dos Azure Mapas, precisamos criar uma conta dos Azure Mapas no grupo de recursos que criamos. Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura da conta dos Azure Mapas no tipo de preço S1. Siga as etapas em [Obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária da sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para registrar dados de evento, criaremos um **v2storage** de uso geral que fornecerá acesso a todos os serviços de Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos.  Precisaremos colocar essa conta de armazenamento no grupo de recursos "ContosoRental" para armazenar dados como blobs. Para criar uma conta de armazenamento, siga a instrução em [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Em seguida, precisaremos criar um contêiner para armazenar os blobs. Siga as etapas abaixo para fazer isso:

1. Em sua "conta de armazenamento – blob, arquivo, tabela, fila", navegue até Contêineres.

    ![blobs](./media/tutorial-iot-hub-maps/blobs.png)

2. Clique no botão contêiner no canto superior esquerdo, dê ao contêiner o nome de "contoso-aluguel-logs" e clique em "OK".

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navegue até a folha **Chaves de acesso** em sua conta de armazenamento e copie o "nome da conta de armazenamento" e a "chave de acesso". Eles serão necessários em uma etapa posterior.

    ![access-keys](./media/tutorial-iot-hub-maps/access-keys.png)


Agora, temos uma conta de armazenamento e um contêiner para registrar dados de evento em log. Em seguida, criaremos um hub IoT.

### <a name="create-an-iot-hub"></a>Crie um Hub IoT

O Hub IoT é um serviço gerenciado na nuvem. O Hub IoT funciona como um hub central de mensagens para comunicação bidirecional entre um aplicativo IoT e os dispositivos gerenciados por ele. Para rotear mensagens de telemetria do dispositivo para uma Grade de Eventos, crie um Hub IoT no grupo de recursos "ContosoRental". Configure uma integração de rota de mensagem na qual filtraremos as mensagens com base no status do motor do carro. Também enviaremos mensagens de telemetria do dispositivo para a Grade de Eventos sempre que o carro estiver se movendo.

> [!Note] 
> A funcionalidade do Hub IoT para publicar eventos de telemetria de dispositivo na Grade de Eventos está em versão prévia pública. A versão prévia pública dos recursos está disponível em todas as regiões, exceto **Leste dos EUA, Oeste dos EUA, Oeste da Europa, Azure Governamental, Azure China 21Vianet** e **Azure Alemanha**. 

Crie um Hub IOT seguindo as etapas na [seção criar um Hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registrar um dispositivo 

Para se conectar ao Hub IoT, um dispositivo deve ser registrado. Para registrar um dispositivo com o Hub IoT, siga as etapas abaixo:

1. No Hub IoT, clique na folha "dispositivos IoT" e clique em "Novo".

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. Na página Criar um dispositivo, dê um nome ao dispositivo IoT e clique em "Salvar".
    
    ![register-device](./media/tutorial-iot-hub-maps/register-device.png)

3. Salve a **Cadeia de conexão primária** do seu dispositivo para usá-la em uma etapa posterior, na qual será necessário alterar um espaço reservado com essa cadeia de conexão.

    ![add-device](./media/tutorial-iot-hub-maps/connectionString.png)

## <a name="upload-geofence"></a>Carregar cerca geográfica

Usaremos o [aplicativo Postman](https://www.getpostman.com) para [carregar a cerca geográfica](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) no serviço Azure Mapas usando a API de Upload de Dados dos Azure Mapas. Qualquer evento quando o carro estiver fora dessa cerca geográfica será registrado.

Abra o aplicativo Postman e siga as etapas abaixo para carregar a cerca geográfica usando o Azure Mapas, API de Upload de Dados.  

1. No aplicativo Postman, clique em novo | Criar novo e selecione Solicitação. Insira um nome de solicitação para o upload de dados de limite geográfico, selecione uma coleção ou uma pasta em que salvá-los e clique em Salvar.

    ![Carregar cercas geográficas usando Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecione o método HTTP POST na guia compilador e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro "geojson" no parâmetro `dataFormat` no caminho da URL representa o formato dos dados sendo carregados.

3. Clique em **Parâmetros** e insira os seguintes pares de chave/valor a serem usados para a URL da solicitação POST. Substitua o valor subscription-key pela chave do Azure Mapas.
   
    ![Postman de parâmetros de chave-valor](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Clique em **Corpo** e, em seguida, selecione o formato de entrada **bruto** e escolha **JSON (aplicativo/texto)** como o formato de entrada na lista suspensa. Abra o arquivo de dados JSON [aqui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copie o JSON para a seção do corpo como os dados a serem carregados e clique em **Enviar**.
    
    ![postar dados](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Examine os cabeçalhos de resposta. Após uma solicitação bem-sucedida, o cabeçalho **Local** conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status estará no formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o URI de status e acrescente um parâmetro `subscription-key` a ele. Atribua o valor da sua chave de assinatura da conta do Azure Mapas ao parâmetro `subscription-key`. O formato do URI de status deve se parecer com o formato abaixo e `{Subscription-key}` deve ser substituído pela chave de assinatura.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o `udId`, abra uma nova guia no aplicativo de postmaster e selecione obter método GET HTTP na guia construtor e faça uma solicitação GET no URI de status. Se o upload de dados for bem-sucedido, você receberá uma udId no corpo da resposta. Copie o udId para uso posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Em seguida, criaremos uma Função do Azure dentro do grupo de recursos "ContosoRental" e, depois, configuraremos uma rota de mensagem no Hub IoT para filtrar as mensagens de telemetria do dispositivo.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Criar uma função do Azure e adicionar uma assinatura da Grade de Eventos

O Azure Functions é um serviço de computação sem servidor que permite executar o código sob demanda, sem a necessidade de provisionar explicitamente nem gerenciar a infraestrutura de computação. Para saber mais sobre o Azure Functions, confira a documentação do [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

A lógica que implementamos na função é usar os dados de localização provenientes da telemetria do dispositivo no veículo para avaliar o status da cerca geográfica. Se um veículo específico sair da cerca geográfica, a função coletará mais informações, como o endereço da localização por meio da [API Obter Reversão de Endereço de Pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Essa API converte uma coordenada de localização específica em um endereço compreensível por humanos. 

Todas as informações de evento relevantes são então mantidas no Armazenamento de Blobs. A Etapa 5 abaixo aponta para o código executável que implementa essa lógica. Siga as etapas abaixo para criar uma Função do Azure que envia logs de dados para o contêiner de blob na conta de Armazenamento de Blobs e adicionar uma assinatura da Grade de Eventos a ela.

1. No dashboard do portal do Azure, selecione Criar um recurso. Selecione **Computação** na lista de tipos de recursos disponíveis e, em seguida, selecione o **Aplicativo de Funções**.

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na página de criação do **Aplicativo de Funções**, nomeie o aplicativo de funções. Em **Grupo de Recursos**, selecione **Usar existente** e selecione "ContosoRental" na lista suspensa. Selecione ".NET Core" como a pilha de runtime. Em **Hospedagem**, para **Conta de armazenamento**, selecione o nome de uma conta de armazenamento de uma etapa anterior. Na etapa anterior, nomeamos a conta de armazenamento como **v2storage**.  Em seguida, selecione **Revisar + Criar**.
    
    ![create-app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Examine os detalhes do aplicativo de funções e selecione "Criar".

3. Depois que o aplicativo tiver sido criado, precisaremos adicionar uma função a ele. Vá para o aplicativo de funções. Clique em **Nova função** para adicionar uma função e escolha **No portal** como o ambiente de desenvolvimento. Em seguida, selecione **Continuar**.

    ![create-function](./media/tutorial-iot-hub-maps/function.png)

4. Escolha **Mais modelos** e clique em **Concluir e exibir modelos**. 

5. Selecione o modelo com um **Gatilho de Grade de Eventos do Azure**. Instale extensões, se solicitado, nomeie a função e selecione **Criar**.

    ![function-template](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    O **Gatilho de Hub de Eventos do Azure** e o **Gatilho da Grade de Eventos do Azure** têm ícones semelhantes. Selecione o **Gatilho da Grade de Eventos do Azure**.

6. Copie o [código C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) para sua função.
 
7. No script C#, substitua os parâmetros a seguir. Clique em **Save** (Salvar). Não clique em **Executar** ainda
    * Substitua **SUBSCRIPTION_KEY** pela chave primária de assinatura da conta do Azure Mapas.
    * Substitua **UDID** pelo udId da cerca geográfica que você carregou. 
    * A função **CreateBlobAsync** no script cria um blob por evento na conta de armazenamento de dados. Substitua **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, pelo nome da conta e pelo contêiner de armazenamento de dados.

10. Clique em **Adicionar assinatura da Grade de Eventos**.
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Preencha os detalhes da assinatura, dê um nome à sua assinatura em **DETALHES DA ASSINATURA DO EVENTO**. Para o Esquema de Evento, escolha "Esquema de Grade de Eventos". Em **DETALHES DO TÓPICO**, selecione "Contas do Hub IoT do Azure" como Tipo de tópico. Escolha a mesma assinatura que você usou para criar o grupo de recursos e selecione "ContosoRental" como o "Grupo de Recursos". Escolha o Hub IoT criado como um "Recurso". Escolha **Telemetria do Dispositivo** como Tipo de Evento. Depois de escolher essas opções, você verá o "Tipo de Tópico" mudar para "Hub IoT" automaticamente.

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando o roteamento de mensagens do Hub IoT

Depois de adicionar uma assinatura da Grade de Eventos à Função do Azure, você verá uma rota de mensagem padrão para a Grade de Eventos na folha **Roteiros de Mensagens** do Hub IoT. Os roteiros de mensagens permite que você roteie diferentes tipos de dados para vários pontos de extremidade. Por exemplo, você pode rotear mensagens de telemetria do dispositivo, eventos do ciclo de vida do dispositivo e eventos de alteração de dispositivo gêmeo. Para saber mais sobre o roteamento de mensagens do Hub IoT, confira [Usar o roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

Em nosso cenário de exemplo, desejamos filtrar todas as mensagens nas quais o veículo alugado está em movimento. Para publicar esses eventos de telemetria do dispositivo na Grade de Eventos, usaremos a consulta de roteiros para filtrar os eventos, em que a propriedade `Engine` está **"ATIVADA"** . Há várias maneiras de consultar mensagens do dispositivo para a nuvem do IoT. Para saber mais sobre a sintaxe de roteamento de mensagens, confira [Roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Para criar uma consulta de roteamento, clique na rota **RouteToEventGrid** e substitua a **Consulta de roteamento** por **"Engine='ON'"** e clique em **Salvar**. Agora, o Hub IoT apenas publicará a telemetria do dispositivo em que o Motor estiver LIGADO.

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

Depois que a nossa Função do Azure estiver em funcionamento, podemos enviar dados telemétricos ao Hub IoT, que os roteará para a Grade de Eventos. Vamos usar um aplicativo C# para simular dados de localização para um dispositivo no veículo de um carro de aluguel. Para executar o aplicativo, é necessário ter o SDK do .NET Core 2.1.0 ou superior no computador de desenvolvimento. Siga as etapas abaixo para enviar dados de telemetria simulados para o Hub IoT.

1. Baixe o projeto [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) em C#. 

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
