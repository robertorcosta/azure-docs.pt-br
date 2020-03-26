---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "66419954"
---
Etapa final é preparar o dispositivo para o envio. Nesta etapa, todos os compartilhamentos do dispositivo são colocados offline. As ações não poderão ser acessadas depois que você iniciar esse processo.

> [!IMPORTANT]
> A preparação para o envio é obrigatória, pois sinaliza os dados que não estão em conformidade com as convenções de nomenclatura do Azure. Ignorar essa etapa pode causar eventuais falhas no upload dos dados provocadas por dados fora de conformidade.

1. Vá para **Preparar para enviar** e clique em **Iniciar preparação**. Por padrão, as somas de verificação são computadas enquanto os dados estão sendo copiados. A preparação para o envio conclui o cálculo de soma de verificação e cria a lista de arquivos (também conhecida como *arquivos BOM* ou manifesto). O cálculo da soma de verificação pode levar de horas a dias dependendo do tamanho dos seus dados.
   
    ![Preparar para o envio 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Se, por algum motivo, você desejar interromper a preparação do dispositivo, clique em **Parar a preparação**. É possível retomar a preparação para o envio mais tarde.
        
    ![Preparar para o envio 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para o envio é iniciada e os compartilhamentos do dispositivo ficam offline. Você verá um lembrete para baixar a etiqueta de remessa depois que o dispositivo estiver pronto.

    ![Baixar o lembrete da etiqueta de remessa](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. O status do dispositivo é atualizado para *Pronto para envio* e o dispositivo será bloqueado depois que sua preparação for concluída.
        
    ![Preparar para o envio 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Se desejar copiar mais dados para o dispositivo, será possível desbloqueá-lo, copiar mais dados e executar a preparação para o envio novamente.

    Se houver erros nesta etapa, baixe o log de erros e resolva os erros. Depois de resolver os erros, execute **Preparar para o envio**.

4. Depois que a preparação para o envio for concluída com êxito (sem erros), baixe a lista de arquivos (também conhecida como *arquivos BOM* ou manifesto) copiada neste processo. 

    ![Baixar a lista de arquivos ou arquivos BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Posteriormente, você poderá usar essa lista para verificar os arquivos carregados no Azure. Para obter mais informações, confira [Inspecionar arquivos de BOM durante a Preparação para o envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Arquivo BOM de exemplo](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Desligar o dispositivo. Vá para **desligar ou reiniciar** da página e clique em **desligar**. Quando solicitado a confirmação, clique em **OK** para continuar.

    ![Desligar o primeiro nó do dispositivo](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Repita todas as etapas acima para o outro nó do dispositivo.
7. Depois que o dispositivo for completamente desligado, todos os LEDs na parte traseira do dispositivo deverão ser desligados. A próxima etapa será remover todos os cabos e enviar o dispositivo para a Microsoft.
