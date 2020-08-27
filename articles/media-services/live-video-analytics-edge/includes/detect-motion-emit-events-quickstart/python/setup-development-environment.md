---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690895"
---
1. Clone o repositório deste local: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python.
1. No Visual Studio Code, abra a pasta na qual o repositório foi baixado.
1. No Visual Studio Code, navegue até a pasta *src/cloud-to-device-console-app*. Lá, crie um arquivo e dê a ele o nome *appsettings.json*. Esse arquivo vai conter as configurações necessárias para executar o programa.
1. Copie o conteúdo do arquivo *~/clouddrive/lva-sample/appsettings.json* que você gerou anteriormente neste início rápido.

    O texto deve ser semelhante à saída a seguir.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Vá até a pasta *src/edge* e crie um arquivo chamado *.env*.
1. Copie o conteúdo do arquivo */clouddrive/lva-sample/edge-deployment/.env*. O texto deve ser semelhante ao código a seguir.

    ```
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
    