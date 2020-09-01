---
title: Executar um módulo de GPU em Microsoft Azure Stack dispositivo de GPU de borda | Microsoft Docs
description: Descreve como configurar e executar um módulo na GPU em um dispositivo Azure Stack Edge por meio do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 19b6001e7bf5038b4c5e6112266d1e5dfc0a792b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146310"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-device"></a>Configurar e executar um módulo na GPU no dispositivo Azure Stack Edge

O dispositivo do Azure Stack Edge contém uma ou mais GPU (unidade de processamento gráfico). As GPUs são uma opção popular para cálculos de ia, pois oferecem recursos de processamento paralelo e são mais rápidas na renderização de imagens do que nas CPUs (unidades de processamento central). Para obter mais informações sobre a GPU contida no dispositivo Azure Stack Edge, acesse [Azure Stack especificações técnicas do dispositivo do Edge](azure-stack-edge-gpu-technical-specifications-compliance.md).

Este artigo descreve como configurar e executar um módulo na GPU em seu dispositivo Azure Stack Edge. Neste artigo, você usará os **dígitos** do módulo de contêiner disponíveis publicamente escritos para GPUs NVIDIA T4. Esse procedimento pode ser usado para configurar outros módulos publicados pela NVIDIA para essas GPUs.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você tem acesso a um dispositivo de borda com Azure Stack de 1 nó habilitado por GPU. Este dispositivo está ativado com um recurso no Azure.  

## <a name="configure-module-to-use-gpu"></a>Configurar o módulo para usar a GPU

Para configurar um módulo para usar a GPU em seu Azure Stack dispositivo de borda para executar um módulo, siga estas etapas.

1. No portal do Azure, vá para o recurso associado ao seu dispositivo. 

2. Acesse **Computação de borda > Introdução**. No bloco **Configurar computação de borda** , selecione Configurar.

    ![Configurar o módulo para usar a GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. Na folha **Configurar computação de borda** :

    1. Para o **Hub IOT**, escolha **criar novo**.
    2. Forneça um nome para o recurso do Hub IoT que você deseja criar para seu dispositivo. PARA usar uma camada gratuita, selecione um recurso existente. 
    3. Anote o dispositivo de IoT Edge e o dispositivo de gateway IoT que são criados com o recurso do Hub IoT. Você usará essas informações em etapas posteriores.

    ![Configurar o módulo para usar a GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Leva vários minutos para criar o recurso do Hub IoT. Depois que o recurso for criado, no bloco **Configurar a computação de borda** , selecione **Exibir configuração** para exibir os detalhes do recurso do Hub IOT.

    ![Configurar o módulo para usar a GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Vá para **gerenciamento automático de dispositivos > IOT Edge**.

    ![Configurar o módulo para usar a GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    No painel direito, você verá o dispositivo IoT Edge associado ao dispositivo Azure Stack Edge. Isso corresponde ao dispositivo IoT Edge que você criou na etapa anterior ao criar o recurso do Hub IoT. 
    
6. Selecione este dispositivo IoT Edge.

   ![Configurar o módulo para usar a GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Selecione **Definir módulos**.

    ![Configurar o módulo para usar a GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Selecione **+ Adicionar** e, em seguida, selecione **+ IOT Edge módulo**. 

    ![Configurar o módulo para usar a GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. Na guia **Adicionar módulo de IOT Edge** :

    1. Forneça o **URI da imagem**. Você usará os **dígitos** do módulo NVIDIA disponíveis publicamente aqui. 
    
    2. Defina a **política de reinicialização** como **sempre**.
    
    3. Defina o **estado desejado** como **em execução**.
    
    ![Configurar o módulo para usar a GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. Na guia **variáveis de ambiente** , forneça o nome da variável e o valor correspondente. 

    1. Para que o módulo atual use uma GPU nesse dispositivo, use o NVIDIA_VISIBLE_DEVICES. 

    2. Defina o valor como 0 ou 1. Isso garante que pelo menos uma GPU seja usada pelo dispositivo para este módulo. Quando você define o valor como 0, 1, isso significa que ambas as GPUs em seu dispositivo estão sendo usadas por este módulo.

        ![Configurar o módulo para usar a GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Para obter mais informações sobre variáveis de ambiente que você pode usar com a GPU NVIDIA, vá para [tempo de execução de contêiner NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Uma GPU só pode ser mapeada para um módulo. Um módulo pode, no entanto, usar uma, ambas ou nenhuma GPUs. 

11. Digite um nome para o módulo. Neste ponto, você pode optar por fornecer a opção de criação de contêiner e modificar as configurações de módulo de configuração ou, se terminar, selecionar **Adicionar**. 

    ![Configurar o módulo para usar a GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Verifique se o módulo está em execução e selecione **revisar + criar**.    

    ![Configurar o módulo para usar a GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. Na guia **revisar + criar** , as opções de implantação que você selecionou são exibidas. Examine as opções e selecione **criar**.
    
    ![Configurar o módulo para usar a GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Anote o **status do tempo de execução** do módulo. 
    
    ![Configurar o módulo para usar a GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Leva alguns minutos para que o módulo seja implantado. Selecione **Atualizar** e você deverá ver a atualização do **status de tempo de execução** para **em execução**.

    ![Configurar o módulo para usar a GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as variáveis de ambiente que você pode usar com a GPU NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
