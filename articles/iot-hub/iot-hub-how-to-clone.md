---
title: Como clonar um hub de IoT do Azure
description: Como clonar um hub de IoT do Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429146"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Como clonar um hub de IoT do Azure para outra região

Este artigo explora maneiras de clonar um IoT Hub e fornece algumas perguntas que você precisa responder antes de começar. Aqui estão várias razões pelas quais você pode querer clonar um hub de IoT:
 
* Você está mudando sua empresa de uma região para outra, como da Europa para a América do Norte (ou vice-versa), e você quer que seus recursos e dados estejam geograficamente perto de sua nova localização, então você precisa mover seu hub.

* Você está criando um hub para um ambiente de desenvolvimento versus produção.

* Você deseja fazer uma implementação personalizada de alta disponibilidade multihub. Para obter mais informações, consulte a [seção Como alcançar a seção HA transversal do IoT Hub de alta disponibilidade e recuperação de desastres](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Você deseja aumentar o número de [partições](iot-hub-scaling.md#partitions) configuradas para o seu hub. Isso é definido quando você cria seu hub pela primeira vez, e não pode ser alterado. Você pode usar as informações neste artigo para clonar seu hub e quando o clone for criado, aumentar o número de partições.

Para clonar um hub, você precisa de uma assinatura com acesso administrativo ao hub original. Você pode colocar o novo hub em um novo grupo de recursos e região, na mesma assinatura do hub original, ou mesmo em uma nova assinatura. Você simplesmente não pode usar o mesmo nome porque o nome do hub tem que ser globalmente único.

> [!NOTE]
> No momento, não há nenhum recurso disponível para clonagem de um hub IoT automaticamente. É principalmente um processo manual, e, portanto, é bastante propenso a erros. A complexidade da clonagem de um hub é diretamente proporcional à complexidade do hub. Por exemplo, clonar um hub de IoT sem roteamento de mensagens é bastante simples. Se você adicionar roteamento de mensagem como apenas uma complexidade, a clonagem do hub torna-se pelo menos uma ordem de magnitude mais complicada. Se você também mover os recursos usados para roteamento de pontos finais, é outra ordem de magnitura mais complicada. 

## <a name="things-to-consider"></a>Itens a serem considerados

Há várias coisas a considerar antes de clonar um hub de IoT.

* Certifique-se de que todos os recursos disponíveis no local original também estão disponíveis no novo local. Alguns serviços estão em pré-visualização, e nem todos os recursos estão disponíveis em todos os lugares.

* Não remova os recursos originais antes de criar e verificar a versão clonada. Uma vez que você remove um hub, ele se foi para sempre, e não há nenhuma maneira de recuperá-lo para verificar as configurações ou dados para se certificar de que o hub é replicado corretamente.

* Muitos recursos exigem nomes globalmente únicos, então você deve usar nomes diferentes para as versões clonadas. Você também deve usar um nome diferente para o grupo de recursos ao qual o hub clonado pertence. 

* Os dados do hub IoT original não migraram. Isso inclui mensagens de telemetria, comandos cloud-to-device (C2D) e informações relacionadas ao trabalho, como horários e histórico. Métricas e resultados de registro também não são migrados. 

* Para dados ou mensagens roteadas para o Azure Storage, você pode deixar os dados na conta de armazenamento original, transferir esses dados para uma nova conta de armazenamento na nova região ou deixar os dados antigos no lugar e criar uma nova conta de armazenamento no novo local para os novos dados. Para obter mais informações sobre a movimentação de dados no armazenamento Blob, consulte [Iniciar com o AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Os dados para hubs de eventos e para tópicos e filas de ônibus de serviço não podem ser migrados. Esses são dados pontuais e não são armazenados após o processamento das mensagens.

* Você precisa agendar o tempo de inatividade para a migração. A clonagem dos dispositivos para o novo hub leva tempo. Se você estiver usando o método Importação/Exportação, os testes de benchmark revelaram que pode levar cerca de duas horas para mover 500.000 dispositivos e quatro horas para mover um milhão de dispositivos. 

* Você pode copiar os dispositivos para o novo hub sem desligar ou alterar os dispositivos. 

    * Se os dispositivos foram originalmente provisionados usando DPS, reprovisioná-los atualiza as informações de conexão armazenadas em cada dispositivo. 
    
    * Caso contrário, você tem que usar o método Importação/Exportação para mover os dispositivos e, em seguida, os dispositivos têm que ser modificados para usar o novo hub. Por exemplo, você pode configurar seu dispositivo para consumir o nome de host ioT Hub a partir das propriedades desejadas gêmeos. O dispositivo pegará o nome do host ioT hub, desconectará o dispositivo do hub antigo e o reconectará ao novo.
    
* Você precisa atualizar todos os certificados que você está usando para que você possa usá-los com os novos recursos. Além disso, você provavelmente tem o hub definido em uma tabela DNS em algum lugar — você precisará atualizar essas informações de DNS.

## <a name="methodology"></a>Metodologia

Este é o método geral que recomendamos para mover um hub de IoT de uma região para outra. Para o roteamento de mensagens, isso pressupõe que os recursos não estão sendo transferidos para a nova região. Para obter mais informações, consulte a [seção em Roteamento de Mensagens](#how-to-handle-message-routing).

   1. Exporte o hub e suas configurações para um modelo de Gerenciador de recursos. 
   
   1. Faça as alterações necessárias ao modelo, como atualizar todas as ocorrências do nome e a localização do hub clonado. Para quaisquer recursos no modelo usado para pontos finais de roteamento de mensagens, atualize a chave no modelo para esse recurso.
   
   1. Importe o modelo para um novo grupo de recursos no novo local. Isso cria o clone.

   1. Depurar conforme necessário. 
   
   1. Adicione qualquer coisa que não tenha sido exportada para o modelo. 
   
       Por exemplo, os grupos de consumidores não são exportados para o modelo. Você precisa adicionar os grupos de consumidores ao modelo manualmente ou usar o [portal Azure](https://portal.azure.com) após a criação do hub. Há um exemplo de adicionar um grupo de consumidores a um modelo no artigo [Use um modelo do Azure Resource Manager para configurar o roteamento de mensagens do IoT Hub](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Copie os dispositivos do hub original para o clone. Isso é coberto na seção [Gerenciamento dos dispositivos registrados no hub IoT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Como lidar com o roteamento de mensagens

Se o seu hub usar [roteamento personalizado,](iot-hub-devguide-messages-read-custom.md)exportar o modelo para o hub inclui a configuração de roteamento, mas não inclui os recursos em si. Você deve escolher se deve mover os recursos de roteamento para o novo local ou deixá-los no lugar e continuar a usá-los "como está". 

Por exemplo, digamos que você tem um hub no Oeste dos EUA que está encaminhando mensagens para uma conta de armazenamento (também no Oeste dos EUA), e você quer mudar o hub para o Leste dos EUA. Você pode mover o hub e ainda encaminhá-lo para a conta de armazenamento no Oeste dos EUA, ou você pode mover o hub e também mover a conta de armazenamento. Pode haver um pequeno impacto de desempenho desde o roteamento de mensagens até recursos de ponto final em uma região diferente.

Você pode mover um hub que usa roteamento de mensagens muito facilmente se você também não mover os recursos usados para os pontos finais de roteamento. 

Se o hub usa roteamento de mensagens, você tem duas opções. 

1. Mova os recursos usados para os pontos finais de roteamento para o novo local.

    * Você deve criar os novos recursos você mesmo manualmente no [portal Dozure](https://portal.azure.com) ou através do uso de modelos do Gerenciador de Recursos. 

    * Você deve renomear todos os recursos ao criá-los no novo local, pois eles têm nomes globalmente únicos. 
     
    * Você deve atualizar os nomes dos recursos e as chaves de recursos no modelo do novo hub, antes de criar o novo hub. Os recursos devem estar presentes quando o novo hub for criado.

1. Não mova os recursos usados para os pontos finais de roteamento. Use-os "no lugar".

   * Na etapa em que você edita o modelo, você precisará recuperar as chaves para cada recurso de roteamento e colocá-las no modelo antes de criar o novo hub. 

   * O hub ainda faz referência aos recursos de roteamento originais e encaminha mensagens para eles conforme configurado.

   * Você terá um pequeno desempenho atingido porque o hub e os recursos de ponto final de roteamento não estão no mesmo local.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Prepare-se para migrar o hub para outra região

Esta seção fornece instruções específicas para migrar o hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Encontre o hub original e exporte-o para um modelo de recurso.

1. Inscreva-se no [portal Azure](https://portal.azure.com). 

1. Vá para **Grupos de recursos** e selecione o grupo de recursos que contém o hub que deseja mover. Você também pode ir para **Recursos** e encontrar o hub dessa forma. Selecione o hub.

1. Selecione Modelo de **exportação** na lista de propriedades e configurações do hub. 

   ![Captura de tela mostrando o comando para exportar o modelo para o IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecione **Baixar** para baixar o modelo. Salve o arquivo em algum lugar onde você pode encontrá-lo novamente. 

   ![Captura de tela mostrando o comando para baixar o modelo para o IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Exibição do modelo 

1. Vá para a pasta Downloads (ou para qualquer pasta que você usou quando exportou o modelo) e encontre o arquivo zip. Abra o arquivo zip e `template.json`encontre o arquivo chamado . Selecione-o e selecione Ctrl+C para copiar o modelo. Vá para uma pasta diferente que não está no arquivo zip e cole o arquivo (Ctrl+V). Agora você pode editá-lo.
 
    O exemplo a seguir é para um hub genérico sem configuração de roteamento. É um hub de nível S1 (com 1 unidade) chamado **ContosoTestHub29358** na região **westus**. Aqui está o modelo exportado.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Editar o modelo 

Você tem que fazer algumas alterações antes de poder usar o modelo para criar o novo hub na nova região. Use [o CÓDIGO VS](https://code.visualstudio.com) ou um editor de texto para editar o modelo.

#### <a name="edit-the-hub-name-and-location"></a>Editar o nome e a localização do hub

1. Remova a seção de parâmetros na parte superior - é muito mais simples apenas usar o nome do hub porque não vamos ter vários parâmetros. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Altere o nome para usar o nome real (novo) em vez de recuperá-lo de um parâmetro (que você removeu na etapa anterior). 

    Para o novo hub, use o nome do hub original mais o *clone* de seqüência para compor o novo nome. Comece limpando o nome e a localização do hub.
    
    Versão antiga:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nova versão: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Em seguida, você verá que os valores para **o caminho** contêm o nome antigo do hub. Mude-os para usar o novo. Esses são os valores de caminho em **eventHubEndpoints** chamados **eventos** e **OperationsMonitoringEvents**.

    Quando terminar, a seção de pontos finais do hub de eventos deve ficar assim:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Atualize as chaves para os recursos de roteamento que não estão sendo movidos

Quando você exporta o modelo gerenciador de recursos para um hub que tem roteamento configurado, você verá que as chaves desses recursos não estão fornecidas no modelo exportado -- sua colocação é denotada por asteriscos. Você deve preenchê-los indo para esses recursos no portal e recuperando as chaves **antes** de importar o modelo do novo hub e criar o hub. 

1. Recupere as chaves necessárias para qualquer um dos recursos de roteamento e coloque-as no modelo. Você pode recuperar as tecla(s) do recurso no [portal Azure](https://portal.azure.com). 

   Por exemplo, se você estiver encaminhando mensagens para um contêiner de armazenamento, encontre a conta de armazenamento no portal. Na seção Configurações, selecione **Teclas de acesso**e copie uma das teclas. Veja como a chave se parece quando você exporta pela primeira vez o modelo:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Depois de recuperar a chave da conta para a conta `AccountKey=****` de armazenamento, coloque-a no modelo na cláusula no lugar dos asteriscos. 

1. Para filas de barramento de serviço, obtenha a chave de acesso compartilhado que corresponda ao SharedAccessKeyName. Aqui está a `SharedAccessKeyName` chave e o no json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. O mesmo se aplica aos tópicos de ônibus de serviço e conexões do Event Hub.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Crie os novos recursos de roteamento no novo local

Esta seção só se aplica se você estiver movendo os recursos usados pelo hub para os pontos finais de roteamento.

Se você quiser mover os recursos de roteamento, você deve configurar manualmente os recursos no novo local. Você pode criar os recursos de roteamento usando o [portal Azure](https://portal.azure.com)ou exportando o modelo de Gerenciador de recursos para cada um dos recursos usados pelo roteamento de mensagens, editando-os e importando-os. Depois que os recursos forem configurados, você pode importar o modelo do hub (que inclui a configuração de roteamento).

1. Crie cada recurso usado pelo roteamento. Você pode fazer isso manualmente usando o [portal Azure](https://portal.azure.com)ou criar os recursos usando modelos do Gerenciador de Recursos. Se você quiser usar modelos, estes são os passos a seguir:

    1. Para cada recurso usado pelo roteamento, exporte-o para um modelo de Gerenciador de recursos.
    
    1. Atualize o nome e a localização do recurso. 

    1. Atualize quaisquer referências cruzadas entre os recursos. Por exemplo, se você criar um modelo para uma nova conta de armazenamento, você precisará atualizar o nome da conta de armazenamento nesse modelo e qualquer outro modelo que faça referência a ele. Na maioria dos casos, a seção de roteamento no modelo para o hub é o único outro modelo que faz referência ao recurso. 

    1. Importe cada um dos modelos, que implanta cada recurso.

    Uma vez que os recursos usados pelo roteamento estejam configurados e executados, você pode continuar.

1. No modelo para o hub IoT, altere o nome de cada um dos recursos de roteamento para seu novo nome e atualize o local, se necessário. 

Agora você tem um modelo que criará um novo hub que se parece quase exatamente com o hub antigo, dependendo de como você decidiu lidar com o roteamento.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Mover - criar o novo hub na nova região carregando o modelo

Crie o novo hub no novo local usando o modelo. Se você tiver recursos de roteamento que serão movimentados, os recursos devem ser configurados no novo local e as referências no modelo atualizado para corresponder. Se você não estiver movendo os recursos de roteamento, eles devem estar no modelo com as chaves atualizadas.

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. 

1. Na caixa de pesquisa, coloque em "implantação de modelo" e selecione Enter.

1. Selecione **a implantação do modelo (implante usando modelos personalizados)**. Isso leva você a uma tela para a implantação do Template. Selecione **Criar**. Você verá esta tela:

   ![Captura de tela mostrando o comando para construir seu próprio modelo](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selecione **Construir seu próprio modelo no editor, o**que permite que você carregue seu modelo a partir de um arquivo. 

1. Selecione **'Carregar arquivo ''Carregar'.** 

   ![Captura de tela mostrando o comando para upload de um arquivo de modelo](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Procure o novo modelo que você editou e selecione-o e, em seguida, selecione **Abrir**. Ele carrega seu modelo na janela de edição. Selecione **Salvar**. 

   ![Captura de tela mostrando o carregamento do modelo](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Preencha os campos a seguir.

   **Assinatura**: selecione a assinatura a ser usada.

   **Grupo de recursos**: crie um novo grupo de recursos em um novo local. Se você já tiver uma configuração nova, você pode selecioná-la em vez de criar uma nova.

   **Localização**: Se você selecionou um grupo de recursos existente, isso será preenchido para que você corresponda à localização do grupo de recursos. Se você criou um novo grupo de recursos, esta será sua localização.

   **Concordo com a caixa de seleção**: isso basicamente diz que você concorda em pagar pelos recursos que está criando.

1. Selecione o botão **Comprar**.

O portal agora valida seu modelo e implanta seu hub clonado. Se você tiver dados de configuração de roteamento, ele será incluído no novo hub, mas apontará para os recursos no local anterior.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Gerenciamento dos dispositivos registrados no hub ioT

Agora que você tem o seu clone em funcionamento, você precisa copiar todos os dispositivos do hub original para o clone. 

Há várias maneiras de realizar isso. Ou você usou originalmente [o DPS (Device Provisioning Service, serviço de provisionamento de dispositivos)](/azure/iot-dps/about-iot-dps)para provisionar os dispositivos, ou não. Se você fez, isso não é difícil. Se você não fez, isso pode ser muito complicado. 

Se você não usou DPS para provisionar seus dispositivos, você pode pular a próxima seção e começar [com o uso de importação/exportação para mover os dispositivos para o novo hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Usando DPS para reprovisionar os dispositivos no novo hub

Para usar o DPS para mover os dispositivos para o novo local, consulte [Como reprovisionar dispositivos](../iot-dps/how-to-reprovision.md). Quando terminar, você pode visualizar os dispositivos no [portal Azure](https://portal.azure.com) e verificar se eles estão no novo local.

Vá para o novo hub usando o [portal Azure](https://portal.azure.com). Selecione seu hub e selecione **Dispositivos IoT**. Você vê os dispositivos que foram reprovisionados para o hub clonado. Você também pode visualizar as propriedades do hub clonado. 

Se você implementou o roteamento, teste e certifique-se de que suas mensagens sejam encaminhadas para os recursos corretamente.

### <a name="committing-the-changes-after-using-dps"></a>Cometendo as alterações após o uso do DPS

Essa mudança foi cometida pelo serviço DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Reverter as alterações depois de usar DPS. 

Se você quiser reverter as alterações, reprovisione os dispositivos do novo hub para o antigo.

Você está agora terminando de migrar seu hub e seus dispositivos. Você pode pular para [limpeza](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Usando importação-exportação para mover os dispositivos para o novo hub

O aplicativo tem como alvo o .NET Core, para que você possa executá-lo no Windows ou Linux. Você pode baixar a amostra, recuperar suas strings de conexão, definir as bandeiras para quais bits você deseja executar e executá-la. Você pode fazer isso sem nunca abrir o código.

### <a name="downloading-the-sample"></a>Baixar o exemplo

1. Use as amostras ioT C# desta página: [Azure IoT Samples for C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Baixe o arquivo zip e descompacte-o no seu computador. 

1. O código pertinente está em ./iot-hub/Samples/service/ImportExportDevicesSample. Você não precisa visualizar ou editar o código para executar o aplicativo.

1. Para executar o aplicativo, especifique três strings de conexão e cinco opções. Você passa esses dados como argumentos de linha de comando ou usa variáveis de ambiente, ou usa uma combinação dos dois. Vamos passar as opções como argumentos de linha de comando, e as strings de conexão como variáveis de ambiente. 

   A razão para isso é porque as strings de conexão são longas e desajeitadas, e dificilmente mudarão, mas você pode querer mudar as opções e executar o aplicativo mais de uma vez. Para alterar o valor de uma variável de ambiente, você tem que fechar a janela de comando e o Visual Studio ou o Código VS, o que você estiver usando. 

### <a name="options"></a>Opções

Aqui estão as cinco opções especificadas ao executar o aplicativo. Colocaremos isso na linha de comando em um minuto.

*   **adicionarDispositivos** (argumento 1) -- defina isso como verdadeiro se você quiser adicionar dispositivos virtuais gerados para você. Estes são adicionados ao centro de origem. Além disso, defina **numToAdd** (argumento 2) para especificar quantos dispositivos você deseja adicionar. O número máximo de dispositivos que você pode registrar em um hub é de um milhão. O objetivo desta opção é testar -- você pode gerar um número específico de dispositivos e, em seguida, copiá-los para outro hub.

*   **copiarDispositivos** (argumento 3) -- configure isso como verdadeiro para copiar os dispositivos de um hub para outro. 

*   **exclusãoSourceDevices** (argumento 4) -- defina isso como verdadeiro para excluir todos os dispositivos registrados no hub de origem. Recomendamos esperar até que você tenha certeza de que todos os dispositivos foram transferidos antes de executar isso. Uma vez que você excluir os dispositivos, você não pode recuperá-los.

*   **excluirDestDevices** (argumento 5) -- configure isso como verdadeiro para excluir todos os dispositivos registrados no hub de destino (o clone). Você pode querer fazer isso se quiser copiar os dispositivos mais de uma vez. 

O comando básico será *executado dotnet* -- isso diz .NET para construir o arquivo csproj local e, em seguida, executá-lo. Você adiciona seus argumentos de linha de comando ao final antes de executá-lo. 

Sua linha de comando será parecida com estes exemplos:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Usando variáveis de ambiente para as strings de conexão

1. Para executar a amostra, você precisa das strings de conexão para os antigos e novos hubs de IoT, e para uma conta de armazenamento que você pode usar para arquivos de trabalho temporário. Armazenaremos os valores para estes em variáveis de ambiente.

1. Para obter os valores da seqüência de conexão, faça login no [portal Azure](https://portal.azure.com). 

1. Coloque as strings de conexão em algum lugar que você possa recuperá-las, como NotePad. Se você copiar o seguinte, você pode colar as strings de conexão diretamente para onde elas vão. Não adicione espaços ao redor do sinal de igual, ou ele altera o nome da variável. Além disso, você não precisa de aspas duplas em torno das strings de conexão. Se você colocar cotações em torno da seqüência de conexão da conta de armazenamento, não funcionará.

   Para o Windows, é assim que você define as variáveis do ambiente:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Para o Linux, é assim que você define as variáveis do ambiente:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Para as strings de conexão de hub IoT, vá para cada hub no portal. Você pode procurar em **Recursos** para o hub. Se você conhece o Grupo de recursos, você pode ir a **grupos de recursos,** selecionar seu grupo de recursos e, em seguida, selecionar o hub na lista de ativos desse grupo de recursos. 

1. Selecione políticas de **acesso compartilhado** nas Configurações do hub e selecione **iothubowner** e copie uma das strings de conexão. Faça o mesmo para o hub de destino. Adicione-os aos comandos SET apropriados.

1. Para a seqüência de conexão de conta de armazenamento, encontre a conta de armazenamento em **Resources** ou em seu **grupo Resource** e abra-a. 
   
1. Na seção Configurações, selecione **'Acessar', e** copie uma das strings de conexão'. Coloque a seqüência de conexões no arquivo de texto para o comando SET apropriado. 

Agora você tem as variáveis de ambiente em um arquivo com os comandos SET, e você sabe quais são seus argumentos de linha de comando. Vamos ver a amostra.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Executando o aplicativo de exemplo e usando argumentos de linha de comando

1. Abra una janela de prompt de comando. Selecione Windows `command prompt` e digite para obter a janela de solicitação de comando.

1. Copie os comandos que definem as variáveis do ambiente, uma de cada vez, e cole-as na janela de solicitação de comando e selecione Enter. Quando terminar, digite `SET` a janela de solicitação de comando para ver as variáveis do ambiente e seus valores. Depois de copiá-los na janela de solicitação de comando, você não precisa copiá-los novamente, a menos que você abra uma nova janela de solicitação de comando.

1. Na janela de solicitação de comando, altere diretórios até que você esteja em ./ImportExportDevicesSample (onde o arquivo ImportExportDevicesSample.csproj existe). Em seguida, digite o seguinte e inclua seus argumentos de linha de comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    O comando dotnet constrói e executa o aplicativo. Como você está passando as opções ao executar o aplicativo, você pode alterar os valores delas cada vez que executar o aplicativo. Por exemplo, você pode querer executá-lo uma vez e criar novos dispositivos, em seguida, executá-lo novamente e copiar esses dispositivos para um novo hub, e assim por diante. Você também pode executar todas as etapas na mesma execução, embora recomendamos não excluir nenhum dispositivo até ter certeza de que está terminada com a clonagem. Aqui está um exemplo que cria 1000 dispositivos e depois os copia para o outro hub.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Depois de verificar se os dispositivos foram copiados com sucesso, você pode remover os dispositivos do centro de origem assim:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Executando o aplicativo de exemplo usando o Visual Studio

1. Se você quiser executar o aplicativo no Visual Studio, altere seu diretório atual para a pasta onde o arquivo IoTHubServiceSamples.sln reside. Em seguida, execute este comando na janela de solicitação de comando para abrir a solução no Visual Studio. Você deve fazer isso na mesma janela de comando onde você define as variáveis do ambiente, para que essas variáveis sejam conhecidas.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Clique com o botão direito do mouse no projeto *ImportExportDevicesSample* e selecione **Definir como projeto de inicialização**.    
    
1. Defina as variáveis no topo da Program.cs na pasta ImportExportDevicesSample para as cinco opções.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Selecione F5 para executar o aplicativo. Depois que ele terminar de correr, você pode ver os resultados.

### <a name="view-the-results"></a>Exibir os resultados 

Você pode visualizar os dispositivos no [portal Azure](https://portal.azure.com) e verificar se eles estão no novo local.

1. Vá para o novo hub usando o [portal Azure](https://portal.azure.com). Selecione seu hub e selecione **Dispositivos IoT**. Você vê os dispositivos que você acabou de copiar do antigo hub para o hub clonado. Você também pode visualizar as propriedades do hub clonado. 

1. Verifique se há erros de importação/exportação indo até a conta de `devicefiles` armazenamento `ImportErrors.log`do Azure no [portal Azure](https://portal.azure.com) e procurando no contêiner o . Se este arquivo estiver vazio (o tamanho é 0), não houve erros. Se você tentar importar o mesmo dispositivo mais de uma vez, ele rejeitará o dispositivo na segunda vez e adicionará uma mensagem de erro ao arquivo de registro.

### <a name="committing-the-changes"></a>Cometendo as mudanças 

Neste ponto, você copiou seu hub para o novo local e migrou os dispositivos para o novo clone. Agora você precisa fazer alterações para que os dispositivos funcionem com o hub clonado.

Para realizar as alterações, aqui estão as etapas que você precisa executar: 

* Atualize cada dispositivo para alterar o nome de host do IoT Hub para apontar o nome do host IoT Hub para o novo hub. Você deve fazer isso usando o mesmo método que usou quando provisionou o dispositivo pela primeira vez.

* Altere todos os aplicativos que você tenha que se referem ao antigo hub para apontar para o novo hub.

* Depois que você terminar, o novo hub deve estar funcionando. O hub antigo não deve ter dispositivos ativos e estar em um estado desconectado. 

### <a name="rolling-back-the-changes"></a>Revertendo as mudanças

Se você decidir reverter as alterações, aqui estão as etapas a serem tomadas:

* Atualize cada dispositivo para alterar o nome host do IoT Hub para apontar o nome host host do IoT Hub para o hub antigo. Você deve fazer isso usando o mesmo método que usou quando provisionou o dispositivo pela primeira vez.

* Altere todos os aplicativos que você tenha que se referem ao novo hub para apontar para o hub antigo. Por exemplo, se você estiver usando o Azure Analytics, talvez seja necessário reconfigurar sua [entrada do Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Exclua o novo hub. 

* Se você tiver recursos de roteamento, a configuração no hub antigo ainda deve apontar para a configuração correta de roteamento e deve trabalhar com esses recursos depois que o hub for reiniciado.

### <a name="checking-the-results"></a>Verificando os resultados 

Para verificar os resultados, altere sua solução de IoT para apontar para o seu hub no novo local e executá-lo. Em outras palavras, execute as mesmas ações com o novo hub que você realizou com o hub anterior e certifique-se de que eles funcionem corretamente. 

Se você implementou o roteamento, teste e certifique-se de que suas mensagens sejam encaminhadas para os recursos corretamente.

## <a name="clean-up"></a>Limpar

Não limpe até ter certeza de que o novo hub está funcionando e os dispositivos estão funcionando corretamente. Certifique-se também de testar o roteamento se você estiver usando esse recurso. Quando estiver pronto, limpe os recursos antigos executando estas etapas:

* Se você ainda não o fez, exclua o antigo hub. Isso remove todos os dispositivos ativos do hub.

* Se você tiver recursos de roteamento que você moveu para o novo local, você pode excluir os recursos de roteamento antigos.

## <a name="next-steps"></a>Próximas etapas

Você clonou um hub de IoT em um novo hub em uma nova região, completo com os dispositivos. Para obter mais informações sobre a realização de operações em massa contra o registro de identidade em um Hub IoT, consulte [Importar e exportar identidades de dispositivos IoT Hub em massa](iot-hub-bulk-identity-mgmt.md).

Para obter mais informações sobre o IoT Hub e o desenvolvimento para o hub, consulte os seguintes artigos.

* [Guia do desenvolvedor do IoT Hub](iot-hub-devguide.md)

* [Tutorial de roteamento do IoT Hub](tutorial-routing.md)

* [Visão geral do gerenciamento de dispositivos IoT Hub](iot-hub-device-management-overview.md)

* Se você quiser implantar o aplicativo de amostra, consulte a [implantação do aplicativo .NET Core](https://docs.microsoft.com/dotnet/core/deploying/index).
