---
title: Definir um novo tipo de dispositivo Azure IoT Edge no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como construtor, como criar um dispositivo Azure IoT Edge em seu aplicativo do Azure IoT Central. Você define a telemetria, estado, propriedades e comandos para o seu tipo.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892022"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: Definir um novo tipo de dispositivo Azure IoT Edge no aplicativo do Azure IoT Central (versão prévia dos recursos)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra a você, como um construtor, como usar um modelo de dispositivo para definir um novo tipo de dispositivo Azure IoT Edge no seu aplicativo do Azure IoT Central. 

Para obter uma visão geral do Azure IoT Edge, [consulte este artigo](overview-iot-central.md). 

O Azure IoT Edge é composto de três componentes:
* Os **módulos do IoT Edge** são contêineres que executam serviços do Azure, serviços de terceiros ou o seu próprio código. Os módulos são implantados em dispositivos do IoT Edge e executados localmente nesses dispositivos.
* O **runtime do IoT Edge** é executado em cada dispositivo IoT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite monitorar e gerenciar dispositivos do IoT Edge remotamente. IoT Central será a interface de nuvem.

Um dispositivo **Azure IoT Edge** pode ser um dispositivo de gateway com dispositivos downstream conectados ao dispositivo Azure IoT Edge. Os padrões de conectividade do dispositivo downstream serão discutidos neste tutorial.

Um **modelo de dispositivo** define as funcionalidades do seu dispositivo e dos módulos IoT Edge. As funcionalidades incluem telemetria que o módulo envia, as propriedades do módulo e os comandos aos quais um módulo responde.

Neste tutorial, você criará um modelo de dispositivo **Sensor de Ambiente**. Um dispositivo de sensor ambiental:

* Envia telemetria como a temperatura.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos como a redefinição da temperatura.

Além disso, neste tutorial, você criará um modelo de dispositivo de **Gateway de Ambiente**. Um dispositivo de gateway ambiental:

* Envia telemetria como a temperatura.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos como a redefinição da temperatura.
* Permite relações com outros modelos de funcionalidade do dispositivo


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de dispositivo Azure IoT Edge.
> * Carregar um manifesto de implantação.
> * Criar funcionalidades, incluindo telemetria, propriedades e comandos para cada módulo
> * Definir uma visualização para a telemetria do módulo.
> * Adicionar relações aos modelos de dispositivo downstream
> * Publicar seu modelo de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Siga este início rápido para [Criar um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Relações de dispositivo downstream com gateway e módulos

Os dispositivos downstream podem se conectar a um dispositivo de gateway do Azure IoT Edge por meio do módulo $edgeHub. Esse dispositivo Azure IoT Edge se torna um gateway transparente neste cenário

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Os dispositivos downstream podem se conectar a um dispositivo de gateway do Azure IoT Edge por meio de um módulo personalizado. No cenário abaixo, os dispositivos downstream estão se conectando por meio de um módulo personalizado Modbus e os dispositivos downstream podem se conectar ao dispositivo de gateway do Azure IoT Edge por meio do módulo $edgeHub.

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-module.png)

Os dispositivos downstream podem se conectar a um dispositivo de gateway do Azure IoT Edge por meio de um módulo personalizado. No cenário abaixo, os dispositivos downstream estão se conectando por meio de um módulo personalizado Modbus. 

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Os dispositivos downstream podem se conectar a um dispositivo de gateway do Azure IoT Edge por meio de vários módulos personalizados. No cenário abaixo, os dispositivos downstream estão se conectando por meio de um módulo personalizado Modbus, um módulo personalizado BLE e os dispositivos downstream podem se conectar ao dispositivo de gateway do Azure IoT Edge por meio do módulo $edgeHub. 

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Criar um modelo

Como um construtor, você pode criar e editar os modelos de dispositivo Azure IoT Edge no seu aplicativo. Depois de publicar um modelo de dispositivo, você pode conectar dispositivos reais que implementam o modelo de dispositivo.

### <a name="select-device-template-type"></a>Selecionar um tipo de modelo de dispositivo 

Para adicionar um novo modelo de dispositivo ao seu aplicativo, vá até a página **Modelos de Aplicativo**. Para fazer isso, selecione a guia **Modelos de Dispositivo** no painel esquerdo.

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Clique em **+ Novo** para começar a criar um modelo de dispositivo.

![Modelos de dispositivo – Novo](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Você será direcionado à página de seleção do tipo de modelo de dispositivo. Selecione o bloco **Azure IoT Edge** e clique no botão **Avançar: Personalizar** na parte inferior

![Seleção de modelos de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personalizar modelo de dispositivo

O Azure IoT Edge permite implantar e gerenciar a lógica de negócios na forma de módulos. Os **módulos do Azure IoT Edge** são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (por exemplo, o Azure Stream Analytics) ou o código específico da sua própria solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, leia [Módulos de IoT Edge](../../iot-edge/iot-edge-modules.md).

Em um alto nível, um manifesto de implantação é uma lista de módulos gêmeos que são configurados com suas propriedades desejadas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais módulos instalar e como configurá-los. Manifestos de implantação incluem as propriedades desejadas para cada módulo gêmeo. Dispositivos do IoT Edge relatam de volta as propriedades relatadas para cada módulo.

Use o Visual Studio Code para criar um manifesto de implantação. Siga a documentação sobre como criar um [manifesto de implantação](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo a ser usado para este tutorial. Copie o JSON abaixo e salve-o como arquivo .json. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

**Carregar um manifesto de implantação do Azure IoT Edge**

Clique no botão **Procurar** 

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Se você planeja criar um modelo de dispositivo de gateway do Azure IoT Edge, marque a caixa de seleção **Dispositivo de gateway com dispositivos downstream**

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Uma caixa de diálogo de seleção de arquivo será exibida. Selecione o arquivo de manifesto de implantação e clique no botão **Abrir**.

O arquivo de manifesto de implantação será validado em relação a um esquema. Após a validação bem-sucedida, clique no botão **Revisão**

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Veja abaixo o fluxo do ciclo de vida de um manifesto de implantação no IoT Central.

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

A página Revisão é exibida com detalhes do manifesto de implantação. A lista de módulos do manifesto de implantação será exibida na página de revisão. Neste tutorial, você verá o módulo SimulatedTemperatureSensor listado. Clique no botão **Criar**.

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Se você selecionou dispositivo de gateway, verá esta página de revisão

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


A criação de um controle giratório de modelo de dispositivo será exibida em que o modelo de dispositivo está sendo criado no IoT Central.

O modelo de dispositivo é criado com modelos de capacidade do módulo. Neste tutorial, você verá o modelo de capacidade do módulo SimulatedTemperatureSensor criado. 

Altere o título do modelo de dispositivo para modelo de dispositivo do sensor de ambiente.

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

A modelagem do dispositivo plug and play Azure IoT Edge é feita da seguinte maneira
* Cada modelo de dispositivo Azure IoT Edge terá um **modelo de capacidade de dispositivo**
* Para cada módulo personalizado listado no manifesto de implantação, um **modelo de capacidade do módulo** será gerado
* Uma **relação** será estabelecida entre cada modelo de capacidade do módulo e um modelo de funcionalidade do dispositivo
* O modelo de capacidade do módulo implementa **interfaces de módulo**
* Cada interface de módulo contém
   - Telemetria
   - propriedades
   - Comandos

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Adicionar funcionalidades ao modelo de capacidade do módulo**

Veja um exemplo de saída do módulo SimulatedTemperatureSensor
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Adicione funcionalidades ao módulo SimulatedTemperatureSensor, que refletirá o JSON acima. 

* Clique em **Gerenciar** uma interface do modelo de capacidade do módulo SimulatedTemperatureSensor. Clique em **Adicionar Capacidade**. 

    ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Adicionar computador como um tipo de objeto, pois ele é um tipo complexo
  
    ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Clique em **Definir**. No pop-up modal, altere o nome do objeto para o computador e crie propriedades de temperatura, pressão e clique em **Aplicar**
  
    ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Adicionar ambiente como um tipo de objeto, pois ele é um tipo complexo

    Clique em **Definir**. No pop-up modal, altere o nome do objeto para o ambiente e crie propriedades de temperatura, umidade e clique em **Aplicar**
  
    ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Adicione timeCreated como um tipo Data/Hora e clique em **Salvar**
  
    ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Adicionar relações

Se você selecionou o dispositivo Azure IoT Edge como um dispositivo de gateway, poderá adicionar relações de downstream aos modelos de funcionalidade do dispositivo para os dispositivos que serão conectados ao dispositivo de gateway.
  
  ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

A relação pode ser adicionada em um dispositivo ou em um módulo.
  
  ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Você pode selecionar um modelo de funcionalidade do dispositivo downstream ou selecionar asterisco. 
  
  ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Para este tutorial, selecionaremos asterisco, o que significa que qualquer relacionamento downstream será permitido. Clique em **Salvar**

  ![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades da Nuvem** e, em seguida, **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao seu modelo de dispositivo.

    | Nome de exibição      | Tipo Semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data do Último Serviço | Nenhum          | Data   |
    | Nome do cliente     | Nenhum          | Cadeia de caracteres |

2. Selecione **Salvar** para salvar as alterações:

  
    ![Propriedades de nuvem – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface ou adicionar recursos específicos de IoT Central a uma funcionalidade que não exige uma nova versão do seu modelo de funcionalidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, você pode:

- Personalizar o nome de exibição e as unidades de uma funcionalidade.
- Adicionar uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especificar os valores iniciais, mínimos e máximos, para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Clique em **Salvar**
  
![Personalizações – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo de sensor ambiental para um operador. Suas personalizações habilitam o operador a gerenciar os dispositivos de sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="configure-a-view-to-visualize-devices"></a>Configurar um modo de exibição para visualizar dispositivos

Um painel de dispositivos permite que um operador visualize um dispositivo usando gráficos e métricas. Como um construtor, você pode definir quais informações são exibidas em um painel do dispositivo. Você pode definir vários painéis para os dispositivos. Para criar um painel para visualizar a telemetria do sensor ambiental, selecione **Modo de exibição** e, em seguida, **Visualizando o dispositivo**:

  
![Exibições – Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Telemetria de ambiente e telemetria de computador são objetos complexos; para criar gráficos, faça o seguinte

Arraste a telemetria de ambiente e selecione gráfico de linhas. 
  
![Exibições – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Clique no ícone Configurar e selecione temperatura e umidade para visualizar os dados e clique no botão **Atualizar configuração**. 
  
![Exibições – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Selecione **Salvar** para salvar sua exibição:

Você pode adicionar mais blocos que mostram outras propriedades ou valores de telemetria. Você também pode adicionar texto estático, links e imagens. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco e solte-o para uma nova localização ou para redimensioná-lo.
  
![Exibições – Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Adicionar um formulário de dispositivo

Um formulário de dispositivo permite que um operador edite as propriedades de dispositivo graváveis e as propriedades de nuvem. Como um construtor, você pode definir vários formulários e escolher quais propriedades de dispositivo e de nuvem mostrar em cada formulário. Você também pode exibir propriedades de dispositivo somente leitura em um formulário.

Para criar um formulário para exibir e editar as propriedades do sensor ambiental:

Navegue até **Exibições** no modelo **Sensor Ambiental**. Selecione o bloco **Editando dados de Dispositivo e de Nuvem** para adicionar uma nova exibição.
  
![Exibições – Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Insira o nome do formulário **Propriedades do Sensor Ambiental**.

Arraste as propriedades da nuvem **Nome do cliente** e **Última data de serviço** até a seção existente no formulário.
  
![Exibições – Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Selecione **Salvar** para salvar sua exibição.

### <a name="generate-default-views"></a>Gerar modos de exibição padrão

Não há suporte para a funcionalidade gerar modos de exibição padrão para modelos do Azure IoT Edge 

## <a name="publish-device-template"></a>Publicar um modelo de dispositivo

Antes de criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para seu modelo de dispositivo na página **Modelos de Dispositivo**.

2. Selecione **Publicar**.
  
    ![Exibições – Publicar](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Na caixa de diálogo **Publicar um Modelo de Dispositivo** escolha **Publicar**:
  
    ![Exibições – Publicar](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Depois que um modelo de dispositivo é publicado, ele fica visível na página **Dispositivos** e para o operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de funcionalidade do dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, nas personalizações e nos modos de exibição, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer qualquer alteração, selecione **Publicar** para enviar por push essas alterações para o operador.
  
![Exibições – Publicar](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

* Criar uma borda como um modelo de dispositivo de folha
* Gerar módulos de um manifesto de implantação carregado
* Adicionar propriedades e telemetria de tipo complexo
* Criar propriedades da nuvem.
* Criar personalizações.
* Definir uma visualização para a telemetria do dispositivo.
* Publicar seu modelo de dispositivo de borda.

Agora que você criou um modelo de dispositivo no seu aplicativo do Azure IoT Central, estas são as próximas etapas sugeridas:

> [!div class="nextstepaction"]
> [Conectar dispositivo](./tutorial-connect-pnp-device.md)
