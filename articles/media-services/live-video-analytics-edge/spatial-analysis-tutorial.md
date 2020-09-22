---
title: Análise Dinâmica de Vídeo com a Pesquisa Visual Computacional para Análise Espacial – Azure
description: Este tutorial mostra como usar a Análise Dinâmica de Vídeo junto com o recurso de IA de Pesquisa Visual Computacional para análise espacial dos Serviços Cognitivos do Azure para analisar um feed de vídeo ao vivo de uma câmera IP (simulada).
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929219"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Análise Dinâmica de Vídeo com a Pesquisa Visual Computacional para Análise Espacial (versão prévia)

Este tutorial mostra como usar a Análise Dinâmica de Vídeo junto com o [serviço de IA de Pesquisa Visual Computacional para análise espacial dos Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para analisar um feed de vídeo ao vivo de uma câmera IP (simulada). Você verá como esse servidor de inferência permite analisar o vídeo de streaming para entender as relações espaciais entre as pessoas e o movimento no espaço físico.  Um subconjunto dos quadros no feed de vídeo é enviado para esse servidor de inferência e os resultados são enviados para o Hub do IoT Edge. Quando algumas condições são atendidas, os clipes de vídeo são gravados e armazenados como ativos dos Serviços de Mídia do Azure.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurar recursos.
> * Examine o código.
> * Executar o código de exemplo.
> * Monitorar eventos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

Leia estes artigos antes de começar:

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceitos de grafo de mídia](media-graph-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Tutorial: Desenvolver um módulo do IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Implantar a Análise Dinâmica de Vídeo no Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Pré-requisitos

Veja a seguir os pré-requisitos para conectar o módulo de análise espacial ao módulo Análise Dinâmica de Vídeo.

* O [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento. Verifique se você tem a [Extensão do Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Verifique se a rede à qual seu computador de desenvolvimento está conectado permite o Advanced Message Queueing Protocol pela porta 5671. Essa configuração permite que o Azure IoT Tools se comunique com o Hub IoT do Azure.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) com aceleração de GPU.  
    É recomendável que você use o Azure Stack Edge com aceleração de GPU, no entanto, o contêiner é executado em qualquer outro dispositivo com uma [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [O contêiner de Pesquisa Visual Computacional do Serviço Cognitivo do Azure](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise espacial.  
    Para usar esse contêiner, você deve ter um recurso de Pesquisa Visual Computacional para obter a **chave de API** associada e um **URI de ponto de extremidade**. A chave de API está disponível nas páginas Chaves e Visão Geral da Pesquisa Visual Computacional do portal do Azure. A chave e o ponto de extremidade são necessários para iniciar o contêiner.

## <a name="overview"></a>Visão geral

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Visão geral da Análise Espacial":::
 
O diagrama mostra como os sinais fluem neste tutorial. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de filtro de taxa de quadros](media-graph-concept.md#frame-rate-filter-processor). Esse processador limita a taxa de quadros do fluxo de vídeo que chega ao nó do processador MediaGraphCognitiveServicesVisionExtension.

O nó MediaGraphCognitiveServicesVisionExtension desempenha a função de um proxy. Ele converte os quadros de vídeo no tipo de imagem especificado. Em seguida, ele retransmite a imagem por **memória compartilhada** para outro módulo de borda que executa operações de IA atrás de um ponto de extremidade gRPC. Neste exemplo, esse módulo de borda é o módulo de análise espacial. O nó do processador MediaGraphCognitiveServicesVisionExtension executa duas ações:

* Coleta os resultados e publica eventos no nó do [coletor do Hub IoT](media-graph-concept.md#iot-hub-message-sink). Em seguida, o nó envia esses eventos para o [Hub do IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub). 
* Ele também captura um clipe de vídeo de 30 segundos da fonte RTSP usando um [processador de porta de sinal](media-graph-concept.md#signal-gate-processor) e o armazena como um ativo dos Serviços de Mídia.

## <a name="create-the-computer-vision-resource"></a>Criar o recurso da Pesquisa Visual Computacional

Você precisa criar um recurso do Azure do tipo Pesquisa Visual Computacional no [portal do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) ou via CLI do Azure. Você poderá criar o recurso depois que sua solicitação de acesso ao contêiner tiver sido aprovada e sua ID de assinatura do Azure tiver sido registrada. Acesse https://aka.ms/csgate para enviar seu caso de uso e sua ID de Assinatura do Azure.  Você precisa criar o recurso do Azure usando a mesma assinatura do Azure fornecida no formulário solicitação de acesso.

### <a name="gathering-required-parameters"></a>Como reunir os parâmetros necessários

Há três parâmetros principais para todos os contêineres de Serviços Cognitivos que são necessários, incluindo o contêiner de análise espacial. O EULA (contrato de licença de usuário final) deve estar presente com um valor de aceito. Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

### <a name="endpoint-uri-endpoint_uri"></a>URI do ponto de extremidade {ENDPOINT_URI}

O valor do URI do ponto de extremidade está disponível na página Visão Geral portal do Azure do recurso Serviços Cognitivos. Navegue até a página Visão Geral e localize o URI do ponto de extremidade. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Chaves e Ponto de Extremidade":::

### <a name="keys-api_key"></a>Chaves {API_KEY}

Essa chave é usada para iniciar o contêiner de análise espacial e está disponível na página Chaves do portal do Azure do recurso de Serviço Cognitivo correspondente. Navegue até a página Chaves e localize as chaves.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="URI do ponto de extremidade":::

## <a name="set-up-azure-stack-edge"></a>Configurar o Azure Stack Edge

Siga [estas etapas](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) configurar o Azure Stack Edge e continue seguindo as etapas abaixo para implantar a Análise Dinâmica de Vídeo e os módulos de análise espacial.

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

1. Clone o repositório deste local: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. No Visual Studio Code, abra a pasta na qual o repositório foi baixado.
1. No Visual Studio Code, navegue até a pasta src/cloud-to-device-console-app. Lá, crie um arquivo e dê a ele o nome appsettings.json. Esse arquivo vai conter as configurações necessárias para executar o programa.
1. Obtenha o IotHubConnectionString do Azure Stack Edge seguindo estas etapas:

    * Vá para o Hub IoT no portal do Azure e clique em `Shared access policies` no painel de navegação à esquerda.
    * Clique em `iothubowner` obter as chaves de acesso compartilhado.
    * Copie o  `Connection String – primary key` e cole-o na caixa de entrada no VSCode.
    
        A cadeia de conexão terá a seguinte aparência: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Copie o conteúdo abaixo no arquivo. Substitua as variáveis.
    
    ```json
    {
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Vá até a pasta src/edge e crie um arquivo chamado .env.
1. Copie o conteúdo do arquivo /clouddrive/lva-sample/edge-deployment/.env. O texto deve ser semelhante ao código a seguir.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Configure modelo de implantação  

Adicione o módulo de análise espacial a /src/edge/deployment.template.json. No modelo, há um módulo lvaEdge, um módulo rtspsim e nosso módulo de análise espacial.

<p>
<details>
<summary>Expanda isso e exiba nosso modelo de implantação de exemplo.  
Copie o conteúdo daqui e cole-o em /src/edge/deployment.template.json.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
                        "Type": "volume"
                      }
                    ],
                    "PortBindings": {
                      "554/tcp": [
                        {
                          "HostPort": "554"
                        }
                      ]
                    }
                  }
                }
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

É preciso estar atento a alguns pontos:

1. Defina a associação de porta.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. IpcMode em lvaEdge e createOptions do módulo de análise espacial devem ser iguais e definidos como host.
1. O arquivo de modelo de implantação deve conter "deployment" no nome de arquivo, caso contrário, não poderá ser reconhecido e gerar o manifesto para implantação.
1. Para que o simulador RTSP funcione, verifique se você configurou os limites de volume. Para obter mais informações, confira [Configurar a montagem de volume do Docker](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Conecte-se ao compartilhamento SMB](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share) e copie o [arquivo de vídeo](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) para o compartilhamento local.
    1. Veja que o módulo rtspsim tem o seguinte:
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implantar o manifesto de implantação

O manifesto de implantação define quais módulos são implantados em um dispositivo de borda. Ele também define as configurações desses módulos.

Siga estas etapas para gerar o manifesto no arquivo de modelo e implantá-lo no dispositivo de borda.

1. Abra o Visual Studio Code.
1. Ao lado do painel HUB IOT DO AZURE, selecione o ícone Mais ações para definir a cadeia de conexão do Hub IoT. Você pode copiar a cadeia de caracteres do arquivo src/cloud-to-device-console-app/appsettings.json.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Análise Espacial: cadeia de conexão":::
1. Clique com o botão direito do mouse em src/edge/deployment.template.json e selecione Gerar Manifesto de Implantação do IoT Edge.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Análise Espacial: implantação amd64 json":::
    
    Esta ação deve criar um arquivo de manifesto chamado deployment.amd64.json na pasta src/edge/config.
1. Clique com o botão direito do mouse em src/edge/config/deployment.amd64.json, selecione Criar Implantação para Dispositivo Único e selecione o nome do dispositivo de borda.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Análise Espacial: json do modelo de implantação":::   
1. Quando for solicitado a selecionar um dispositivo do Hub IoT, escolha o nome do Azure Stack Edge no menu suspenso.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, atualize o Hub IoT do Azure. Agora, o dispositivo de borda mostra os seguintes módulos implantados:
    
    * Análise Dinâmica de Vídeo no IoT Edge (nome do módulo lvaEdge).
    * Simulador RTSP (Real-Time Streaming Protocol) (nome do módulo rtspsim).
    * Análise Espacial (nome do módulo spatialAnalysis).
    
Se você implantar com êxito, haverá uma mensagem em OUTPUT como esta:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Em seguida, você pode encontrar os módulos `lvaEdge`, `rtspsim`, `spatialAnalysis` e `rtspsim` em Dispositivos/Módulos e seu status deve ser "em execução".

## <a name="prepare-to-monitor-events"></a>Preparar-se para monitorar eventos

Para ver esses eventos, siga estas etapas:

1. Abra o painel do Explorer no Visual Studio Code e procure pelo Hub IoT do Azure no canto inferior esquerdo.
1. Expanda o nó Dispositivos.
1. Clique com o botão direito do mouse em seu Azure Stack Edge e selecione Iniciar Monitoramento do Ponto de Extremidade do Evento Interno.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Análise Espacial: iniciar monitoramento":::
     
## <a name="run-the-program"></a>Execute o programa

Há um program.cs que invocará os métodos diretos em src/cloud-to-device-console-app/operations.json. Precisamos configurar o operations.json e fornecer uma topologia para uso de grafo de mídia.
Em operations.json:

Defina a topologia como esta (topologyFile para a topologia local, topologyUrl para a topologia online):

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

Crie uma instância de grafo como esta, defina os parâmetros na topologia aqui:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

<p>
<details>
<summary>Expanda para ver nosso arquivo de topologia de exemplo para o módulo spatialAnalysis:
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
        {
          "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "image": {
            "scale": {
              "mode": "pad",
              "width": "1408",
              "height": "786"
            },
            "format": {
              "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
              "pixelFormat": "bgr24"
            }
          },
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


Use MediaGraphRealTimeComputerVisionExtension para se conectar com o módulo de análise espacial. Defina o ${grpcUrl} in tcp://spatialAnalysis:<PORT_NUMBER>, ou seja, tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

Execute uma sessão de depuração e siga as instruções do TERMINAL, que definirá a topologia, definirá a instância do grafo, ativará a instância do grafo e, por fim, excluirá os recursos.

## <a name="interpret-results"></a>Interpretar os resultados

Quando um grafo de mídia é instanciado, você deve ver o evento "MediaSessionEstablished", aqui, um [evento MediaSessionEstablished de exemplo](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

O módulo de análise espacial também enviará eventos de insight de IA para a Análise Dinâmica de Vídeo e então para o IoTHub, também será exibido em OUTPUT. ENTITY são objetos de detecção e EVENT são eventos spaceanalytics. Essa saída será passada para a Análise Dinâmica de Vídeo.

Saída de exemplo para personZoneEvent (da operação cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Tente operações diferentes que o módulo `spatialAnalysis` oferece, como **personCount** e **personDistance**, alternando o sinalizador "enabled" no nó do grafo do seu arquivo de manifesto de implantação.
>[!Tip]
> Use um [arquivo de vídeo](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) que tenha mais de uma pessoa no quadro.

> [!NOTE]
> Você pode executar apenas uma operação por vez. Portanto, verifique se apenas um sinalizador está definido como **true** e se os outros estão definidos como **false**.
