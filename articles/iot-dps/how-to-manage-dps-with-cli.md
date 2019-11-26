---
title: Gerenciar o serviço de provisionamento de dispositivos no Hub IoT usando a extensão CLI do Azure & IoT
description: Saiba como usar a CLI do Azure e a extensão de IoT para gerenciar o Serviço de Provisionamento de Dispositivos no Hub IoT
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0e276ca32d7cd02f9668b33b3729757404b13b00
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229734"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Como usar a CLI do Azure e a extensão de IoT para gerenciar o Serviço de Provisionamento de Dispositivos no Hub IoT

A [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o IoT Edge. A CLI do Azure está disponível no Windows, Linux e MacOS. A CLI do Azure permite gerenciar recursos do Hub IoT, instâncias de serviço de Provisionamento de Dispositivos e hubs vinculados prontos para uso.

A extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e recursos completos de IoT Edge.

Neste tutorial, você primeiro conclui as etapas para configurar a CLI do Azure e a extensão de IoT. Em seguida, você saberá como executar os comandos da CLI para executar operações básicas do Serviço de Provisionamento de Dispositivos. 

## <a name="installation"></a>Instalação 

### <a name="step-1---install-python"></a>Etapa 1 - Instale o Python

[Python 2.7x ou Python 3.x](https://www.python.org/downloads/) são necessários.

### <a name="step-2---install-the-azure-cli"></a>Etapa 2 – Instale a CLI do Azure

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure no seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.24 ou superior. Use `az –version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. Uma maneira simples de instalar no Windows é baixar e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Etapa 3 - Instale a extensão de IoT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-cli-iot-ext`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-cli-iot-ext` para ver os detalhes sobre a extensão de IoT. Para remover a extensão, você pode usar `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Operações básicas do Serviço de Provisionamento de Dispositivos
O exemplo mostra como fazer logon na conta do Azure, criar um Grupo de Recursos do Azure (um contêiner que contém recursos relacionados para uma solução do Azure), criar um Hub IoT, criar um serviço de Provisionamento de Dispositivos, listar os serviços de Provisionamento de Dispositivos existentes e criar um Hub IoT vinculado com os comandos da CLI. 

Conclua as etapas de instalação descritas anteriormente antes de começar. Se você ainda não tiver uma conta do Azure, você pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje. 


### <a name="1-log-in-to-the-azure-account"></a>1. faça logon na conta do Azure
  
    az login

![logon][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. criar um grupo de recursos IoTHubBlogDemo em lesteus

    az group create -l eastus -n IoTHubBlogDemo

![Criar grupo de recursos][2]


### <a name="3-create-two-device-provisioning-services"></a>3. criar dois serviços de provisionamento de dispositivos

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Criar Serviço de Provisionamento de Dispositivos][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. listar todos os serviços de provisionamento de dispositivos existentes neste grupo de recursos

    az iot dps list --resource-group IoTHubBlogDemo

![Listar Serviços de Provisionamento de Dispositivos][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. criar um blogDemoHub do Hub IoT no grupo de recursos recém-criado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Criar Hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. vincular um hub IoT existente a um serviço de provisionamento de dispositivos

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Vincular o Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
