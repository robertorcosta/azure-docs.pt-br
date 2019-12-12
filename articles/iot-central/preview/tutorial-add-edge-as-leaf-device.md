---
title: Adicionar um dispositivo Azure IoT Edge ao Azure IoT Central | Microsoft Docs
description: Como operador, adicione um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979063"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutorial: Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra como adicionar e configurar um dispositivo do Azure IoT Edge para o aplicativo do Azure IoT Central. Neste tutorial, escolhemos uma VM do Linux habilitada para o IoT Edge no Azure Marketplace.

Este tutorial é composto de duas partes:

* Primeiro, como operador, você aprenderá a fazer um provisionamento cloud-first de um dispositivo do IoT Edge.
* Em seguida, você aprenderá a fazer um provisionamento "device first" de um dispositivo do IoT Edge.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo do IoT Edge
> * Configure o dispositivo do IoT Edge para ajudar a provisionar usando uma chave de SAS (assinatura de acesso compartilhado)
> * Exibir painéis e a integridade do módulo no IoT Central
> * Enviar comandos para um módulo em execução no dispositivo IoT Edge
> * Definir as propriedades em um módulo em execução no dispositivo IoT Edge

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Siga [este início rápido para criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Habilitar o grupo de registro do Azure IoT Edge
Na página **Administração**, habilite as chaves SAS para o grupo de registro do Azure IoT Edge.

![Captura de tela da página Administração, com a Conexão do dispositivo realçada](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Provisionamento de um dispositivo "cloud first" do Azure IoT Edge  
Nesta seção, você cria um dispositivo IoT Edge usando o modelo de sensor de ambiente e provisiona um dispositivo. Selecione **Dispositivos** > **Modelo de Sensor de Ambiente**. 

![Captura de tela da página Dispositivos, com a opção Modelo de Sensor de Ambiente realçada](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Selecione **+ Novo** e insira um nome e uma identificação do dispositivo de sua escolha. Selecione **Criar**.

![Captura de tela da caixa de diálogo Criar novo dispositivo, com as opções ID do Dispositivo e Criar realçadas](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

O dispositivo entrará no modo **Registrado**.

![Captura de tela da página Modelo do Sensor de Ambiente, com Status do dispositivo realçada](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Implantar uma VM do Linux habilitada para o IoT Edge

> [!NOTE]
> Você pode optar por usar qualquer computador ou dispositivo. O sistema operacional pode ser Linux ou Windows.

Para este tutorial, estamos usando uma VM do Linux habilitada para IoT do Azure, que pode ser criada no Azure. No [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview), selecione **OBTÊ-LO AGORA**. 

![Captura de tela do Azure Marketplace, com OBTÊ-LO AGORA realçada](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Selecione **Continuar**.

![Captura de tela da caixa de diálogo Criar este aplicativo no Azure, com Continuar realçada](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Você é levado para o portal do Azure. Selecione **Criar**.

![Captura de tela do portal do Azure, com Criar realçada](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selecione **Assinatura**, crie um novo grupo de recursos e selecione **(EUA) Oeste dos EUA 2** para disponibilidade da VM. Em seguida, insira as informações de usuário e de senha. Elas serão necessárias para etapas futuras, portanto, lembre-se delas. Selecione **Examinar + criar**.

![Captura de tela da página Criar detalhes da máquina virtual, com várias opções realçadas](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Após a validação, selecione **Criar**.

![Captura de tela da página Criar uma máquina virtual, com as opções Validação aprovada e Criar realçadas](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

São necessários alguns minutos para criar os recursos. Selecione **Ir para o recurso**.

![Captura de tela da página conclusão da implantação, com a opção Ir para o recurso realçada](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Provisionar uma VM como um dispositivo do IoT Edge 

Em **suporte + solução de problemas**, selecione **Console serial**.

![Captura de tela de Suporte + opções de solução de problemas, com a opção Console serial realçada](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Você verá uma tela semelhante à seguinte:

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Pressione Enter, forneça o nome de usuário e a senha conforme solicitado e pressione Enter novamente. 

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Para executar um comando como administrador (usuário "raiz"), digite: **sudo su –**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Verifique a versão do runtime do IoT Edge. No momento da redação deste artigo, a versão atual do GA é 1.0.8.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Instale o editor Vim ou use o nano se preferir. 

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Edite o arquivo config.yaml do IoT Edge.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Role a página para baixo e comente a parte da cadeia de conexão do arquivo YAML. 

**Antes**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Depois** (pressione ESC e a letra A minúscula para iniciar a edição.)

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Remova a marca de comentário da parte da chave simétrica do arquivo YAML. 

**Antes**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Depois**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Vá para o IoT Central. Obtenha a ID do escopo, a ID do dispositivo e a chave simétrica do dispositivo IoT Edge.
![Captura de tela do IoT Central, com várias opções de conexão de dispositivo realçadas](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Vá para o computador Linux e substitua a ID do escopo e a ID de registro pela ID do dispositivo e chave simétrica.

Pressione Esc e digite **:wq!** . Pressione Enter para salvar as alterações.

Reinicie o IoT Edge para processar as alterações e pressione Enter.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Digite **lista de iotedge**. Após alguns minutos, você verá três módulos implantados.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Gerenciador de Dispositivos do IoT Central 

No IoT Central, seu dispositivo é movido para o estado provisionado.

![Captura de tela de opções de dispositivos do IoT Central, com o Status do dispositivo realçado](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

A guia **Módulos** mostra o status do dispositivo e do módulo no IoT Central. 

![Captura de tela da guia Módulos do IoT Central](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Você verá as propriedades da nuvem em um formulário, do modelo de dispositivo que você criou nas etapas anteriores. Insira valores e selecione **Salvar**. 

![Captura de tela do formulário do Meu Dispositivo Edge do Linux](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Aqui está uma exibição apresentada na forma de um bloco de painel.

![Captura de tela de blocos de painel do Meu Dispositivo Edge do Linux](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a trabalhar com dispositivos IoT Edge e gerenciá-los no IoT Central, esta é uma sugestão para a próxima etapa:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Configurar um gateway transparente](../../iot-edge/how-to-create-transparent-gateway.md)
