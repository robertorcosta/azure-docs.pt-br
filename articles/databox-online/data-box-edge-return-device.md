---
title: Devolva ou substitua o dispositivo Azure Data Box Edge | Microsoft Docs
description: Descreve como retornar ou substituir o dispositivo Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651093"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Retornar ou substituir o dispositivo Azure Data Box Edge

Este artigo descreve como limpar os dados e, em seguida, retornar o dispositivo Azure Data Box Edge. Depois de retornar o dispositivo, você também pode excluir o recurso associado ao dispositivo ou solicitar um dispositivo de substituição.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Limpe os dados dos discos de dados do dispositivo
> * Abra um bilhete de suporte para devolver seu dispositivo
> * Empacote o dispositivo e agende uma retirada
> * Exclua o recurso no portal Azure
> * Obter um dispositivo de substituição

## <a name="erase-data-from-the-device"></a>Apagar dados do dispositivo

Para limpar os dados dos discos de dados do seu dispositivo, você precisa redefinir seu dispositivo. Você pode redefinir seu dispositivo usando a interface web local ou a interface PowerShell.

Antes de reiniciar, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner de armazenamento Azure.

Para redefinir seu dispositivo usando a ui web local, tome as seguintes etapas.

1. Na web UI local, vá para **Reset de Dispositivo de manutenção > .**
2. Selecione **dispositivo de reset**.

    ![Redefinir dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando solicitado para confirmação, revise o aviso e selecione **Sim** para continuar.

    ![Confirmar reset](media/data-box-edge-return-device/device-reset-2.png)  

O reset apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados do seu dispositivo, esse processo leva cerca de 30 a 40 minutos.

Alternativamente, conecte-se à interface PowerShell `Reset-HcsAppliance` do dispositivo e use o cmdlet para apagar os dados dos discos de dados. Para obter mais informações, consulte [Redefinir seu dispositivo](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se você está trocando ou atualizando para um novo dispositivo, recomendamos que você refina seu dispositivo somente depois de receber o novo dispositivo.
> - O dispositivo redefinido exclui apenas todos os dados locais do dispositivo. Os dados que estão na nuvem não são excluídos e [coletam encargos.](https://azure.microsoft.com/pricing/details/storage/) Esses dados precisam ser excluídos separadamente usando uma ferramenta de gerenciamento de armazenamento em nuvem como [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Abra um bilhete de suporte

Para iniciar o processo de devolução, tome as seguintes etapas.

1. Abra um bilhete de suporte com o Suporte da Microsoft indicando que deseja devolver o dispositivo. Selecione o tipo de problema como **Hardware de Borda da Caixa de Dados**.

    ![Abrir tíquete de suporte](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Um engenheiro de suporte da Microsoft entrará em contato com você. Forneça os detalhes do envio.
3. Se você precisar de uma caixa de transporte de devolução, você pode solicitá-la. Resposta **Sim** à pergunta **Precisa de uma caixa vazia para retornar**.


## <a name="schedule-a-pickup"></a>Agende uma coleta

1. Desligar o dispositivo. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **Desligar**. Quando solicitado para confirmação, clique **em Sim** para continuar. Para obter mais informações, consulte [Gerenciar a energia](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desligue os cabos de alimentação e remova todos os cabos de rede do dispositivo.
4. Prepare o pacote de envio usando sua própria caixa ou a caixa vazia que recebeu do Azure. Coloque o dispositivo e os cabos de alimentação que foram enviados com o dispositivo na caixa.
5. Afixar a etiqueta de envio que você recebeu do Azure no pacote.
6. Agende uma retirada com sua transportadora regional. Se devolver o dispositivo nos EUA, sua operadora pode ser UPS ou FedEx. Para agendar uma coleta com a UPS:

    1. Ligue para o serviço de no-break local (linha gratuita específica do país).
    2. Na sua chamada, cite o número de rastreamento de remessa reversa como mostrado na etiqueta impressa.
    3. Se o número de rastreamento não for citado, o NOS exigirá que você pague uma taxa adicional durante a coleta.

    Em vez de agendar a retirada, você também pode deixar o Data Box Edge no local de entrega mais próximo.

## <a name="delete-the-resource"></a>Exclua o recurso

Depois que o dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado por danos ou quaisquer sinais de adulteração.

- Se o dispositivo chegar intacto e em boa forma, o medidor de faturamento pára por esse recurso. O Microsoft Support entrará em contato para confirmar se o dispositivo foi devolvido. Em seguida, você pode excluir o recurso associado ao dispositivo no portal Azure.
- Se o dispositivo chegar significativamente danificado, multas podem ser aplicadas. Para obter detalhes, consulte o [FAQ sobre dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e termos de serviço do [produto](https://www.microsoft.com/licensing/product-licensing/products).  


Você pode excluir o dispositivo no portal Azure:
-   Depois de ter feito o pedido e antes que o dispositivo seja preparado pela Microsoft.
-   Depois de devolver o dispositivo à Microsoft, ele passa pela inspeção física no data center do Azure e as chamadas do Microsoft Support para confirmar se o dispositivo foi devolvido.

Se você ativou o dispositivo em outra assinatura ou local, a Microsoft moverá seu pedido para a nova assinatura ou local dentro de um dia útil. Depois que a ordem é movida, você pode excluir esse recurso.


Tome as seguintes etapas para excluir o dispositivo e o recurso no portal Azure.

1. No portal Azure, vá para o seu recurso e, em seguida, para **Visão Geral**. Na barra de comando, **selecione Excluir**.

    ![Selecione excluir](media/data-box-edge-return-device/delete-resource-1.png)

2. Na lâmina do **dispositivo Excluir,** digite o nome do dispositivo que deseja excluir e **selecione Excluir**.

    ![Confirmar exclusão](media/data-box-edge-return-device/delete-resource-2.png)

Você é notificado depois que o dispositivo e o recurso associado for excluído com sucesso.

## <a name="get-a-replacement-device"></a>Obter um dispositivo de substituição

Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. Tome as seguintes etapas quando o dispositivo tiver um problema de hardware:

1. [Abra um bilhete de suporte para problema de hardware](#open-a-support-ticket). O Microsoft Support determinará que uma FRU (Field Replacement Unit, unidade de substituição de campo) não está disponível para este caso ou que o dispositivo precisa de uma atualização de hardware. Em ambos os casos, o suporte ordenará um dispositivo de substituição.
2. [Crie um novo recurso](data-box-edge-deploy-prep.md#create-a-new-resource) para o dispositivo de substituição. Certifique-se de selecionar a caixa de seleção contra **eu tenho um dispositivo Data Box Edge**. 
3. Depois de receber um dispositivo de substituição, [instale](data-box-edge-deploy-install.md) e [ative](data-box-edge-deploy-connect-setup-activate.md) o dispositivo de substituição contra o novo recurso.
4. Siga todas as etapas para retornar o dispositivo original:
    1. Abra outro bilhete para devolver o dispositivo original.
    2. [Apagar os dados do dispositivo](#erase-data-from-the-device).
    3. [Agende uma coleta.](#schedule-a-pickup)
    5. [Exclua o recurso](#delete-the-resource) associado ao dispositivo retornado.



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
