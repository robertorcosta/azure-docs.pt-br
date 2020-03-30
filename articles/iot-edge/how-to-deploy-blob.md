---
title: Implantar armazenamento blob no módulo para o seu dispositivo - Azure IoT Edge
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133328"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implantar o armazenamento de Blobs do Azure no módulo IoT Edge para seu dispositivo

Existem várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para o Azure Blob Storage em módulos IoT Edge. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Um [hub de IoT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
- Um [Dispositivo do IoT Edge](how-to-register-device.md) com o runtime do IoT Edge instalado.
- [Visual Studio Code](https://code.visualstudio.com/) e as [Ferramentas De IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se implantados a partir do Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Implantar a partir do portal Azure

O portal Azure orienta você através da criação de um manifesto de implantação e empurrando a implantação para um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecionar o dispositivo

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu hub de IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Selecione **Módulos de conjunto**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal Azure tem um assistente que orienta você através da criação de um manifesto de implantação. Possui três etapas organizadas em guias: **Módulos,** **Rotas**e **Revisão + Criar**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na seção Módulos de **borda IoT** da página, clique em **Adicionar** o dropdown e selecione **IoT Edge Module** para exibir a página Adicionar módulo de **borda IoT.**

2. Na guia Configurações do **módulo,** forneça um nome para o módulo e, em seguida, especifique a uri de imagem do contêiner:

   Exemplos:
  
   - **Nome do módulo de borda IoT:**`azureblobstorageoniotedge`
   - **Uri de imagem**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Configurações duplas do módulo](./media/how-to-deploy-blob/addmodule-tab1.png)

   Não selecione **Adicionar** até que você tenha especificado valores nas **configurações**do módulo, **opções de criação de contêiner**e **guias configurações duplas do módulo** conforme descrito neste procedimento.

   > [!IMPORTANT]
   > O Azure IoT Edge é sensível a maiúsculas e minúsculas quando você faz chamadas para módulos, e o SDK de armazenamento também é padrão para minúsculas. Embora o nome do módulo no [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) seja **AzureBlobStorageonIoTEdge**, mudar o nome para minúsculas ajuda a garantir que suas conexões com o Azure Blob Storage no módulo IoT Edge não sejam interrompidas.

3. Abra a guia **'Criar opções' de contêiner.**

   ![Configurações duplas do módulo](./media/how-to-deploy-blob/addmodule-tab3.png)

   Copie e cole o JSON a seguir na caixa, para fornecer informações da conta de armazenamento e uma montagem para o armazenamento em seu dispositivo.
  
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

4. Atualize o JSON que você copiou em **Opções de criação de contêiner** com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes das contas devem ter de 3 a 24 caracteres, com letras minúsculas e números. Não são permitidos espaços.

   - Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

   - Substitua de `<storage mount>` acordo com o sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório existente em seu dispositivo IoT Edge onde o módulo blob armazenará seus dados. A montagem de armazenamento mapeia um local no seu dispositivo que você fornece para um local definido no módulo.

     - Para contêineres Linux, o formato é * \<caminho de armazenamento ou volume>:/blobroot*. Por exemplo
         - montagem [de volume:](https://docs.docker.com/storage/volumes/) **my-volume:/blobroot**
         - usar [montagem de vinculação:](https://docs.docker.com/storage/bind-mounts/) **/srv/containerdata:/blobroot**. Certifique-se de seguir as etapas para [conceder acesso ao diretório ao usuário do contêiner](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para contêineres do Windows, o formato é * \<o caminho de armazenamento ou o volume>:C:/BlobRoot*. Por exemplo
         - use [o suporte de volume:](https://docs.docker.com/storage/volumes/) **my-volume:C:/blobroot**.
         - utilização [montagem de vinculação:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - Em vez de usar sua unidade local, você pode mapear sua localização de rede SMB, para obter mais informações, ver [usando o compartilhamento de SMB como seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor de montagem de armazenamento, que aponta para um local específico no módulo. O suporte de armazenamento deve sempre terminar com **:/blobroot** para contêineres Linux e **:C:/BlobRoot** para contêineres Windows.

5. Na guia **Configurações duplas** do módulo, copie o JSON a seguir e cole-o na caixa.

   ![Configurações duplas do módulo](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configure cada imóvel com um valor adequado, conforme indicado pelos espaços reservados. Se você estiver usando o simulador IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, conforme descrito pelo [dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [pelo dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Para obter informações sobre a configuração do dispositivoToCloudUploadProperties e dispositivoAutoDeletePropriedades após a implantação do módulo, consulte [Editar o módulo gêmeo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre as propriedades desejadas, consulte [Definir ou atualizar as propriedades desejadas](module-composition.md#define-or-update-desired-properties).

6. Selecione **Adicionar**.

7. Selecione **A seguir: Rotas** para continuar até a seção rotas.

#### <a name="specify-routes"></a>Especificar Rotas

Mantenha as rotas padrão e selecione **Next: Review + create** para continuar na seção de revisão.

#### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Há também dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [runtime do IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Revise suas informações de implantação e selecione **Criar**.

### <a name="verify-your-deployment"></a>Verifique sua implantação

Depois de criar a implantação, você retorna à página **IoT Edge** do seu hub IoT.

1. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação** e **Relatado por dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implantar a partir do Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Use as etapas a seguir para criar uma nova solução IoT Edge com um módulo de armazenamento blob e para configurar o manifesto de implantação.

1. Selecione **'Exibir** > **paleta de comando'.**

1. Na paleta de comando, digite e execute o comando **Azure IoT Edge: Nova solução IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local em sua máquina de desenvolvimento para visual Studio Code para criar os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Existente (Inserir a URL de imagem completa)**. |
   | Fornecer um nome de módulo | Digite um nome em minúsculas para o seu módulo, como **azureblobstorageoniotedge**.<br/><br/>É importante usar um nome em minúsculas para o Armazenamento de Blobs do Azure no módulo do IoT Edge. O IoT Edge diferencia maiúsculas de minúsculas ao se referir a chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. |
   | Fornecer a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   O Visual Studio Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo.

1. Abra *deployment.template.json* no workspace da nova solução e encontre a seção **módulos**. Faça as seguintes alterações de configuração:

   1. Exclua o módulo **SimuladTemperatureSensor,** pois não é necessário para essa implantação.

   1. Copie e cole o seguinte `createOptions` código no campo:

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

      ![Atualização de criação de móduloSOpções - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes das contas devem ter de 3 a 24 caracteres, com letras minúsculas e números. Não são permitidos espaços.

1. Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

1. Substitua de `<storage mount>` acordo com o sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A montagem de armazenamento mapeia um local no seu dispositivo que você fornece para um local definido no módulo.  

     - Para contêineres Linux, o formato é * \<caminho de armazenamento ou volume>:/blobroot*. Por exemplo
         - montagem [de volume:](https://docs.docker.com/storage/volumes/) **my-volume:/blobroot**
         - usar [montagem de vinculação:](https://docs.docker.com/storage/bind-mounts/) **/srv/containerdata:/blobroot**. Certifique-se de seguir as etapas para [conceder acesso ao diretório ao usuário do contêiner](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para contêineres do Windows, o formato é * \<o caminho de armazenamento ou o volume>:C:/BlobRoot*. Por exemplo
         - use [o suporte de volume:](https://docs.docker.com/storage/volumes/) **my-volume:C:/blobroot**.
         - utilização [montagem de vinculação:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - Em vez de usar sua unidade local, você pode mapear sua localização de rede SMB, para obter mais [informações, veja usando o compartilhamento de SMB como seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor de montagem de armazenamento, que aponta para um local específico no módulo. O suporte de armazenamento deve sempre terminar com **:/blobroot** para contêineres Linux e **:C:/BlobRoot** para contêineres Windows.

1. Configure [o dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [o dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o seu módulo adicionando o Seguinte JSON ao arquivo *deployment.template.json.* Configure cada propriedade com um valor apropriado e salve o arquivo. Se você estiver usando o simulador IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que você pode encontrar na seção de explicação do [dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![definir as propriedades desejadas para azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre a configuração do dispositivoToCloudUploadProperties e dispositivoAutoDeletePropriedades após a implantação do módulo, consulte [Editar o módulo gêmeo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre opções de criação de contêiner, reinicialização da política e status desejado, consulte [propriedades desejadas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salvar o arquivo *deployment.template.json.*

1. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**.

1. Visual Studio Code pega as informações fornecidas em *deployment.template.json* e as usa para criar um novo arquivo manifesto de implantação. O manifesto de implantação é criado em uma nova pasta **config** no workspace da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implantar várias instâncias de módulo

Se você quiser implantar várias instâncias do Azure Blob Storage no módulo IoT `HostPort` Edge, você precisa fornecer um caminho de armazenamento diferente e alterar o valor ao que o módulo se vincula. Os módulos do armazenamento de blobs sempre expõem a porta 11002 no contêiner, mas é possível declarar a qual porta ele está associado no host.

Editar **opções de criação de contêiner** (no portal Azure) ou no campo **createOptions** (no arquivo `HostPort` *deployment.template.json* no Visual Studio Code) para alterar o valor:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ao conectar-se a módulos de armazenamento de blobs adicionais, altere o ponto de extremidade para indicar a porta do host atualizada.

## <a name="configure-proxy-support"></a>Configurar suporte para proxy

Se sua organização estiver usando um servidor proxy, você precisará configurar o suporte proxy para os módulos de tempo de execução edgeAgent e edgeHub. Este processo envolve duas tarefas:

- Configure os daemons de tempo de execução e o agente IoT Edge no dispositivo.
- Defina a variável de ambiente HTTPS_PROXY para módulos no arquivo JSON manifesto de implantação.

Esse processo é descrito no [Configure um dispositivo IoT Edge para se comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

Além disso, um módulo de armazenamento blob também requer a configuração HTTPS_PROXY no arquivo de implantação manifesto. Você pode editar diretamente o arquivo manifesto de implantação ou usar o portal Azure.

1. Navegue até o seu hub de iot no portal Azure e selecione **Iot Edge** no menu do painel esquerdo.

1. Selecione o dispositivo com o módulo para configurar.

1. Selecione **Módulos de conjunto**.

1. Na seção **Módulos de borda IoT** da página, selecione o módulo de armazenamento blob.

1. Na página **Atualizar módulo de borda ioT,** selecione a guia Variáveis de **ambiente.**

1. Adicione `HTTPS_PROXY` para o **Nome** e sua URL proxy para o **Valor**.

      ![Definir HTTPS_PROXY variável de ambiente](./media/how-to-deploy-blob/https-proxy-config.png)

1. Clique **em Atualizar,** depois **Revisar + Criar**.

1. Observe que o proxy é adicionado ao módulo no manifesto de implantação e selecione **Criar**.

1. Verifique a configuração selecionando o módulo na página de detalhes do dispositivo e na parte inferior da página **Detalhes dos Módulos de Borda ioT** selecione a guia **Variáveis de Ambiente.**

      ![Definir HTTPS_PROXY variável de ambiente](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o Azure Blob Storage no IoT Edge](how-to-store-data-blob.md).

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
