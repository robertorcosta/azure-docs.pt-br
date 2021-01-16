---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 56fc24966fa60c3a5e91f92b57332ae2f6a525ff
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256362"
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


