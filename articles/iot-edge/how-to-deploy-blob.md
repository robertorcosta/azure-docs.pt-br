---
title: Deploy the Azure Blob Storage module to devices - Azure IoT Edge | Microsoft Docs
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: e5241e078cd8d36a9e43b4b55a649c1e24c85345
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456871"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Deploy the Azure Blob Storage on IoT Edge module to your device

There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Blob Storage on IoT Edge modules. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.
- Um [Dispositivo do IoT Edge](how-to-register-device.md) com o runtime do IoT Edge instalado.
- [Visual Studio Code](https://code.visualstudio.com/) and the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) if deploying from Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Deploy from the Azure portal

The Azure portal guides you through creating a deployment manifest and pushing the deployment to an IoT Edge device.

### <a name="select-your-device"></a>Selecionar o dispositivo

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Selecione **Definir Módulos**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. The Azure portal has a wizard that walks you through creating a deployment manifest, instead of building the JSON document manually. Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na seção **Módulos de Implantação** da página, selecione **Adicionar**.

1. From the types of modules in the drop-down list, select **IoT Edge Module**.

1. Provide a name for the module and then specify the container image:

   - **Name** - azureblobstorageoniotedge
   - **Image URI** - mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge is case-sensitive when you make calls to modules, and the Storage SDK also defaults to lowercase. Although the name of the module in the [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) is **AzureBlobStorageonIoTEdge**, changing the name to lowercase helps to ensure that your connections to the Azure Blob Storage on IoT Edge module aren't interrupted.

1. The default **Container Create Options** values define the port bindings that your container needs, but you also need to add your storage account information and a mount for the storage on your device. Replace the default JSON in the portal with the JSON below:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que você possa se lembrar. Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

   - Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

   - Replace `<storage mount>` according to your container operating system. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. The storage mount maps a location on your device that you provide to a set location in the module.

     - For Linux containers, the format is *\<storage path or volume>:/blobroot*. Por exemplo,
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot** 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Make sure to follow the steps to [grant directory access to the container user](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - For Windows containers, the format is *\<storage path or volume>:C:/BlobRoot*. Por exemplo,
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**. 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Instead of using your local drive, you can map your SMB network location, for more information see [using SMB share as your local storage](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Do not change the second half of the storage mount value, which points to a specific location in the module. The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Set [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) properties for your module by copying the following JSON and pasting it into the **Set module twin's desired properties** box. Configure each property with an appropriate value, save it, and continue with the deployment. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section of [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![set container create options, deviceAutoDeleteProperties and deviceToCloudUploadProperties properties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   For information on configuring deviceToCloudUploadProperties and deviceAutoDeleteProperties after your module has been deployed, see [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). For more information about desired properties, see [Define or update desired properties](module-composition.md#define-or-update-desired-properties).

1. Clique em **Salvar**.

1. Selecione **Avançar** para continuar para a seção de rotas.

#### <a name="specify-routes"></a>Especificar Rotas

Keep the default routes, and select **Next** to continue to the review section.

#### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. There are also two modules declared that you didn't add: **$edgeAgent** and **$edgeHub**. Estes dois módulos enganam o [runtime do IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Examine as informações da implantação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verify your deployment

Depois de enviar a implantação, você volta para a página do **IoT Edge** de seu hub IoT.

1. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação** e **Relatado por dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="deploy-from-visual-studio-code"></a>Deploy from Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Use the following steps to create a new IoT Edge solution with a blob storage module and to configure the deployment manifest.

1. Selecione **Exibir** > **Paleta de Comandos**.

1. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: nova solução IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Value |
   | ----- | ----- |
   | Selecionar pasta | Choose the location on your development machine for Visual Studio Code to create the solution files. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Existente (Inserir a URL de imagem completa)** . |
   | Fornecer um nome de módulo | Enter an all-lowercase name for your module, like **azureblobstorageoniotedge**.<br /><br />É importante usar um nome em minúsculas para o Armazenamento de Blobs do Azure no módulo do IoT Edge. O IoT Edge diferencia maiúsculas de minúsculas ao se referir a chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. |
   | Fornecer a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   O Visual Studio Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo.

1. Abra *deployment.template.json* no workspace da nova solução e encontre a seção **módulos**. Faça as seguintes alterações de configuração:

   1. Delete the **SimulatedTemperatureSensor** module, as it's not necessary for this deployment.

   1. Copy and paste the following code into the `createOptions` field:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Update module createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` por um nome de que você possa se lembrar. Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

1. Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

1. Replace `<storage mount>` according to your container operating system. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. The storage mount maps a location on your device that you provide to a set location in the module.  

      
     - For Linux containers, the format is *\<storage path or volume>:/blobroot*. Por exemplo,
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot** 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Make sure to follow the steps to [grant directory access to the container user](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - For Windows containers, the format is *\<storage path or volume>:C:/BlobRoot*. Por exemplo,
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**. 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Instead of using your local drive, you can map your SMB network location, for more information see [using SMB share as your local storage](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Do not change the second half of the storage mount value, which points to a specific location in the module. The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Configure [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) for your module by adding the following JSON to the *deployment.template.json* file. Configure each property with an appropriate value and save the file. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section of [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![set desired properties for azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   For information on configuring deviceToCloudUploadProperties and deviceAutoDeleteProperties after your module has been deployed, see [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). For more information about container create options, restart policy, and desired status, see [EdgeAgent desired properties](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salve o arquivo *deployment.template.json*.

1. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**.

1. Visual Studio Code takes the information that you provided in *deployment.template.json* and uses it to create a new deployment manifest file. O manifesto de implantação é criado em uma nova pasta **config** no workspace da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Deploy multiple module instances

If you want to deploy multiple instances of the Azure Blob Storage on IoT Edge module, you need to provide a different storage path and change the `HostPort` value that the module binds to. Os módulos do armazenamento de blobs sempre expõem a porta 11002 no contêiner, mas é possível declarar a qual porta ele está associado no host.

Edit **Container Create Options** (in the Azure portal) or the **createOptions** field (in the *deployment.template.json* file in Visual Studio Code) to change the `HostPort` value:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ao conectar-se a módulos de armazenamento de blobs adicionais, altere o ponto de extremidade para indicar a porta do host atualizada.

## <a name="next-steps"></a>Próximos passos
Learn more about [Azure Blob Storage on IoT Edge](how-to-store-data-blob.md)

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
