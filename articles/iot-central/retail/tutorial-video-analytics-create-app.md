---
title: Tutorial – Criar um aplicativo de detecção de objetos e movimentos e análise de vídeo no Azure IoT Central
description: Este tutorial mostra como criar um aplicativo de análise de vídeo no IoT Central. Você vai criá-lo, personalizá-lo e conectá-lo a outros serviços do Azure.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369562"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Tutorial: Criar um aplicativo de detecção de objetos e movimentos e análise de vídeo no Azure IoT Central

Como construtor de soluções, saiba como criar um aplicativo de análise de vídeo com o modelo de aplicativo de *detecção de objetos e movimentos e análise de vídeo* do IoT Central, os dispositivos Azure IoT Edge e os Serviços de Mídia do Azure. A solução usa uma loja de varejo para mostrar como atender à necessidade comercial comum de monitorar câmeras de segurança. A solução usa a detecção automática de objetos em um feed de vídeo para identificar e localizar rapidamente eventos interessantes.

O aplicativo de exemplo inclui dois dispositivos simulados e um gateway do IoT Edge. Os seguintes tutoriais mostram duas abordagens para experimentar e entender as funcionalidades do gateway:

* Crie o gateway do IoT Edge em uma VM do Azure e conecte uma câmera simulada.
* Crie o gateway do IoT Edge em um dispositivo real, como um Intel NUC, e conecte uma câmera real.

Neste tutorial, você aprende a:
> [!div class="checklist"]
> * Usar o modelo de aplicativo de análise de vídeo do Azure IoT Central para criar um aplicativo de loja de varejo
> * Personalizar as configurações de aplicativo
> * Criar um modelo de dispositivo para um dispositivo de gateway do IoT Edge
> * Adicionar um dispositivo de gateway ao seu aplicativo do IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).
* Se você estiver usando uma câmera real, precisará obter conectividade entre o dispositivo do IoT Edge e a câmera e ter acesso ao canal **Real Time Streaming Protocol**.

## <a name="initial-setup"></a>Instalação inicial

Nestes tutoriais, você atualizará e usará vários arquivos de configuração. As versões iniciais desses arquivos estão disponíveis no repositório GitHub do [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). O repositório também inclui um arquivo de texto de bloco de anotações para você baixar e usar e registrar os valores de configuração dos serviços implantados.

Crie uma pasta chamada *lva-configuration* no computador local para salvar cópias desses arquivos. Em seguida, clique com o botão direito do mouse em cada um dos seguintes links e escolha **Salvar como** para salvar o arquivo na pasta *lva-configuration*:

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> O repositório GitHub também inclui o código-fonte dos módulos **LvaEdgeGatewayModule** e **lvaYolov3** do IoT Edge. Para obter mais informações sobre como trabalhar com o código-fonte, confira [Criar os módulos do Gateway de LVA](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Implantar e configurar os Serviços de Mídia do Azure

A solução usa uma conta dos Serviços de Mídia do Azure para armazenar os videoclipes de detecção de objetos feitos pelo dispositivo de gateway do IoT Edge.

Ao criar a conta dos Serviços de Mídia:

- Você precisa fornecer um nome de conta, uma assinatura do Azure, uma localização, um grupo de recursos e uma conta de armazenamento. Crie uma conta de armazenamento usando as configurações padrão durante a criação da conta dos Serviços de Mídia.

- Escolha a região **Leste dos EUA** como a localização.

- Crie um grupo de recursos chamado *lva-rg* na região **Leste dos EUA** para os Serviços de Mídia e as contas de armazenamento. Quando você concluir os tutoriais, será fácil remover todos os recursos excluindo o grupo de recursos *lva-rg*.

Crie a [conta dos Serviços de Mídia no portal do Azure](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Estes tutoriais usam a região **Leste dos EUA** em todos os exemplos. Você poderá usar a região mais próxima se preferir.

Anote o nome da sua conta dos **Serviços de Mídia** no arquivo *scratchpad.txt*.

Quando a implantação for concluída, acesse a página **Propriedades** da sua conta dos **Serviços de Mídia**. Anote a **ID do Recurso** no arquivo *scratchpad.txt*; você usará esse valor posteriormente ao configurar o módulo do IoT Edge.

Em seguida, configure uma entidade de serviço do Azure Active Directory para o recurso dos Serviços de Mídia. Selecione **Acesso à API** e **Autenticação da entidade de serviço**. Crie um aplicativo do Azure Active Directory com o mesmo nome do recurso dos Serviços de Mídia e crie um segredo com a descrição *Acesso do IoT Edge*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

Quando o segredo for gerado, role a página para baixo até a seção **Copiar suas credenciais para conectar seu aplicativo de entidade de serviço**. Em seguida, selecione **JSON**. Você pode copiar todas as informações das credenciais aqui de uma só vez. Anote essas informações no arquivo *scratchpad.txt*; você as usará posteriormente ao configurar o dispositivo do IoT Edge.

> [!WARNING]
> Essa é sua única chance de ver e salvar o segredo. Se você perdê-lo, precisará gerar outro segredo.

## <a name="create-the-azure-iot-central-application"></a>Criar o aplicativo do Azure IoT Central

Nesta seção, você cria um novo aplicativo do Azure IoT Central com base em um modelo. Você usará esse aplicativo em toda a série de tutoriais para criar uma solução completa.

Para criar um novo aplicativo Azure IoT Central:

1. Navegue até o site do [Gerenciador de aplicativos do Azure IoT Central](https://aka.ms/iotcentral).

1. Entre com as credenciais usadas para acessar sua assinatura do Azure.

1. Para começar a criar um aplicativo do Azure IoT Central, selecione **Novo Aplicativo** na página **Criar**.

1. Selecione **Varejo**. A página de varejo exibe vários modelos de aplicativos de varejo.

Para criar um aplicativo de análise de vídeo:

1. Selecione o modelo de aplicativo de **detecção de objetos e movimentos e análise de vídeo**. Esse modelo inclui os modelos de todos os dispositivos usados no tutorial. O modelo inclui painéis de exemplo que os operadores podem usar para executar tarefas como monitoramento e gerenciamento de câmeras.

1. Opcionalmente, escolha um **Nome de aplicativo** amigável. Esse aplicativo se baseia em um loja de varejo fictícia chamada Northwind Traders. O tutorial usa o **Nome do aplicativo** *Análise de vídeo da Northwind Traders*.

    > [!NOTE]
    > Se você usar um **Nome de aplicativo** amigável, ainda precisará usar um valor exclusivo para a **URL** do aplicativo.

1. Caso você tenha uma assinatura do Azure, selecione **Diretório**, **assinatura do Azure** e **Estados Unidos** como a **Localização**. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de sete dias** e preencher as informações de contato necessárias. Este tutorial usa três dispositivos: duas câmeras e um dispositivo do IoT Edge; portanto, se você não usar a avaliação gratuita, será cobrado pelo uso.

    Para obter mais informações sobre diretórios, assinaturas e localizações, confira o [guia de início rápido Criar um aplicativo](../core/quick-deploy-iot-central.md).

1. Selecione **Criar**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

### <a name="retrieve-the-configuration-data"></a>Recuperar os dados de configuração

Mais adiante neste tutorial, ao configurar o gateway do IoT Edge, você precisará de alguns dados de configuração do aplicativo do IoT Central. O dispositivo do IoT Edge precisa dessas informações para se registrar no aplicativo e se conectar a ele.

Na seção **Administração**, escolha **Seu aplicativo** e anote a **URL do Aplicativo** e a **ID do Aplicativo** no arquivo *scratchpad.txt*:

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

Selecione **Tokens de API** e gere um novo token chamado **LVAEdgeToken** para a função **Operador**:

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

Anote o token no arquivo *scratchpad.txt* para uso posterior. Depois que a caixa de diálogo for fechada, você não poderá ver o token novamente.

Na seção **Administração**, escolha **Conexão do dispositivo** e selecione **SAS-IoT-Dispositivos**.

Anote a **Chave primária** dos dispositivos no arquivo *scratchpad.txt*. Você usará esse *token de Assinatura de Acesso Compartilhado do grupo primário* mais tarde ao configurar o dispositivo do IoT Edge.

## <a name="edit-the-deployment-manifest"></a>Editar o manifesto de implantação

Implante um módulo do IoT Edge usando um manifesto de implantação. No IoT Central, você pode importar o manifesto como um modelo de dispositivo e permitir que o IoT Central gerenciar a implantação do módulo.

Para preparar o manifesto de implantação:

1. Abra o arquivo *deployment.amd64.json*, que você salvou na pasta *lva-configuration*, usando um editor de texto.

1. Localize as configurações do `LvaEdgeGatewayModule` e altere o nome da imagem, conforme mostrado no seguinte snippet:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adicione o nome da sua conta dos Serviços de Mídia ao nó `env` da seção `LvaEdgeGatewayModule`. Você anotou o nome dessa conta no arquivo *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. O modelo não expõe essas propriedades no IoT Central; portanto, você precisa adicionar os valores de configuração dos Serviços de Mídia ao manifesto de implantação. Localize o módulo `lvaEdge` e substitua os espaços reservados pelos valores que você anotou no arquivo *scratchpad.txt* quando criou sua conta dos Serviços de Mídia.

    A `azureMediaServicesArmId` é a **ID do Recurso** que você anotou no arquivo *scratchpad.txt* quando criou a conta dos Serviços de Mídia.

    Você anotou a `aadTenantId`, a `aadServicePrincipalAppId` e o `aadServicePrincipalSecret` no arquivo *scratchpad.txt* quando criou a entidade de serviço para a sua conta dos Serviços de Mídia:

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Salve as alterações.

Opcionalmente, você pode substituir o módulo Yolov3 pelo Módulo de Extensão de IA de Borda – Servidor de Modelo [OpenVINO&trade; otimizado por hardware](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) da Intel. Baixe um exemplo de manifesto de implantação [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Esse manifesto substitui as definições de configuração do módulo `lvaYolov3` pela seguinte configuração:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Criar o dispositivo de gateway do Azure IoT Edge

O aplicativo de detecção de objetos e movimentos e análise de vídeo inclui um modelo de dispositivo **Detector de Objetos de Borda de LVA** e um modelo de dispositivo **Detector de Movimentos de Borda de LVA**. Nesta seção, você criará um modelo de dispositivo de gateway usando o manifesto de implantação e adicionará o dispositivo de gateway ao seu aplicativo do IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Criar um modelo de dispositivo para o Gateway de Borda de LVA

Para importar o manifesto de implantação e criar o modelo de dispositivo **Gateway de Borda de LVA**:

1. No aplicativo do IoT Central, procure **Modelos de Dispositivo** e selecione **+ Novo**.

1. Na página **Selecionar tipo de modelo**, selecione o bloco **Azure IoT Edge**. Em seguida, selecione **Avançar: Personalizar**.

1. Na página **Carregar um manifesto de implantação do Azure IoT Edge**, insira *Gateway de Borda de LVA* como o nome do modelo e marque a opção **Dispositivo de gateway com dispositivos downstream**.

    Não procure o manifesto de implantação ainda. Se você fizer isso, o assistente de implantação vai esperar uma interface para cada módulo, mas você só precisará expor a interface do **LvaEdgeGatewayModule**. Você carregará o manifesto em uma etapa posterior.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

    Selecione **Avançar: Análise**.

1. Na página **Examinar**, selecione **Criar**.

### <a name="import-the-device-capability-model"></a>Importar o modelo de funcionalidade do dispositivo

O modelo de dispositivo precisa incluir um modelo de funcionalidade do dispositivo. Na página **Gateway de Borda de LVA**, selecione o bloco **Importar modelo de funcionalidade**. Procure a pasta *lva-configuration* criada anteriormente e selecione o arquivo *LvaEdgeGatewayDcm.json*.

O modelo de dispositivo **Gateway de Borda de LVA** agora inclui o **Módulo do Gateway de Borda de LVA** juntamente com três interfaces: **Informações do dispositivo**, **Configurações do Gateway de Borda de LVA** e **Interface do Gateway de Borda de LVA**.

### <a name="replace-the-manifest"></a>Substituir o manifesto

Na página **Gateway de Borda de LVA**, selecione **+ Substituir manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

Procure a pasta *lva-configuration* e selecione o arquivo de manifesto *deployment.amd64.json* editado anteriormente. Escolha **Carregar**. Após a conclusão da validação, selecione **Substituir**.

### <a name="add-relationships"></a>Adicionar relações

No modelo de dispositivo **Gateway de Borda de LVA**, em **Módulos/Módulo do Gateway de Borda de LVA**, selecione **Relações**. Selecione **+ Adicionar relação** e adicione as duas seguintes relações:

|Nome de exibição               |Nome          |Destino |
|-------------------------- |------------- |------ |
|Detector de Movimentos de Borda de LVA   |Usar padrão   |Dispositivo de Detecção de Movimentos de Borda de LVA |
|Detector de Objetos de Borda de LVA   |Usar padrão   |Dispositivo de Detecção de Objetos de Borda de LVA |

Em seguida, selecione **Salvar**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

### <a name="add-views"></a>Adicionar exibições

O modelo de dispositivo **Gateway de Borda de LVA** não inclui nenhuma definição de exibição.

Para adicionar uma exibição ao modelo de dispositivo:

1. No modelo de dispositivo **Gateway de Borda de LVA**, procure **Exibições** e selecione o bloco **Visualização do dispositivo**.

1. Insira *Dispositivo de Gateway de Borda de LVA* como o nome de exibição.

1. Adicione os seguintes blocos à exibição:

    * Um bloco com as propriedades **Informações do Dispositivo**: **Modelo do dispositivo**, **Fabricante**, **Sistema operacional**, **Arquitetura do processador**, **Versão do software**, **Memória total** e **Armazenamento total**.
    * Um bloco de gráfico de linhas com os valores de telemetria de **Memória Livre** e **Pulsação do Sistema**.
    * Um bloco de histórico de eventos com os seguintes eventos: **Criar Câmera**, **Excluir Câmera**, **Reinicialização do Módulo**, **Módulo Iniciado** e **Módulo Interrompido**.
    * Um bloco 2x1 do último valor conhecido mostrando a telemetria de **Estado do Cliente do IoT Central**.
    * Um bloco 2x1 do último valor conhecido mostrando a telemetria de **Estado do Módulo**.
    * Um bloco 1x1 do último valor conhecido mostrando a telemetria de **Pulsação do Sistema**.
    * Um bloco 1x1 do último valor conhecido mostrando a telemetria de **Câmeras Conectadas**.

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Configurar o aplicativo do AAD para o AMS":::

1. Clique em **Salvar**.

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Para adicionar um dispositivo ao aplicativo, publique o modelo de dispositivo:

1. No modelo de dispositivo **Gateway de Borda de LVA**, selecione **Publicar**.

1. Na página **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

O **Gateway de Borda de LVA** agora está disponível como o tipo de dispositivo a ser usado na página **Dispositivos** no aplicativo.

## <a name="add-a-gateway-device"></a>Adicionar um dispositivo de gateway

Para adicionar um dispositivo **Gateway de Borda de LVA** ao aplicativo:

1. Procure a página **Dispositivos** e selecione o modelo de dispositivo **Gateway de Borda de LVA**.

1. Selecione **+ Novo**.

1. Na caixa de diálogo **Criar um dispositivo**, altere o nome do dispositivo para *Gateway de LVA 001* e a identificação do dispositivo para *lva-gateway-001*.

    > [!NOTE]
    > A identificação do dispositivo precisa ser exclusiva no aplicativo.

1. Selecione **Criar**.

O status do dispositivo é **Registrado**.

### <a name="get-the-device-credentials"></a>Obter as credenciais do dispositivo

Você precisará das credenciais que permitem que o dispositivo se conecte ao seu aplicativo do IoT Central. Para obter as credenciais do dispositivo:

1. Na página **Dispositivos**, selecione o dispositivo **lva-gateway-001** criado.

1. Selecione **Conectar**.

1. Na página **Conexão do dispositivo**, anote no arquivo *scratchpad.txt* o **Escopo da ID**, a **Identificação do Dispositivo** e a **Chave Primária** do dispositivo. Esses valores serão usados depois.

1. Verifique se o método de conexão está definido como **Assinatura de Acesso Compartilhado**.

1. Selecione **Fechar**.

## <a name="next-steps"></a>Próximas etapas

Você acabou de criar um aplicativo do IoT Central usando o modelo de aplicativo de **detecção de objetos e movimentos e análise de vídeo** e um modelo de dispositivo para o dispositivo de gateway, além de adicionar um dispositivo de gateway ao aplicativo.

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam uma VM de nuvem com fluxos de vídeo simulados:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)](./tutorial-video-analytics-iot-edge-vm.md)

Caso deseje experimentar o aplicativo de detecção de objetos e movimentos e análise de vídeo usando módulos do IoT Edge que executam um dispositivo real com uma câmera **ONVIF** real:

> [!div class="nextstepaction"]
> [Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
