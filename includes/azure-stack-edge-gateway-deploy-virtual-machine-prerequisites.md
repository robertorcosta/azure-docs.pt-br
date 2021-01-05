---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763414"
---
Antes de implantar VMs em seu dispositivo Azure Stack Edge, você deve configurar seu cliente para se conectar ao dispositivo via Azure Resource Manager sobre Azure PowerShell. Para obter etapas detalhadas, acesse [conectar-se a Azure Resource Manager em seu dispositivo do Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Verifique se as etapas a seguir podem ser usadas para acessar o dispositivo do cliente (você fez essa configuração ao se conectar ao Azure Resource Manager, você está apenas verificando se a configuração foi bem-sucedida.): 

1. Verifique se a comunicação do Azure Resource Manager está funcionando. Tipo:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Chamar APIs de dispositivo local para autenticar. Tipo: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Forneça o nome de usuário- *EdgeARMuser* e a senha para se conectar via Azure Resource Manager.

1. Se você tiver configurado a **computação** para kubernetes, poderá ignorar esta etapa. Continue para garantir que você habilitou uma interface de rede para computação. Na interface do usuário local, vá para configurações de **computação** . Selecione o adaptador de rede que será usado para criar um comutador virtual. As VMs que você criar serão anexadas a um comutador virtual anexado a essa porta e à rede associada. Certifique-se de escolher uma rede que corresponda ao endereço IP que será usado para a VM.  

    ![Habilitar configurações de computação 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Habilite a computação no adaptador de rede. Azure Stack Edge criará e gerenciará um comutador virtual correspondente a esse adaptador de rede. Não insira IPs específicos para kubernetes no momento. Pode levar vários minutos para habilitar a computação.

    > [!NOTE]
    > Se estiver criando VMs GPU, selecione um adaptador de rede conectado à Internet. Isso permite que você instale a extensão de GPU em seu dispositivo.


1. Habilite a função VM do portal do Azure. Esta etapa cria uma assinatura exclusiva para seu dispositivo que é usada para criar VMs por meio das APIs locais do dispositivo. 

    1. Para habilitar a função VM, na portal do Azure, vá para o recurso Azure Stack Edge para seu dispositivo Azure Stack Edge. Vá para a **computação de borda > máquinas virtuais**.

        ![Adicionar imagem de VM 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Selecione **máquinas virtuais** para ir para a página **visão geral** . **Habilite** o gerenciamento de nuvem de máquina virtual.
        ![Adicionar imagem de VM 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)