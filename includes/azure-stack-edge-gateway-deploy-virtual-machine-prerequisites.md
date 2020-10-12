---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 3a17e73c66c2296cc36b24e3b0a8abfcab00e46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89419378"
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

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

