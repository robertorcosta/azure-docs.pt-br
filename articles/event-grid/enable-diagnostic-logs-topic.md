---
title: Azure Event Grid - Habilite registros de diagnóstico para um tópico
description: Este artigo fornece instruções passo-a-passo sobre como ativar registros de diagnóstico para um tópico da grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960497"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Registros de diagnóstico para um tópico da grade de eventos do Azure
As configurações de diagnóstico permitem que os usuários do Event Grid capturem e visualizem a falha de publicação e entrega em um dos seguintes lugares: uma conta de armazenamento Azure, um hub de eventos ou um espaço de trabalho do Log Analytics. Este artigo fornece instruções passo-a-passo para habilitar registros de diagnóstico para um tópico da grade de eventos.

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grade de eventos provisionado
- Um destino provisionado para captura de registros de diagnóstico. Ele pode um dos seguintes destinos:
    - Conta de Armazenamento do Azure
    - Hub de Eventos
    - Espaço de trabalho do Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Etapas para habilitar registros de diagnóstico para um tópico

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue até o tópico da grade de eventos para o qual você deseja ativar as configurações do registro de diagnóstico. 
3. Selecione **Configurações de diagnóstico** em **Monitoramento** no menu esquerdo.
4. Na página **Configurações** de diagnóstico, selecione **Adicionar nova configuração de diagnóstico**. 
    
    ![Adicionar botão de ajuste de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a configuração de diagnóstico. 

    ![Configurações de diagnóstico - nome](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Habilite um ou mais destinos de captura para os logs e, em seguida, configure-os selecionando um recurso de captura criado anteriormente. 
    - Se você selecionar **Arquivamento para uma conta de armazenamento,** selecione **'Armazenar conta ' - Configurar**e, em seguida, selecione a conta de armazenamento na assinatura do Azure. 

        ![Arquivar para uma conta de armazenamento do Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se você selecionar **Stream para um hub de eventos,** selecione Centro de eventos - **Configurar**e, em seguida, selecione o namespace do Event Hubs, o hub de eventos e a política de acesso. 
        ![Fluxo para um hub de eventos](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se você selecionar **Enviar para Log Analytics,** selecione o espaço de trabalho do Log Analytics.
        ![Enviar para o Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selecione as opções **'Falhade entrega** **e'spublish's',** na seção **Log.** 
    ![Selecione as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selecione **Salvar**. Selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página **Configurações de Diagnóstico,** confirme se você vê uma nova entrada na tabela **Configurações de diagnóstico.** 
    ![Configuração de diagnóstico na lista](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Você também pode habilitar a coleta de todas as métricas para o tópico. 

## <a name="next-steps"></a>Próximas etapas
Se você precisar de mais ajuda, poste seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [suporte](https://azure.microsoft.com/support/options/). 
