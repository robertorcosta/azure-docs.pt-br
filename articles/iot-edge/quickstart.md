---
title: Início Rápido para criar um dispositivo do Azure IoT Edge no Windows | Microsoft Docs
description: Neste início rápido, aprenda a criar um dispositivo IoT Edge e então implantar o código pré-compilado remotamente do portal do Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d56f17f6c60f30a38431ee347c7bdfc5b200b641
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328575"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Início Rápido: Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual do Windows

Experimente o Azure IoT Edge neste início rápido implantando um código em contêineres em um dispositivo virtual do Windows IoT Edge. O IoT Edge permite que você gerencie remotamente o código em seus dispositivos para que você possa enviar mais cargas de trabalho para a borda. Para este início rápido, recomendamos usar uma máquina virtual do Azure para o dispositivo do IoT Edge. O uso de uma máquina virtual permite criar rapidamente um computador de teste, instalar os pré-requisitos e, em seguida, excluí-lo quando você terminar.

Neste guia de início rápido, você aprende a:

> [!div class="checklist"]
>
> * Crie um Hub IoT.
> * Registrar um dispositivo IoT Edge em seu Hub IoT.
> * Instale e inicie o runtime do IoT Edge no dispositivo virtual.
> * Implante remotamente um módulo em um dispositivo IoT Edge e envie a telemetria para IoT Hub.

![Diagrama – Início Rápido da arquitetura para dispositivo e nuvem](./media/quickstart/install-edge-full.png)

Este início rápido explica como criar uma máquina virtual do Windows e configurá-la como um dispositivo IoT Edge. Em seguida, implante um módulo do portal do Azure em seu dispositivo. O módulo usado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos adicionais que analisam os dados simulados para obter informações de negócios.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa a CLI do Azure para concluir muitas das etapas neste guia de início rápido. O Azure IoT tem uma extensão para habilitar funcionalidade adicional.

Adicione a extensão de IoT do Azure à instância do Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recursos de nuvem:

* Um grupo de recursos para gerenciar todos os recursos que você usará neste início rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge:

* Uma máquina virtual do Windows para atuar como o dispositivo IoT Edge. É possível criar essa máquina virtual usando o seguinte comando, substituindo `{password}` por uma senha segura:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  A criação e a inicialização da nova máquina virtual podem levar alguns minutos.

  Depois que a máquina virtual for iniciada, baixe um arquivo RDP para usá-lo quando você se conectar à máquina virtual:

  1. Navegue até a nova máquina virtual do Windows no portal do Azure.
  1. Selecione **Conectar**.
  1. Na guia **RDP**, selecione **Baixar arquivo RDP**.

  Abra esse arquivo com Conexão de Área de Trabalho Remota para conectar-se à máquina virtual do Windows usando o nome do administrador e senha que você especificou com o comando `az vm create`.

> [!NOTE]
> Sua máquina virtual do Windows começa com o Windows versão 1809 (build 17763), que é o último [build de suporte de longo prazo do Windows](https://docs.microsoft.com/windows/release-information/). Por padrão, o Windows verifica automaticamente se há atualizações a cada 22 horas. Após uma verificação na sua máquina virtual, o Windows envia por push uma atualização de versão que é incompatível com o IoT Edge para Windows, o que impede o uso posterior de recursos do IoT Edge para Windows. Recomendamos limitar o uso da máquina virtual a um período que não exceda 22 horas ou [pausar temporariamente as atualizações do Windows](https://support.microsoft.com/help/4028233/windows-10-manage-updates).
>
> Este início rápido usa uma máquina virtual da área de trabalho do Windows para manter a simplicidade. Para saber mais sobre quais sistemas operacionais do Windows estão geralmente disponíveis para cenários de produção, confira [Sistemas com suporte do Azure IoT Edge](support.md).
>
> Se estiver pronto para configurar seu próprio dispositivo Windows para o IoT Edge, incluindo dispositivos que executam o IoT Core, siga as etapas em [Instalar o runtime do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando um Hub IoT com a CLI do Azure.

![Diagrama – Criar um hub IoT na nuvem](./media/quickstart/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este guia de início rápido. Se você tiver usado o Hub IoT antes e já tiver um hub criado, poderá usar esse Hub IoT.

O código a seguir cria um hub **F1** disponível no grupo de recursos `IoTEdgeResources`. Substitua `{hub_name}` por um nome exclusivo para o hub IoT. A criação de um Hub IoT pode demorar alguns minutos.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se você receber um erro porque já exsite um hub gratuito na sua assinatura, altere o SKU para **S1**. Caso você receba um erro que o nome do Hub IoT não está disponível, isso significa que alguém já tem um hub com esse nome. Tente usar um novo nome.

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Diagrama – Registrar um dispositivo com uma identidade do Hub IoT](./media/quickstart/register-device.png)

Crie uma identidade de dispositivo para seu dispositivo simulado para que ele possa se comunicar com o hub IoT. A identidade do dispositivo reside na nuvem e você usa uma cadeia de conexão de dispositivo exclusiva para associar um dispositivo físico a uma identidade do dispositivo.

Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare essa identidade para ser um dispositivo IoT Edge com o sinalizador `--edge-enabled`.

1. No Azure Cloud Shell, digite o comando a seguir para criar um dispositivo denominado **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Se você receber um erro sobre as chaves de política do iothubowner, verifique se o Cloud Shell está executando a última versão da extensão azure-iot.

2. Veja a cadeia de conexão para o seu dispositivo, o que vincula o dispositivo físico à sua identidade no Hub IoT. Ele contém o nome do seu hub IoT, o nome do seu dispositivo e uma chave compartilhada que autentica as conexões entre os dois.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor da chave `connectionString` da saída JSON e salve-o. Esse valor é a cadeia de conexão do dispositivo. Você usará essa cadeia de conexão para configurar o tempo de execução do IoT Edge na próxima seção.

   ![Recuperar a cadeia de conexão da saída da CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o runtime do IoT Edge

Instale o runtime do Azure IoT Edge no dispositivo IoT Edge e configure-o com uma cadeia de conexão do dispositivo.
![Diagrama – Iniciar o runtime no dispositivo](./media/quickstart/start-runtime.png)

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O *daemon de segurança do IoT Edge* é iniciado sempre que um dispositivo IoT Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O *agente do IoT Edge* gerencia a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O *hub do IoT Edge* controla a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

O script de instalação também inclui um mecanismo de contêiner chamado Moby que gerencia as imagens de contêiner no seu dispositivo IoT Edge.

Durante a instalação do runtime, você precisará fornecer uma cadeia de conexão do dispositivo. Use a cadeia de caracteres que você recuperou da CLI do Azure. Essa cadeia de caracteres associa seu dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="connect-to-your-iot-edge-device"></a>Conectar-se ao dispositivo do IoT Edge

Todas as etapas desta seção ocorrem no dispositivo IoT Edge, portanto, conecte-se à máquina virtual via área de trabalho remota.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalar e configurar o serviço do IoT Edge

Use o PowerShell para baixar e instalar o runtime do IoT Edge. Use a cadeia de conexão do dispositivo que você recuperou do Hub IoT para configurar o dispositivo.

1. Caso ainda não o tenha feito, siga as etapas em [Registrar um novo dispositivo Azure IoT Edge](how-to-register-device.md) para registrar seu dispositivo e recuperar a cadeia de caracteres de conexão do dispositivo.

2. Na máquina virtual, execute o PowerShell como administrador.

   >[!NOTE]
   >Use uma sessão do AMD64 do PowerShell para instalar o IoT Edge e não o PowerShell (x86). Se não tiver certeza de qual tipo de sessão você está usando, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se seu computador Windows está usando uma versão compatível, ativa o recurso de contêineres, baixa o tempo de execução do Moby e do IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. O computador poderá ser reiniciado automaticamente. Se você for solicitado pelo comando Deploy-IoTEdge a reinicializar, faça isso agora.

5. Execute novamente o PowerShell como administrador.

6. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Quando solicitado a fornecer uma **DeviceConnectionString**, forneça a cadeia de caracteres que você copiou na seção anterior. Não inclua aspas na cadeia de conexão.

### <a name="view-the-iot-edge-runtime-status"></a>Veja o status do runtime do IoT Edge

Verifique se o runtime foi instalado e configurado com êxito. Pode levar alguns minutos para a instalação ser concluída e o módulo do agente do IoT Edge iniciar.

1. Verifique o status do serviço do IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Exiba todos os módulos em execução no seu dispositivo IoT Edge. Como o serviço acabou de ser iniciado pela primeira vez, você só verá o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e a iniciar quaisquer módulos adicionais que você implante em seu dispositivo.

    ```powershell
    iotedge list
    ```

   ![Exibir um módulo no dispositivo](./media/quickstart/iotedge-list-1.png)

Seu dispositivo IoT Edge agora está configurado. Ele está pronto para executar os módulos implantados na nuvem.

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que envia dados telemétricos ao Hub IoT.

![Diagrama – Implantar o módulo da nuvem para dispositivo](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge nele. Em seguida, você usou o portal do Azure para implantar um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo.

Nesse caso, o módulo enviado por push gera dados de ambiente de exemplo que você pode usar para testar mais tarde. O sensor simulado está monitorando um computador e o ambiente em torno do computador. Por exemplo, esse sensor pode estar em uma sala de servidor, em um chão de fábrica ou em uma turbina eólica. A mensagem inclui a temperatura ambiente e umidade, temperatura do computador, pressão e um carimbo de data/hora. Os tutoriais do IoT Edge usam os dados criados por esse módulo de dados de teste para análise.

Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge.

```powershell
iotedge list
```

   ![Exibir três módulos no seu dispositivo](./media/quickstart/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo sensor de temperatura para a nuvem.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Ao fazer referência a nomes de módulo, comandos do IoT Edge diferenciam maiúsculas de minúsculas.

   ![Exibir os dados do seu módulo](./media/quickstart/iotedge-logs.png)

Você também pode exibir as mensagens que são recebidas pelo seu hub IoT usando a [Extensão do Hub IoT do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você deseja prosseguir para os tutoriais do IoT Edge, pode usar o dispositivo registrado e configurado neste guia de início rápido. Caso contrário, você pode excluir os recursos do Azure que criou para evitar encargos.

Se você tiver criado a sua máquina virtual e o Hub IoT em um novo grupo de recursos, é possível excluir esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que não haja nada que você queira manter. Caso não queira excluir o grupo inteiro, é possível excluir recursos individuais em vez disso.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível.

Remova o grupo **IoTEdgeResources**. A exclusão de um grupo de recursos pode demorar alguns minutos.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Para confirmar se o grupo de recursos foi removido, veja a lista de grupos de recursos.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo de teste que gera dados brutos sobre seu ambiente.

A próxima etapa é configurar seu ambiente de desenvolvimento local para que você possa começar a criar módulos de IoT Edge que executem a sua lógica de negócios.

> [!div class="nextstepaction"]
> [Começar a desenvolver módulos do IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md)
