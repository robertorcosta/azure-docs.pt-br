---
title: 'Início Rápido: Configurar Provisionamento de Dispositivos no Portal do Azure'
description: Guia de Início Rápido do Azure - configurar o serviço de Provisionamento de Dispositivos do Hub IoT do Azure no Portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 504e027095d839efcbfb535c0e1ecc8c6cfbad26
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903446"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Início Rápido: Configurar o Serviço de Provisionamento de Dispositivos do Hub IoT com o portal do Azure

Estas etapas mostram como configurar os recursos de nuvem do Azure no portal para provisionar seus dispositivos. Este artigo inclui etapas para criar um novo IoT Serviço de Provisionamento de Dispositivos no Hub IoT e vincular os dois serviços. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Criar uma nova instância para o Serviço de Provisionamento de Dispositivos do Hub IoT

1. Clique no botão **Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.

2. *Pesquise no Marketplace* o **serviço de Provisionamento de Dispositivos**. Selecione o botão **Serviço de Provisionamento de Dispositivos no Hub IoT** e clique em **Criar**. 

3. Forneça as seguintes informações para a nova instância de serviço de provisionamento de dispositivo e clique em **Criar**.

    * **Nome:** forneça um nome exclusivo para sua nova instância do serviço de provisionamento de dispositivos. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.
    * **Assinatura:** escolha a assinatura que você deseja usar para criar essa instância do serviço de Provisionamento de Dispositivos.
    * **Grupo de recursos:** este campo permite que você crie um novo grupo de recursos ou escolha um existente para conter a nova instância. Escolha o mesmo grupo de recursos que contenha o hub Iot criado acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados em um grupo, você pode gerenciá-los juntos. Por exemplo, a exclusão do grupo de recursos exclui todos os recursos contidos nesse grupo. Para obter mais informações, veja [Gerenciar grupos de recursos do Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Localização:** selecione a localização mais próxima de seus dispositivos.

      ![Inserir as informações básicas sobre sua instância do Serviço Provisionamento de Dispositivos na folha do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Clique no botão de notificação para monitorar a criação da instância do recurso. Depois que o serviço é implantado com êxito, clique em **fixar no painel**e então **Ir para o recurso**.

    ![Monitorar a notificação de implantação](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Vincular o Hub IoT e o Serviço de Provisionamento de Dispositivos

Nesta seção, você adicionará uma configuração para a instância do serviço de provisionamento de dispositivos. Essa configuração define o hub IoT para o qual os dispositivos serão provisionados.

1. Clique no botão **Todos os recursos** no menu à esquerda do portal do Azure. Selecione a instância de serviço de Provisionamento de Dispositivos que você criou na seção anterior.  

2. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Hubs IoT Vinculados**. Clique no botão **+ Adicionar** na parte superior. 

3. Sobre o **Adicionar link para o hub IoT**, forneça as informações a seguir para vincular a nova instância de serviço de provisionamento de dispositivo a um hub IoT. Em seguida, clique em **Salvar**. 

    * **Assinatura:** selecione a assinatura que contém o hub IoT que você deseja vincular com a nova instância do serviço de Provisionamento de Dispositivos.
    * **Hub IoT:** selecione o hub IoT para vincular com a nova instância do serviço de Provisionamento de Dispositivos.
    * **Política de acesso:** selecione **iothubowner** como as credenciais para estabelecer o link com o hub IoT.  

      ![Vincular o nome do hub para vincular à instância do serviço de Provisionamento de Dispositivos na folha do portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora você deve ver o hub selecionado na folha **Hubs IoT Vinculados**. Talvez você precise clicar em **Atualizar** para mostrar **Hubs IoT vinculados**.



## <a name="clean-up-resources"></a>Limpar recursos

Outros Guias de Início Rápido na coleção aproveitam este Guia de Início Rápido. Se você planeja continuar trabalhando com Guias de Início Rápido ou tutoriais subsequentes, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse Guia de Início Rápido no portal do Azure.

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento do Dispositivo. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  
2. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você implantou um Hub IoT e uma instância do Serviço de Provisionamento de Dispositivos, e vinculou os dois recursos. Para aprender a usar essa configuração a fim de provisionar um dispositivo simulado, prossiga para o Guia de Início Rápido de criação de dispositivo simulado.

> [!div class="nextstepaction"]
> [Guia de Início Rápido para criar dispositivo simulado](./quick-create-simulated-device.md)
