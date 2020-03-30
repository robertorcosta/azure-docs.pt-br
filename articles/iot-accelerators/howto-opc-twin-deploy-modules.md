---
title: Como implantar o módulo OPC Twin para Ozure do zero | Microsoft Docs
description: Este artigo descreve como implantar o OPC Twin do zero usando a lâmina IoT Edge do portal Azure e também usando a Cli AZ.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241070"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implantar módulo OPC Twin e dependências do zero

O módulo OPC Twin é executado no IoT Edge e fornece vários serviços de borda para os serviços de registro e gêmeos de dispositivos OPC. 

Existem várias opções para implantar módulos no [seu Gateway de Borda IoT do Azure,](https://azure.microsoft.com/services/iot-edge/) entre eles

- [Implantando a partir da lâmina IoT Edge do portal Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Implantação usando AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Para obter mais informações sobre detalhes e instruções de implantação, consulte o [repositório](https://github.com/Azure/azure-iiot-components)GitHub .

## <a name="deployment-manifest"></a>Manifesto de implantação

Todos os módulos são implantados usando um manifesto de implantação.  Um manifesto de exemplo para implantar tanto [o OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) quanto [o OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) é mostrado abaixo.

```json
{
  "content": {
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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Implantação do portal Azure

A maneira mais fácil de implantar os módulos em um dispositivo gateway Azure IoT Edge é através do portal Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implante as [dependências](howto-opc-twin-deploy-dependencies.md) OPC Twin e `.env` obtive o arquivo resultante. Observe a `hub name` implantação `PCS_IOTHUBREACT_HUB_NAME` da variável `.env` no arquivo resultante.

2. Registre-se e inicie um gateway [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ou `device id` [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge e observe sua .

### <a name="deploy-to-an-edge-device"></a>Implantar em um dispositivo de borda

1. Faça login no [portal Azure](https://portal.azure.com/) e navegue até o seu hub de IoT.

2. Selecione **IoT Edge** no menu à esquerda.

3. Clique no ID do dispositivo alvo da lista de dispositivos.

4. Selecione **Módulos de conjunto**.

5. Na seção **Módulos** de Implantação da página, **selecione Adicionar** e **IoT Edge Module.**

6. Na caixa de diálogo **IoT Edge Custom Module,** use `opctwin` como nome para o módulo e, em seguida, especifique o *URI de imagem* do contêiner como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *opções de criação de contêineres,* use o seguinte JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Preencha os campos opcionais se necessário. Para mais informações sobre opções de criação de contêiner, políticas de reinício, e status desejados consulte [propriedades desejadas do EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selecione **Salvar** e repetir o passo **5**.  

8. Na caixa de diálogo IoT `opcpublisher` Edge Custom Module, use como nome para o módulo e o *URI da imagem* do contêiner como 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *opções de criação de contêineres,* use o seguinte JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecione **Salvar** e, em seguida, **Seguinte** para continuar para a seção rotas.

10. Na guia rotas, cole o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selecione **Next**

11. Revise suas informações de implantação e se manifeste.  Deve parecer o manifesto de implantação acima.  Selecione **Enviar**.

12. Após implantar os módulos no dispositivo, será possível exibir todos eles na seção **Dispositivos do Hub IoT**. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída.

## <a name="deploying-using-azure-cli"></a>Implantação usando o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente da interface de linha de [comando Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [daqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Guia de Início Rápido

1. Salve o manifesto de `deployment.json` implantação acima em um arquivo.  

2. Use o comando a seguir para aplicar a configuração a um dispositivo do IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O `device id` parâmetro é sensível a maiúsculas e minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou. 
    ![az IoT Edge set-modules saída](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Após implantar os módulos no dispositivo, será possível exibir todos eles com o comando a seguir:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro de id do dispositivo é sensível a maiúsculas e minúsculas. ![saída da lista de identidade do módulo hub iot az](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o OPC Twin do zero, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Implantar o OPC Twin em um projeto existente](howto-opc-twin-deploy-existing.md)
