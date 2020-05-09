---
title: Início Rápido do Azure – Criar um hub de eventos usando o portal do Azure
description: Neste início rápido, você aprenderá a criar um hub de eventos do Azure usando o portal do Azure e, em seguida, enviar e receber eventos usando o SDK do .NET Standard.
services: event-hubs
documentationcenter: ''
author: spelluru
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/04/2020
ms.author: spelluru
ms.openlocfilehash: c678965048a6437deb8cbf39f38f12dc116d39ab
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743941"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início Rápido: Criar um hub de eventos usando o portal do Azure
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Neste início rápido, você cria um hub de eventos usando o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, verifique se você tem:

- Assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- [Visual Studio 2019)](https://www.visualstudio.com/vs) ou posterior.
- [SDK do .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica dos recursos do Azure. Todos os recursos são implantados e gerenciados em um grupo de recursos. Para criar um grupo de recursos:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Grupos de recursos**. Clique em **Adicionar**.

   ![Grupos de recursos - botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Para **Assinatura**, selecione o nome da assinatura do Azure na qual você deseja criar o grupo de recursos.
3. Digite um **nome exclusivo para o grupo de recursos**. O sistema imediatamente verifica para ver se o nome está disponível na assinatura do Azure selecionada no momento.
4. Selecione uma **região** para o grupo de recursos.
5. Selecione **Examinar + criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na página **Examinar + Criar**, selecione **Criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Um namespace de Hubs de Eventos fornece um contêiner de escopo exclusivo, referenciado pelo nome de domínio totalmente qualificado, em que você cria uma ou mais hubs de eventos. Para criar um namespace em seu grupo de recursos usando o portal, faça o seguinte:

1. Faça logon no portal do Azure e clique em **Criar um recurso** na parte superior esquerda da tela.
2. Selecione **Todos os serviços** no menu à esquerda e selecione **estrela (`*`)** ao lado de **Hubs de Eventos** na categoria **Análise**. Confirme que **Hubs de Eventos** foi adicionado à **FAVORITOS** no menu de navegação à esquerda. 
    
   ![Pesquisar Hubs de Eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecione **Hubs de Eventos** em **FAVORITOS** no menu de navegação à esquerda e selecione **Adicionar** na barra de ferramentas.

   ![Botão Adicionar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na página **Criar namespace**, execute as seguintes etapas:
    1. Selecione a **assinatura** na qual você deseja criar o namespace.
    2. Selecione o **grupo de recursos** criado na etapa anterior. 
    3. Insira um **nome** para o namespace. O sistema imediatamente verifica para ver se o nome está disponível.
    4. Selecione uma **localização** para o namespace.    
    5. Escolha o **tipo de preço** (Básico ou Standard).  
    6. Não altere as configurações das **unidades de produtividade**. Para saber mais sobre as unidades de produtividade, confira [Escalabilidade dos Hubs de Eventos](event-hubs-scalability.md#throughput-units)  
    5. Selecione **Examinar + Criar** na parte inferior da página.

       ![Criar um namespace do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   6. Na página **Examinar + Criar**, examine as configurações e selecione **Criar**. Aguarde até que a implantação seja concluída. 

       ![Página Examinar + criar](./media/event-hubs-quickstart-portal/review-create.png)
   7. Na página **Implantação**, selecione **Ir para o recurso** para navegar até a página de seu namespace. 

      ![Implantação concluída – Ir para o recurso](./media/event-hubs-quickstart-portal/deployment-complete.png)
   8. Confirme que você vê a página **Namespace dos Hubs de Eventos** semelhante ao seguinte exemplo: 

       ![Home page do namespace](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

       > [!NOTE]
       > Hubs de Eventos do Azure fornecem um ponto de extremidade do Kafka. Esse ponto de extremidade permite que seu namespace de Hubs de eventos entenda nativamente a mensagem de protocolo [Kafka Apache](https://kafka.apache.org/intro) e as APIs. Com essa funcionalidade, você pode se comunicar com seus Hubs de eventos, como você faria com tópicos Kafka sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Hubs de Eventos dão suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html) e posterior. Para obter mais informações, confira [Usar os Hubs de Eventos de aplicativos Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Para criar um hub de eventos dentro do namespace, faça o seguinte:

1. Na página Namespace de Hubs de Eventos, selecione **Hubs de Eventos** no menu à esquerda.
1. Na parte superior da janela, clique em **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Digite um nome para seu hub de eventos e clique em **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Você pode verificar o status da criação do hub de eventos em alertas. Depois que o hub de eventos for criado, você o verá na lista de Hubs de Eventos conforme mostrado na imagem a seguir:

    ![Hub de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um grupo de recursos, um namespace de Hubs de Eventos e um hub de eventos. Para obter instruções passo a passo sobre como enviar eventos (ou) receber eventos de um hub de eventos, confira os tutoriais para **Enviar e receber eventos**: 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente receber)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
