---
title: 'Início Rápido: Implantar o Conector IoT (versão prévia) usando o portal do Azure'
description: Neste guia de início rápido, você aprenderá a implantar, configurar e usar o recurso do Conector IoT da API do Azure para FHIR por meio do portal do Azure.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: 95f5b5f13401c224ccf67c5f013deedf00379de7
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446807"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>Início Rápido: Implantar o Conector IoT (versão prévia) usando o portal do Azure

O Conector IoT é um recurso opcional da API do Azure para FHIR que fornece a capacidade de ingerir dados de dispositivos IoMT (Internet das Coisas Médicas). Além disso, durante a fase de versão prévia, o recurso Conector IoT fica disponível gratuitamente. Neste início rápido, você aprenderá a:
- Implantar e configurar o Conector IoT usando o portal do Azure
- Usar um dispositivo simulado para enviar dados ao Conector IoT
- Exibir os recursos criados pelo Conector IoT na API do Azure para FHIR

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura ativa do Azure: [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Recurso da API do Azure para FHIR – [Implantar a API do Azure para FHIR usando o portal do Azure](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Acessar o recurso da API do Azure para FHIR

Abra o [portal do Azure](https://portal.azure.com) e acesse o recurso da **API do Azure para FHIR** para o qual deseja criar o recurso do Conector IoT.

[![Recurso da API do Azure para FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

No menu de navegação à esquerda, clique em **Conector IoT (versão prévia)** na seção **Suplementos** para abrir a página **Conectores IoT**.

[![Recurso do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>Criar um Conector IoT (versão prévia)

Clique no botão **Adicionar** para abrir a página **Criar Conector IoT**.

[![Adicionar Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Insira as configurações do novo Conector IoT. Clique no botão **Criar** e aguarde a implantação do Conector IoT.

> [!NOTE]
> É necessário selecionar **Criar** como o valor no menu suspenso do **Tipo de resolução** para esta instalação. 

[![Criar Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Configuração|Valor|Descrição |
|---|---|---|
|Nome do conector|Um nome exclusivo|Insira um nome para identificar o Conector IoT. Esse nome deve ser exclusivo em um recurso da API do Azure para FHIR. O nome pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa começar e terminar com uma letra ou um número e precisa ter entre 3 e 24 caracteres.|
|Tipo de resolução|Pesquisa ou criação|Selecione **Pesquisa** se você tiver um processo fora de banda para criar recursos FHIR de [Dispositivo](https://www.hl7.org/fhir/device.html) e [Paciente](https://www.hl7.org/fhir/patient.html) na API do Azure para FHIR. O Conector IoT usará a referência a esses recursos ao criar um recurso FHIR de [Observação](https://www.hl7.org/fhir/observation.html) para representar os dados do dispositivo. Selecione **Criar** quando desejar que o Conector IoT crie recursos básicos de Dispositivo e de Paciente na API do Azure para FHIR usando os respectivos valores de identificador presentes nos dados do dispositivo.|

Quando a instalação for concluída, o Conector IoT recém-criado será exibido na página **Conectores IoT**.

[![Conector IoT criado](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>Configurar o Conector IoT (versão prévia)

O Conector IoT precisa de dois modelos de mapeamento para transformar as mensagens do dispositivo em recursos de Observação baseados no FHIR: **mapeamento de dispositivo** e **mapeamento FHIR**. O Conector IoT não ficará totalmente operacional até que esses mapeamentos sejam carregados.

[![Mapeamentos ausentes do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Para carregar modelos de mapeamento, clique no Conector IoT recém-implantado para acessar a página **Conector IoT**.

[![Clique no Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Mapeamento de dispositivo

O modelo de mapeamento de dispositivo transforma os dados do dispositivo em um esquema normalizado. Na página **Conector IoT**, clique no botão **Configurar mapeamento de dispositivo** para acessar a página **Mapeamento de dispositivo**. 

[![Clique no Conector IoT e Configurar mapeamento de dispositivo](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Na página **Mapeamento de dispositivo**, adicione o script a seguir ao editor de JSON e clique em **Salvar**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Mapeamento de dispositivo do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>Mapeamento FHIR

O modelo de mapeamento FHIR transforma uma mensagem normalizada em um recurso de Observação baseado no FHIR. Na página **Conector IoT**, clique no botão **Configurar mapeamento FHIR** para acessar a página **Mapeamento FHIR**.  

[![Clique no Conector IoT e Configurar mapeamento FHIR](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Na página **Mapeamento FHIR**, adicione o script a seguir ao editor de JSON e clique em **Salvar**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Mapeamento FHIR do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Criar uma cadeia de conexão

O dispositivo IoMT precisa de uma cadeia de conexão para se conectar e enviar mensagens ao Conector IoT. Na página **Conector IoT** do Conector IoT recém-implantado, selecione o botão **Gerenciar conexões de cliente**. 

[![Clique no Conector IoT e Gerenciar conexões de cliente](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Na página **Conexões**, clique no botão **Adicionar** para criar uma conexão. 

[![Conexões do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Forneça um nome amigável para essa conexão na janela de sobreposição e selecione o botão **Criar**.

[![Nova conexão do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Selecione a conexão recém-criada na página **Conexões** e copie o valor do campo **Cadeia de conexão primária** da janela de sobreposição à direita.

[![Cadeia de conexão do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Preserve essa cadeia de conexão, pois ela será usada em uma etapa posterior. 

## <a name="connect-your-devices-to-iot"></a>Conectar seus dispositivos à IoT

O Azure oferece um amplo pacote de produtos de IoT para conectar e gerenciar seus dispositivos IoT. Você pode criar uma solução própria baseada em PaaS usando o Hub IoT do Azure ou comece a gerenciar uma plataforma de aplicativos IoT com o Azure IoT Central. Para este tutorial, aproveitaremos o Azure IoT Central, que tem modelos de solução voltados para o setor para ajudar você a começar.

Implante o [Modelo de aplicativo de monitoramento contínuo de pacientes](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template). Esse modelo inclui dois dispositivos simulados que produzem dados em tempo real para ajudar você a começar: **Patch de Sinais Vitais Inteligente** e **Braçadeira de Joelho Inteligente**.

> [!NOTE]
> Sempre que os seus dispositivos reais estiverem prontos, você poderá usar o mesmo aplicativo IoT Central para [integrar seus dispositivos](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) e substituir os simuladores de dispositivo. Os dados do dispositivo também começarão a fluir automaticamente para o FHIR. 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>Conectar seus dados de IoT ao Conector IoT (versão prévia)
Depois de implantar seu aplicativo do IoT Central, seus dois dispositivos simulados prontos para uso começarão a gerar telemetria. Para este tutorial, vamos ingerir a telemetria do simulador *Patch de Sinais Vitais Inteligente* no FHIR por meio do Conector IoT. Para exportar seus dados de IoT para o Conector IoT, o ideal é [configurarmos uma exportação contínua de dados no IoT Central](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export). Na página de exportação contínua de dados:
- Escolha *Hubs de Eventos do Azure* como o destino de exportação.
- Selecione o valor *Usar uma cadeia de conexão* para o campo **Namespace dos Hubs de Eventos**.
- Forneça a cadeia de conexão do Conector IoT obtida em uma etapa anterior para o campo **Cadeia de Conexão**.
- Mantenha a opção **Telemetria** *Ativada* para o campo **Dados a serem exportados**.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver os dados do dispositivo na API do Azure para FHIR

Veja os recursos de Observação baseados no FHIR criados pelo Conector IoT na API do Azure para FHIR usando o Postman. Configure o [Postman para acessar a API do Azure para FHIR](access-fhir-postman-tutorial.md) e faça uma solicitação `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` para ver os recursos de Observação do FHIR com o valor de frequência cardíaca. 

> [!TIP]
> Verifique se o usuário tem acesso apropriado ao plano de dados da API do Azure para FHIR. Use o [Azure RBAC (controle de acesso baseado em função do Azure)](configure-azure-rbac.md) para atribuir as funções de plano de dados necessárias.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua uma instância do Conector IoT removendo o grupo de recursos associado, o serviço da API do Azure para FHIR ou a própria instância do Conector IoT quando eles não forem mais necessários. 

Para remover uma instância do Conector IoT diretamente, selecione a instância na página **Conectores IoT** para acessar a página **Conector IoT** e clique no botão **Excluir**. Selecione **Sim** quando a confirmação for solicitada. 

[![Excluir a instância do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou o recurso do Conector IoT no recurso da API do Azure para FHIR. Selecione uma das próximas etapas abaixo para saber mais sobre o Conector IoT:

Entenda os diferentes estágios do fluxo de dados no Conector IoT.

>[!div class="nextstepaction"]
>[Fluxo de dados do Conector IoT](iot-data-flow.md)

Saiba como configurar o Conector IoT usando modelos de mapeamento FHIR e do dispositivo.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do Conector IoT](iot-mapping-templates.md)

FHIR é uma marca registrada da HL7, usada com permissão da HL7.
