---
title: Retornar ou substituir seu dispositivo do Azure Stack Edge | Microsoft Docs
description: Descreve como devolver ou substituir o dispositivo do Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743630"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Devolver ou substituir seu dispositivo do Azure Stack Edge

Este artigo descreve como apagar os dados e, em seguida, devolver o dispositivo do Azure Stack Edge. Depois de devolver o dispositivo, você também poderá excluir o recurso associado ao dispositivo ou solicitar um dispositivo de substituição.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Apagar os dados dos discos de dados no dispositivo
> * Abrir um tíquete de suporte para devolver seu dispositivo
> * Empacotar o dispositivo e agendar uma retirada
> * Excluir o recurso no portal do Azure
> * Obter um dispositivo de substituição

## <a name="erase-data-from-the-device"></a>Apagar os dados do dispositivo

Para apagar os dados dos discos de dados do seu dispositivo, você precisa redefinir o dispositivo. Você pode redefinir o seu dispositivo usando a IU da Web local ou a interface do PowerShell.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner do Armazenamento do Microsoft Azure.

Para redefinir o dispositivo usando a IU da Web local, execute as etapas a seguir.

1. Na IU da Web local, acesse **Manutenção > Redefinição do dispositivo**.
2. Selecione **Redefinir dispositivo**.

    ![Redefinir dispositivo](media/azure-stack-edge-return-device/device-reset-1.png)

3. Quando a confirmação for solicitada, examine o aviso e selecione **Sim** para continuar.

    ![Confirmar redefinição](media/azure-stack-edge-return-device/device-reset-2.png)  

A redefinição apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados em seu dispositivo, esse processo leva cerca de 30 a 40 minutos.

Como alternativa, conecte-se à interface do PowerShell do dispositivo e use o cmdlet `Reset-HcsAppliance` para apagar os dados dos discos de dados. Para obter mais informações, consulte [Redefinir seu dispositivo](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se estiver trocando ou atualizando para um novo dispositivo, recomendamos definir seu dispositivo somente depois de receber o novo dispositivo.
> - A redefinição do dispositivo apenas exclui todos os dados locais do dispositivo. Os dados que estão na nuvem não são excluídos e recebe [cobranças](https://azure.microsoft.com/pricing/details/storage/). Esses dados precisam ser excluídos separadamente com uma ferramenta de gerenciamento de armazenamento em nuvem, como o [Gerenciador do Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Abrir um tíquete de suporte

Para iniciar o processo de devolução, execute as etapas a seguir.

1. Abra um tíquete de suporte com Suporte da Microsoft indicando que você deseja devolver o dispositivo. Selecione o tipo de problema como **Hardware Azure Stack Edge**.

    ![Abrir tíquete de suporte](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Um engenheiro do Suporte da Microsoft entrará em contato com você. Informe os detalhes de envio.
3. Peça uma caixa de envio para devolução se precisar de uma. Responda **Sim** à pergunta **Preciso de uma caixa vazia para devolução**.


## <a name="schedule-a-pickup"></a>Agendar uma retirada

1. Desligar o dispositivo. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **Desligar**. Quando precisar confirmar, clique em **Sim** para continuar. Para obter mais informações, confira [Gerenciar energia](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desconecte os cabos de alimentação e remova todos os cabos de rede do dispositivo.
4. Use sua própria caixa ou uma vazia recebida da Azure para embalar o equipamento para envio. Coloque o dispositivo e os cabos de alimentação que foram enviados com o dispositivo na caixa.
5. Cole a etiqueta de remessa recebida da Azure no pacote.
6. Agende uma retirada com sua transportadora regional. Se devolver o dispositivo nos EUA, sua transportadora será a UPS ou a FedEx. Agendar uma retirada com a UPS:

    1. Ligue para a UPS local (linha gratuita específica do país/região).
    2. Em sua chamada, mencione o número de rastreamento de envio reverso, conforme mostrado na etiqueta impressa.
    3. Se o número de rastreamento não for mencionado, a UPS exigirá que você pague uma taxa adicional durante a retirada.

    Em vez de agendar a retirada, você também pode entregar o Azure Stack Edge no local mais próximo de redistribuição.

## <a name="delete-the-resource"></a>Excluir o recursos

Depois que o dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado quanto a danos ou sinais de violação.

- Se o dispositivo chegar intacto e em bom estado, interromperemos o medidor de cobrança desse recurso. O Suporte da Microsoft entrará em contato com você para confirmar que o dispositivo foi devolvido. Em seguida, você pode excluir o recurso associado ao dispositivo no portal do Azure.
- Se o dispositivo chegar significativamente danificado, multas poderão ser aplicadas. Para mais informações, consulte [Perguntas frequentes sobre dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e os [Termos de serviço do produto](https://www.microsoft.com/licensing/product-licensing/products).  


Você pode excluir o dispositivo no portal do Azure:
-    Depois de ter feito o pedido e antes de o dispositivo ser preparado pela Microsoft.
-    Depois de devolver o dispositivo à Microsoft, ele passa por uma inspeção física no datacenter do Azure e Suporte da Microsoft liga para confirmar que o dispositivo foi devolvido.

Se você ativou o dispositivo em outra assinatura ou local, a Microsoft moverá seu pedido para a nova assinatura ou local em até um dia útil. Depois que o pedido for movido, você poderá excluir esse recurso.


Siga as etapas abaixo para excluir o dispositivo e o recurso em portal do Azure.

1. Na portal do Azure, vá para o recurso e, em seguida, para **Visão geral**. Na barra de comandos, selecione **Excluir**.

    ![Selecione excluir](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Na folha **Excluir dispositivo**, digite o nome do dispositivo que quer excluir e selecione **Excluir**.

    ![Confirmar exclusão](media/azure-stack-edge-return-device/delete-resource-2.png)

Você será notificado após o dispositivo e o recurso associado for excluído com êxito.

## <a name="get-a-replacement-device"></a>Obter um dispositivo de substituição

Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. Siga as etapas abaixo quando o dispositivo tiver um problema de hardware:

1. [Abra um tíquete de suporte para o problema de hardware](#open-a-support-ticket). O Suporte da Microsoft determinará se uma FRU (unidade de substituição em campo) não está disponível para esta instância ou se o dispositivo precisa de uma atualização de hardware. Em ambos os casos, o suporte solicitará um dispositivo de substituição.
2. [Crie um recurso](azure-stack-edge-deploy-prep.md#create-a-new-resource) para o dispositivo de substituição. Marque a caixa de seleção **Eu tenho um dispositivo Azure Stack Edge**. 
3. Depois de receber um dispositivo de substituição, [instale](azure-stack-edge-deploy-install.md) e [ative](azure-stack-edge-deploy-connect-setup-activate.md) o dispositivo de substituição no novo recurso.
4. Siga todas as etapas para devolver o dispositivo original:
    1. Abra outro tíquete para devolver o dispositivo original.
    2. [Apague os dados no dispositivo](#erase-data-from-the-device).
    3. [Agende uma retirada](#schedule-a-pickup).
    5. [Exclua o recurso](#delete-the-resource) associado ao dispositivo retornado.



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).
