---
title: Tutorial para filtrar e analisar dados com computação em uma GPU do Azure Stack Edge | Microsoft Docs
description: Saiba como configurar a função de computação em uma GPU do Azure Stack Edge e usá-la para transformar dados antes de enviá-los para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: f4a8786c8d86f43d3433dd51fe7696fd523025a9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293533"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-gpu-device"></a>Tutorial: Configurar a computação no dispositivo de GPU Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como configurar uma função de computação e criar um cluster do Kubernetes no dispositivo Azure Stack Edge. 

Esse procedimento pode levar cerca de 20 a 30 minutos para ser concluído.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Obter pontos de extremidade do Kubernetes

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo Azure Stack Edge, certifique-se de que:

- Você ativou o dispositivo Azure Stack Edge conforme descrito em [Ativar o Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md).
- Lembre-se de seguir as instruções em [Habilitar a rede de computação](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) e:
    - Habilitar um adaptador de rede para computação.
    - Atribuir IPS de nó do Kubernetes e IPs de serviço externos do Kubernetes.

## <a name="configure-compute"></a>Configurar a computação

Para configurar a computação no Azure Stack Edge, você criará um recurso do Hub IoT do meio do portal do Azure.

1. No portal do Azure do recurso do Azure Stack Edge, acesse **Visão geral**. No painel direito, no bloco **Computação**, selecione **Introdução**.

    ![Introdução à computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. No bloco **Configurar computação de borda**, selecione **Configurar computação**.

    ![Configurar a computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Na folha **Configurar computação de borda**, insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |Hub IoT     | Escolha **Novo** ou **Existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Azure Stack Edge.     |
    |Nome     |Insira um nome para o recurso do Hub IoT.         |

    ![Introdução à computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Selecione **Criar**. A criação do recurso do Hub IoT leva vários minutos. Depois que o recurso do Hub IoT for criado, o bloco **Configurar computação** será atualizado para mostrar a configuração de computação. 

    ![Introdução à computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Para confirmar que a função de computação de borda foi configurada, selecione **Exibir computação** no bloco **Configurar computação**.
    
    ![Introdução à computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Se a caixa de diálogo **Configurar Computação** fechar antes que o Hub IoT seja associado ao dispositivo Azure Stack Edge, o Hub IoT será criado, mas não será mostrado na configuração de computação. 
    
Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. Um runtime do IoT Edge também está em execução no dispositivo do IoT Edge. No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge.

Pode levar de 20 a 30 minutos para configurar a computação já que, nos bastidores, as máquinas virtuais e o cluster do Kubernetes estão sendo criados. 

Depois de você configurar com êxito a computação no portal do Azure, um cluster Kubernetes e um usuário padrão associado ao namespace de IoT (um namespace do sistema controlado pelo Azure Stack Edge) passarão a existir. 

## <a name="get-kubernetes-endpoints"></a>Obter pontos de extremidade do Kubernetes

Para configurar um cliente para acessar o cluster do Kubernetes, você precisará do ponto de extremidade do Kubernetes. Siga estas etapas para obter o ponto de extremidade da API do Kubernetes da IU local do dispositivo Azure Stack Edge.

1. Na IU da Web local do dispositivo, acesse a página **Dispositivos**.
2. Nos **Pontos de extremidade do dispositivo**, copie o ponto de extremidade do **Serviço de API do Kubernetes**. Esse ponto de extremidade é uma cadeia de caracteres no seguinte formato: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Página de dispositivo na IU local](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Salve a cadeia de caracteres do ponto de extremidade. Você a usará posteriormente ao configurar um cliente para acesso ao cluster do Kubernetes por meio do kubectl.

4. Enquanto você estiver na IU da Web local, poderá:

    - Acessar a API do Kubernetes, selecionar **configurações avançadas** e baixar um arquivo de configuração avançada para o Kubernetes. 

        ![Página de dispositivo na IU local 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Se você tiver recebido uma chave da Microsoft (usuários selecionados podem obtê-la), poderá usar esse arquivo de configuração.

        ![Página de dispositivo na IU local 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Você também pode acessar o ponto de extremidade do **painel do Kubernetes** e baixar um arquivo de configuração `aseuser`. 
    
        ![Página de dispositivo na IU local 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Você pode usar esse arquivo de configuração para entrar no painel do Kubernetes ou depurar problemas no cluster do Kubernetes. Para obter mais informações, confira [Acessar o painel do Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar a computação
> * Obter pontos de extremidade do Kubernetes


Para saber como administrar seu dispositivo Azure Stack Edge, confira:

> [!div class="nextstepaction"]
> [Usar IU da Web local para administrar um Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md)
