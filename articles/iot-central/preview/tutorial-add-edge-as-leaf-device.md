---
title: Adicionar um dispositivo Azure IoT Edge ao Azure IoT Central | Microsoft Docs
description: Como operador, adicione um dispositivo Azure IoT Edge ao Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892632"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Tutorial: Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central (versões prévias dos recursos)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra como adicionar e configurar um *dispositivo Azure IoT Edge* para o aplicativo do Microsoft Azure IoT Central. Neste tutorial, escolhemos uma VM do Linux habilitada para Azure IoT Edge no Azure Marketplace.

Este tutorial é composto de duas partes:

* Primeiro, como operador, você aprenderá a fazer um provisionamento cloud-first de um dispositivo Azure IoT Edge.
* Em seguida, você aprenderá a fazer um provisionamento device-first de um dispositivo Azure IoT Edge.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo Azure IoT Edge
> * Configurar o dispositivo Azure IoT Edge para ajudar no provisionamento usando a chave SAS
> * Exibir painéis e a integridade do módulo no IoT Central
> * Enviar comandos para um módulo em execução no dispositivo Azure IoT Edge
> * Definir as propriedades em um módulo em execução no dispositivo Azure IoT Edge

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Siga este início rápido para [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Habilitar o grupo de registro do Azure IoT Edge
Habilite as chaves SAS para o grupo de registro do Azure IoT Edge na página Administração.

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Provisionamento cloud-first de dispositivos Azure IoT Edge   
Nesta seção, você criará um dispositivo Azure IoT Edge usando o **modelo de sensor de ambiente** e provisionará um dispositivo. Clique em Dispositivos no painel de navegação à esquerda e clique em Modelo de Sensor de Ambiente. 

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Clique em **+ Novo** e insira um nome e uma identificação do dispositivo convenientes. 

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

O dispositivo entrará no modo **Registrado**.

![Modelo de dispositivo – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Implantar uma VM do Linux habilitada para Azure IoT Edge

>Observação: Você pode optar por usar qualquer computador ou dispositivo. sistema operacional: Linux ou Windows

Para este tutorial, escolhemos uma VM do Linux habilitada para IoT do Azure, que pode ser criada no Azure. Você será direcionado para o [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) e clicará no botão **Obter AGORA**. 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Clique em **Continuar**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Você será direcionado para o portal do Azure. Clique no botão **Criar**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selecione Assinatura, preferencialmente, crie um grupo de recursos, selecione Oeste dos EUA 2 para disponibilidade da VM, insira o usuário e a senha. Lembre-se de que o usuário e a senha serão necessários para futuras etapas. Clique em **Examinar + Criar**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Após a validação, clique em **Criar**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

São necessários alguns minutos para criar os recursos. Clique em Ir para **Recurso**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Provisionar uma VM como um dispositivo Azure IoT Edge 

Em Suporte + solução de problemas no painel de navegação à esquerda, clique em Console serial

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Você verá uma tela como a mostrada abaixo

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Pressione Enter e forneça o nome de usuário e a senha, conforme solicitado, e pressione Enter. 

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Para executar um comando como administrador/raiz, execute o comando: **sudo su –**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Verifique a versão do runtime do Azure IoT Edge. A versão atual do GA é 1.0.8

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Instale o editor do VIM ou, se preferir, use o Nano. 

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Edite o arquivo config.yaml do Azure IoT Edge

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Role a página para baixo e comente a parte da cadeia de conexão do arquivo YAML. 

**Antes**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Depois** (pressione ESC e a letra A minúscula para iniciar a edição)

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Remova a marca de comentário da parte da chave simétrica do arquivo YAML. 

**Antes**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Depois**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Acesse o IoT Central e obtenha a ID do escopo, a identificação do dispositivo e a chave simétrica do dispositivo Azure IoT Edge ![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Acesse a caixa do Linux e substitua a ID do escopo e a ID de registro pela identificação do dispositivo e pela chave simétrica

Pressione **ESC** e digite **:wq!** e pressione **Enter** para salvar as alterações

Reinicie o Azure IoT Edge para processar as alterações e pressione **Enter**

![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Digite **iotedge list**. Isso levará alguns minutos, e você verá três módulos implantados

![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Gerenciador de Dispositivos do IoT Central 

No IoT Central, seu dispositivo será movido para o estado provisionado

![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

A guia Módulos mostrará o status do dispositivo e do módulo no IoT Central 

![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


As propriedades da nuvem serão exibidas em um formulário (no modelo de dispositivo criado nas etapas anteriores). Insira valores e clique em **Salvar**. 

![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Bloco do painel

![Conexão de Dispositivo](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Neste tutorial, você aprendeu como:

* Adicionar um novo dispositivo Azure IoT Edge
* Configurar o dispositivo Azure IoT Edge para ajudar no provisionamento usando a chave SAS
* Exibir painéis e a integridade do módulo no IoT Central
* Enviar comandos para um módulo em execução no dispositivo Azure IoT Edge
* Definir as propriedades em um módulo em execução no dispositivo Azure IoT Edge

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a trabalhar com dispositivos Azure IoT Edge e gerenciá-los no IoT Central, esta é uma sugestão para a próxima etapa:

<!-- Next how-tos in the sequence -->

Como configurar um gateway transparente, siga este tutorial

> [!div class="nextstepaction"]
> [Configurar um gateway transparente](../../iot-edge/how-to-create-transparent-gateway.md)
