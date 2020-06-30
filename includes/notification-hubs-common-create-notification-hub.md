---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095130"
---
### <a name="create-a-notification-hub"></a>Criar um hub de notificação 

Nesta seção, você criará um hub de notificação e configurará a autenticação com a **APNs**. Você pode usar um certificado de push p12 ou a autenticação baseada em token. Se você quiser usar um hub de notificação já criado, passe diretamente à etapa 5.

1. Entre no [Azure](https://portal.azure.com).

1. Clique em **Criar um recurso**, pesquise e escolha **Hub de Notificação** e clique em **Criar**.

1. Atualize os seguinte campos e clique em **Criar**:

    **DETALHES BÁSICOS**  

    **Assinatura:** Escolha a **Assinatura** de destino na lista suspensa  
    **Grupo de recursos:** Crie um **Grupo de recursos** (ou use um grupo existente)  

    **DETALHES DO NAMESPACE**  

    **Namespace do hub de notificação:** Insira um nome globalmente exclusivo para o namespace do **Hub de Notificação**  

    > [!NOTE]
    > Verifique se a opção **Criar** está selecionada para este campo.

    **DETALHES DO HUB DE NOTIFICAÇÃO**  

    **Hub de Notificação:** Insira um nome para o **Hub de Notificação**  
    **Localização:** Escolha uma localização adequada na lista suspensa  
    **Tipo de preço:** Mantenha a opção **Gratuita** padrão  

    > [!NOTE]
    > A menos que você tenha alcançado o número máximo de hubs na Camada gratuita.

1. Quando o **Hub de Notificação** tiver sido provisionado, navegue até esse recurso.
1. Navegue até o novo **Hub de Notificação**.
1. Selecione **Políticas de Acesso** na lista (em **GERENCIAR**).
1. Anote os valores de **Nome da Política** junto com os valores de **Cadeia de Conexão** correspondentes.
