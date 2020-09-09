---
title: Tutorial para configurar certificados para um dispositivo Azure Stack Edge com uma GPU no portal do Azure | Microsoft Docs
description: O tutorial para implantar o Azure Stack Edge com uma GPU instrui como se conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 80a857f80fd2c164637e591fbab43123659cd2f7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268169"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Tutorial: Configurar certificados para o Azure Stack Edge com uma GPU

Este tutorial descreve como é possível configurar certificados para o dispositivo Azure Stack Edge com uma GPU integrada usando a IU da Web local.

O tempo que essa etapa levará varia dependendo da opção específica que você escolher e de como o fluxo de certificado é estabelecido em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e configurar o dispositivo do Azure Stack Edge com uma GPU, verifique se você fez o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Se você planeja trazer seus certificados:
    - Você deve manter seus certificados preparados no formato apropriado, incluindo o certificado de cadeia de assinatura.
<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Configurar certificados para o dispositivo


1. No bloco **Segurança**, selecione **Configurar** para certificados. 

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. Dependendo de você ter ou não alterado o nome do dispositivo ou o domínio DNS no bloco **Configuração do dispositivo**, você poderá escolher uma das opções a seguir para seus certificados.

    - Se você não alterou o nome do dispositivo nem o domínio DNS na etapa anterior e não deseja trazer seus certificados, pode ignorar esta etapa e prosseguir para a próxima. Para começar, o dispositivo gerou automaticamente certificados autoassinados. 

        ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Se você alterar o nome do dispositivo ou o domínio DNS, escolha uma das seguintes opções para ativar o dispositivo com êxito: 
    
        - **Gere todos os certificados de dispositivo**. Os certificados de dispositivo só devem ser usados para teste e não usados com cargas de trabalho de produção.** Para obter mais informações, acesse [Gerar certificados de dispositivo no Azure Stack Edge](#generate-device-certificates).

        - **Traga seus certificados**. Você pode colocar seus certificados de ponto de extremidade assinados e as cadeias de assinatura correspondentes. Primeiro, você adiciona a cadeia de assinatura e carrega os certificados de ponto de extremidade. **É recomendável que você sempre traga seus certificados para cargas de trabalho de produção.** Para obter mais informações, acesse [Traga seus certificados em seu dispositivo Azure Stack Edge](#bring-your-own-certificates).
    
        - Você pode trazer alguns de seus certificados e gerar alguns certificados de dispositivo. A opção **Gerar certificados** vai regenerar os certificados de dispositivo.

    - Se você tiver alterado o nome do dispositivo ou o domínio DNS e não gerar certificados nem trouxer seus certificados, a ativação será bloqueada.


### <a name="generate-device-certificates"></a>Gerar certificados de dispositivo

Siga estas etapas para gerar certificados de dispositivo.

Use estas etapas para regenerar e baixar os certificados de dispositivo Azure Stack Edge:

1. Na IU local do dispositivo, acesse **Configuração > Certificados**. Selecione **Gerar certificados**.

    ![Gerar e baixar o certificado 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Em **Gerar certificados de dispositivo**, selecione **Gerar**.

    ![Gerar e baixar o certificado 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Os certificados de dispositivo agora são gerados e aplicados. 
    
    > [!IMPORTANT]
    > Enquanto a operação de geração de certificado está em andamento, não traga seus certificados nem tente adicioná-los por meio da opção **+ Adicionar certificado**.

    Você será notificado quando a operação for concluída com êxito. Para evitar possíveis problemas de cache, reinicie o navegador. 
    
    ![Gerar e baixar o certificado 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Na página **Certificados**, você verá que a coluna **Download** agora está populada e que links para baixar os certificados regenerados estão disponíveis. 

    ![Gerar e baixar o certificado 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Selecione o link de download para um certificado e, quando solicitado, salve o certificado em questão. 

    ![Gerar e baixar o certificado 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Repita esse processo para todos os certificados que você deseja baixar. 
    
    ![Gerar e baixar o certificado 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Os certificados gerados pelo dispositivo são salvos como certificados DER com o seguinte formato de nome: 

    `<Device name>_<Endpoint name>.cer`. Esses certificados contêm a chave pública para os certificados correspondentes instalados no dispositivo. 

Será necessário instalar esses certificados no sistema cliente que você está usando para acessar os pontos de extremidade no dispositivo ASE. Esses certificados estabelecem a confiança entre o cliente e o dispositivo.

Para importar e instalar esses certificados no cliente que você está usando para acessar o dispositivo, siga as etapas em [Importar certificados nos clientes que acessam o dispositivo Azure Stack Edge](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Se você estiver usando o Gerenciador de Armazenamento do Azure, precisará instalar certificados em seu cliente no formato PEM e converter os certificados gerados pelo dispositivo no formato PEM. 

> [!IMPORTANT]
> - O link de download só estará disponível para os certificados gerados pelo dispositivo, mas o mesmo não acontecerá se você trouxer seus certificados.
> - É possível optar por usar uma combinação, usando certificados gerados pelo dispositivo e trazendo seus certificados, desde que os outros requisitos de certificado sejam atendidos. Para obter mais informações, acesse [Requisitos de certificado](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Trazer seus certificados

Siga estas etapas para adicionar seus certificados, incluindo a cadeia de assinatura.

1. Para carregar o certificado, na página **Certificado**, selecione **+ Adicionar certificado**.

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Carregue a cadeia de assinatura primeiro e selecione **Validar e adicionar**.

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Agora, é possível carregar outros certificados. Por exemplo, é possível carregar os certificados de ponto de extremidade do Armazenamento de Blobs e do Azure Resource Manager.

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Também é possível carregar o certificado da IU da Web local. Depois de carregar esse certificado, será necessário iniciar o navegador e limpar o cache. Em seguida, será necessário conectar-se à IU da Web local do dispositivo.  

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Também é possível carregar o certificado de nó.

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    A página de certificados será atualizada para refletir os certificados recém-adicionados.

    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > Exceto para a nuvem pública do Azure, os certificados de cadeia de assinatura precisam ser trazidos antes da ativação para todas as configurações de nuvem (Azure Government ou Azure Stack).


Agora, seu dispositivo está pronto para ser ativado.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico

Para saber como administrar seu dispositivo Azure Stack Edge, confira:

> [!div class="nextstepaction"]
> [Ativar o dispositivo Azure Stack Edge](./azure-stack-edge-gpu-deploy-activate.md)
