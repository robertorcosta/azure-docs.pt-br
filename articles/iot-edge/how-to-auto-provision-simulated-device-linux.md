---
title: Dispositivo de provisão com um TPM virtual no Linux VM - Azure IoT Edge
description: Use um TPM simulado em uma VM Linux para testar o Serviço de Provisionamento de Dispositivos do Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511052"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Criar e provisionar um dispositivo IoT Edge com um TPM virtual em uma máquina virtual Linux

Os dispositivos Azure IoT Edge podem ser provisionados automaticamente usando o [Serviço de Provisionamento de Dispositivos](../iot-dps/index.yml). Se você não estiver familiarizado com o processo de provisionamento automático, revise os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas:

* Crie uma máquina virtual Linux (VM) no Hyper-V com Trusted Platform Module (TPM) para segurança de hardware.
* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um registro individual para o dispositivo
* Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT

> [!TIP]
> Este artigo descreve como testar o provisionamento de DPS usando um simulador TPM, mas grande parte dele se aplica a hardware tpm físico, como o [Infineon&trade; OPTIGA TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), um dispositivo Azure Certified for IoT.
>
> Se você estiver usando um dispositivo físico, você pode pular adiante para o [Fornecimento de informações de recuperação de uma](#retrieve-provisioning-information-from-a-physical-device) seção de dispositivo físico neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento do Windows com [Hyper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Este artigo usa o Windows 10 em execução de uma VM do Ubuntu Server.
* Um Hub IoT ativo.
* Se estiver usando um TPM simulado, [o Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou posterior com a carga de trabalho ['Desenvolvimento de desktop com C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ativado.

> [!NOTE]
> O TPM 2.0 é necessário ao usar o atestado TPM com DPS e só pode ser usado para criar matrículas individuais, não em grupo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crie máquinas virtuais Linux com um TPM virtual

Nesta seção, você cria uma nova máquina virtual Linux no Hyper-V. Você configurou esta máquina virtual com um TPM simulado para que você possa usá-lo para testar como o provisionamento automático funciona com o IoT Edge.

### <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Um comutador virtual permite que sua máquina virtual se conecte a uma rede física.

1. Abra o Hyper-V Manager na sua máquina Windows.

2. No menu **Ações**, selecione **Gerenciador de Comutador Virtual**.

3. Escolha um comutador virtual **Externo** e, em seguida, selecione **Criar Comutador Virtual**.

4. Nomeie seu novo comutador virtual, por exemplo **EdgeSwitch**. Certifique-se de que o tipo de conexão é definido como **rede externa**, em seguida, selecione **Ok**.

5. Um pop-up avisa que a conectividade de rede pode ser interrompida. Selecione **Sim** para continuar.

Se você vir erros ao criar o novo comutador virtual, verifique se nenhum outro comutador está usando o adaptador Ethernet e se nenhum outro comutador usa o mesmo nome.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. Baixe um arquivo de imagem de disco para usar para sua máquina virtual e salve-o localmente. Por exemplo: [servidor Ubuntu](https://www.ubuntu.com/download/server).

2. No Hyper-V Manager novamente, selecione **Nova** > **máquina virtual** no menu **Ações.**

3. Conclua o **Assistente de nova máquina Virtual** com as seguintes configurações específicas:

   1. **Especificar geração**: selecione **geração 2**. As máquinas virtuais da Geração 2 têm a virtualização aninhada ativada, que é necessária para executar o IoT Edge em uma máquina virtual.
   2. **Configurar rede**: defina o valor de **Conexão** para o comutador virtual que você criou na seção anterior.
   3. **Opções de instalação**: Selecione **Instalar um sistema operacional a partir de um arquivo de imagem inicializável** e navegue até o arquivo de imagem de disco que você salvou localmente.

4. Selecione **Concluir** no assistente para criar a máquina virtual.

A criação da nova VM pode levar alguns minutos.

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Uma vez que sua VM seja criada, abra suas configurações para habilitar o módulo de plataforma virtual confiável (TPM) que permite que você provisione automaticamente o dispositivo.

1. Selecione a máquina virtual e abra suas **Configurações**.

2. Navegar para **Segurança**.

3. Desmarque a opção **habilitar inicialização segura**.

4. Verificar **Habilitar Trusted Platform Module**.

5. Clique em **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar a máquina virtual e coletar dados TPM

Na máquina virtual, construa uma ferramenta que você pode usar para recuperar o **ID** de registro do dispositivo e **a chave de endosso**.

1. Inicie sua máquina virtual e conecte-se a ela.

1. Siga os prompts dentro da máquina virtual para terminar o processo de instalação e reiniciar a máquina.

1. Faça login na sua VM e siga as etapas em [Configurar um ambiente de desenvolvimento Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e construir o Dispositivo IoT Azure SDK para C.

   >[!TIP]
   >No decorrer deste artigo, você copiará e colará da máquina virtual, o que não é fácil através do aplicativo de conexão Hyper-V Manager. Você pode querer se conectar à máquina virtual através do Hyper-V Manager uma vez para recuperar seu endereço IP: `ifconfig`. Em seguida, você pode usar o endereço `ssh <username>@<ipaddress>`IP para se conectar através do SSH: .

1. Execute os seguintes comandos para construir a ferramenta SDK que recupera as informações de provisionamento do dispositivo do simulador TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A partir de uma `azure-iot-sdk-c` janela de comando, navegue até o diretório e execute o simulador TPM. Ele escuta em um soquete nas portas 2321 e 2322. Não feche esta janela de comando; você precisará manter este simulador funcionando.

   A `azure-iot-sdk-c` partir do diretório, execute o seguinte comando para iniciar o simulador:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Usando o Visual Studio, abra `cmake` a `azure_iot_sdks.sln`solução gerada no diretório nomeado e construa-a usando o comando **Build solution** no menu **Build.**

1. No painel **Gerenciador de Soluções** no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**.

1. Execute a solução usando qualquer um dos comandos **Iniciar** no menu **Debug.** A janela de saída exibe o **ID** de registro do simulador TPM e a **tecla Endosso**, que você deve copiar para uso mais tarde quando criar uma inscrição individual para o seu dispositivo em Você pode fechar esta janela (com iD de registro e tecla de endosso), mas deixar a janela do simulador TPM em execução.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperar informações de provisionamento de um dispositivo físico

Em seu dispositivo, construa uma ferramenta que você pode usar para recuperar as informações de provisionamento do dispositivo.

1. Siga as etapas em [Configurar um ambiente de desenvolvimento Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar e construir o Dispositivo Azure IoT SDK para C.

1. Execute os seguintes comandos para criar a ferramenta SDK que recupera as informações de provisionamento do dispositivo do dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copie os valores para **ID de Registro** e **tecla de endosso**. Você pode usar esses valores para criar um registro individual para seu dispositivo no DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Recuperar as informações de provisionamento da sua máquina virtual e usá-la para criar um registro individual no serviço de provisionamento do dispositivo.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

1. No [portal Azure,](https://portal.azure.com)navegue até a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

2. Em **Configurações**, selecione **Gerenciar registros**.

3. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   1. Em **Mecanismo**, selecione **TPM**.

   2. Forneça a **chave de endosso** e o **iD de registro** que você copiou da sua máquina virtual.

      > [!TIP]
      > Se você estiver usando um dispositivo TPM físico, você precisa determinar a **tecla Endorsement**, que é exclusiva de cada chip TPM e é obtida do fabricante de chips TPM associado a ele. Você pode obter um **ID de registro** exclusivo para o seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.

   3. Selecione **True** para declarar que esta máquina virtual é um dispositivo IoT Edge.

   4. Escolha o **IoT Hub** vinculado que você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   5. Forneça uma ID para seu dispositivo, se desejar. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. Se você não fornecer um ID do dispositivo, o iD de registro é usado.

   6. Adicionar um valor de marca para o **estado inicial do dispositivo gêmeo** se desejar. Você pode usar marcas para grupos de dispositivos de destino para a implantação do módulo. Por exemplo: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Selecione **Salvar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda. Instale o runtime do IoT Edge em sua máquina virtual.

Saiba seu DPS **Escopo da ID** e do dispositivo **ID de registro** antes de começar o artigo que combine com o seu tipo de dispositivo. Se você instalou o servidor do Ubuntu de exemplo, use as instruções **x64**. Certifique-se de configurar o runtime do IoT Edge para provisionamento automático, não manual.

[Instale o tempo de execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acesso de IoT Edge no TPM

Em ordem para o runtime do IoT Edge provisionar automaticamente o seu dispositivo, ele precisa acessar o TPM.

Você pode conceder acesso TPM no runtime do IoT Edge, substituindo as configurações de systemd, de modo que o serviço **iotedge** tenha privilégios de raiz. Se você não deseja elevar os privilégios de serviço, você também pode usar as etapas a seguir para fornecer manualmente o acesso TPM.

1. Localizar o caminho do arquivo para o módulo de hardware do TPM no seu dispositivo e salve-o como uma variável local.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Crie uma nova regra que dará ao runtime do IoT Edge acesso ao tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra o arquivo de regras.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie as seguintes informações de acesso para o arquivo de regras.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Salve e feche o arquivo.

6. Dispare o sistema de udev para avaliar a nova regra.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verifique se a regra foi aplicada com êxito.

   ```bash
   ls -l /dev/tpm0
   ```

   A saída bem sucedida se parece com o seguinte:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se você não vir que as permissões corretas foram aplicadas, tente reinicializar o computador para atualizar udev.

## <a name="restart-the-iot-edge-runtime"></a>Reinicie o runtime do IoT Edge

Reinicie o runtime do IoT Edge para que ele pega todas as alterações de configuração feitas no dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Verifique se o runtime do IoT Edge está sendo executado.

   ```bash
   sudo systemctl status iotedge
   ```

Se você vir erros de provisionamento, pode ser que as alterações de configuração ainda não entraram em vigor. Tente reiniciar o daemon do IoT Edge novamente.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou então, tente reiniciar sua máquina virtual para ver se as alterações entram em vigor em um novo início.

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e veja que o novo dispositivo foi provisionado automaticamente. Agora seu dispositivo está pronto para executar os módulos do IoT Edge.

Verifique o status do Daemon do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs do daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Módulos de execução da lista.

```cmd/sh
iotedge list
```

Você pode verificar se a inscrição individual que você criou no Serviço de Provisionamento de Dispositivos foi usada. Navegue até a instância do Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes de inscrição para a matrícula individual que você criou. Observe que o status da inscrição é **atribuído** e o ID do dispositivo está listado.

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Aprenda a [implantar e monitorar módulos IoT Edge em escala usando o portal Azure](how-to-deploy-monitor.md) ou usando o [Azure CLI](how-to-deploy-monitor-cli.md).
