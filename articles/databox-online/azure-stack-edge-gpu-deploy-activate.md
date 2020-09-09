---
title: Tutorial para ativar um dispositivo Azure Stack Edge com GPU no portal do Azure | Microsoft Docs
description: O tutorial para implantar o Azure Stack Edge com GPU instrui como ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267931"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Tutorial: Ativar o Azure Stack Edge com GPU

Este tutorial descreve como é possível ativar o dispositivo Azure Stack Edge com uma GPU integrada usando a IU da Web local.

O processo de ativação pode levar cerca de cinco minutos para ser concluído.

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e configurar o dispositivo do Azure Stack Edge com uma GPU, verifique se você fez o seguinte:

* Para o dispositivo físico: 
    
    - Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Você definiu as configurações de rede e de rede de computação, conforme detalhado em [Configurar rede, rede de computação e proxy Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Você carregou seu certificado de dispositivo ou gerou os certificados de dispositivo no dispositivo em questão, caso você tenha alterado o nome do dispositivo ou o domínio DNS por meio da página **Dispositivo**. Se você ainda não realizou essa etapa, verá um erro durante a ativação do dispositivo e a ativação será bloqueada. Para obter mais informações, confira [Configurar certificados](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Você tem a chave de ativação do serviço Azure Stack Edge que você criou para gerenciar o dispositivo Azure Stack Edge. Para obter mais informações, vá para [Preparar para implantar o Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Ativar o dispositivo

1. Na IU da Web local do dispositivo, acesse a página **Introdução**.
2. No bloco **Ativação**, selecione **Ativar**. 

    ![Página "Detalhes de nuvem" da IU da Web local](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. No painel **Ativar**, digite a **Chave de ativação** recebida em [Obter chave de ativação para o Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Selecione **Aplicar**.

    ![Página "Detalhes de nuvem" da IU da Web local](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Primeiro, o dispositivo é ativado. Em seguida, você será solicitado a baixar o arquivo de chave.
    
    ![Página "Detalhes de nuvem" da IU da Web local](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Selecione **Baixar o arquivo de chave** e salve o arquivo *keys.json* em um local seguro fora do dispositivo. **Esse arquivo de chave contém as chaves de recuperação para o disco do sistema operacional e discos de dados em seu dispositivo**. Aqui está o conteúdo do arquivo *keys.json*:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    A seguinte tabela explica as várias chaves mostradas aqui:
    
    |Campo  |Descrição  |
    |---------|---------|
    |`Id`    | Esta é a identificação do dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Essas são as chaves de BitLockers para os discos de dados e são usadas para recuperar os dados locais em seu dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Essa é a chave do BitLocker para o volume do sistema. Essa chave ajuda na recuperação da configuração do sistema e dos dados do sistema para seu dispositivo. |
    |`ServiceEncryptionKey`| Essa chave protege os dados que fluem por meio do serviço do Azure. Essa chave garante que um comprometimento do serviço do Azure não resulte em um comprometimento das informações armazenadas. |

6. Vá para a página **Visão geral**. O estado do dispositivo agora deve ser mostrado como **Ativado**.

    ![Página "Detalhes de nuvem" da IU da Web local](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
A ativação do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

Para saber como transferir dados com o seu dispositivo Azure Stack Edge, confira:

> [!div class="nextstepaction"]
> [Transferir dados com o Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
