---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844574"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implantar módulo de borda IoT da grade de eventos

Existem várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para o Azure Event Grid no IoT Edge. Este artigo descreve as etapas para implantar a Event Grid no IoT Edge a partir do portal Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo Event Grid sem persistência. Isso significa que todos os tópicos e assinaturas que você criar neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persist state in Linux](../articles/event-grid/edge/persist-state-linux.md) ou Persist state in [Windows](../articles/event-grid/edge/persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo Event Grid com persistência.

>[!IMPORTANT]
> Neste tutorial, o módulo Event Grid será implantado com a autenticação do cliente desacionada e permitirá que os assinantes HTTP. Para cargas de trabalho de produção, recomendamos que você habilite apenas solicitações HTTPS e assinantes com a autenticação do cliente ativada. Para obter mais informações sobre como configurar o módulo Event Grid com segurança, consulte [Segurança e autenticação](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Entre no [portal do Azure](https://portal.azure.com)
1. Navegue até seu Hub IoT.
1. Selecione **IoT Edge** no menu na seção **Gerenciamento automático de dispositivos.** 
1. Clique no ID do dispositivo de destino da lista de dispositivos
1. Selecione **Módulos de conjunto**. Mantenha a página aberta. Você continuará com os passos na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal Azure tem um assistente que orienta você a criar um manifesto de implantação, em vez de construir o documento JSON manualmente.  Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **Módulos de implantação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de paradas, selecione **IoT Edge Module**
1. Forneça o nome, imagem, opções de criação de contêiner do contêiner:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nome**: eventgridmodule
   * **Uri de imagem**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de Criação de Contêiner**:

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Clique em **Salvar**
 1. Clique **em Next** para continuar na seção rotas

    > [!NOTE]
    > Se você estiver usando uma VM Do Azure como um dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [Como abrir portas em uma VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Rotas de configuração

 Mantenha as rotas padrão e selecione **'Seguinte'** para continuar até a seção de revisão

### <a name="review-deployment"></a>Rever implantação

1. A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Confirme se você vê os dois módulos na lista: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o runtime do IoT Edge e são padrões requeridos em toda implantação.
1. Examine as informações da implantação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verifique sua implantação

1. Depois de enviar a implantação, você volta para a página do IoT Edge de seu hub IoT.
1. Selecione o **dispositivo IoT Edge** que você direcionou com a implantação para abrir seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo Event Grid está listado como **especificado na implantação** e **relatado pelo dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.