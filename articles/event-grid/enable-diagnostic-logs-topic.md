---
title: Grade de eventos do Azure – Habilitar logs de diagnóstico para um tópico
description: Este artigo fornece instruções passo a passo sobre como habilitar os logs de diagnóstico para um tópico da grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76960497"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Logs de diagnóstico para um tópico da grade de eventos do Azure
As configurações de diagnóstico permitem que os usuários da grade de eventos capturem e exibam logs de falha de publicação e entrega em um dos seguintes locais: uma conta de armazenamento do Azure, um hub de eventos ou um espaço de trabalho Log Analytics. Este artigo fornece instruções passo a passo para habilitar os logs de diagnóstico para um tópico da grade de eventos.

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grade de eventos provisionado
- Um destino provisionado para capturar logs de diagnóstico. Ele pode ser um dos seguintes destinos:
    - Conta de Armazenamento do Azure
    - Hub de Eventos
    - Espaço de trabalho do Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Etapas para habilitar os logs de diagnóstico para um tópico

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até o tópico da grade de eventos para o qual você deseja habilitar as configurações do log de diagnóstico. 
3. Selecione **configurações de diagnóstico** em **monitoramento** no menu à esquerda.
4. Na página **configurações de diagnóstico** , selecione **Adicionar nova configuração de diagnóstico**. 
    
    ![Botão Adicionar configuração de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a configuração de diagnóstico. 

    ![Configurações de diagnóstico-nome](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Habilite um ou mais dos destinos de captura para os logs e, em seguida, configure-os selecionando um recurso de captura criado anteriormente. 
    - Se você selecionar **arquivar em uma conta de armazenamento**, selecione **conta de armazenamento-configurar**e, em seguida, selecione a conta de armazenamento em sua assinatura do Azure. 

        ![Arquivar em uma conta de armazenamento do Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se você selecionar **fluxo para um hub de eventos**, selecione **Hub de eventos-configurar**e, em seguida, selecione o namespace de hubs de eventos, o Hub de eventos e a política de acesso. 
        ![Transmitir para um hub de eventos](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se você selecionar **Enviar para log Analytics**, selecione o espaço de trabalho log Analytics.
        ![Enviar para o Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selecione as opções **DeliveryFailures** e **PublishFailures** na seção **log** . 
    ![Selecionar as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selecione **Salvar**. Selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página **configurações de diagnóstico** , confirme se você vê uma nova entrada na tabela configurações de **diagnóstico** . 
    ![Configuração de diagnóstico na lista](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Você também pode habilitar a coleta de todas as métricas para o tópico. 

## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [Fórum de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
