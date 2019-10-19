---
title: Criar Apache Kafka Hub de eventos habilitado-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções para criar um Apache Kafka namespace de hubs de eventos do Azure habilitado usando o portal do Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555807"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Criar Apache Kafka hubs de eventos habilitados

Os hubs de eventos do Azure são uma plataforma de streaming de Big data como um serviço (PaaS) que ingere milhões de eventos por segundo e fornece baixa latência e alta taxa de transferência para análise e visualização em tempo real.

Os hubs de eventos do Azure fornecem um ponto de extremidade Kafka. Esse ponto de extremidade permite que seu namespace de hubs de eventos entenda nativamente [Apache Kafka](https://kafka.apache.org/intro) protocolo de mensagem e APIs. Com esse recurso, você pode se comunicar com seus hubs de eventos como faria com os tópicos do Kafka sem alterar seus clientes de protocolo nem executar seus próprios clusters. Os hubs de eventos oferecem suporte às [versões 1,0](https://kafka.apache.org/10/documentation.html) e posteriores do Apache Kafka.

Este artigo descreve como criar um namespace de hubs de eventos e obter a cadeia de conexão necessária para conectar aplicativos Kafka a hubs de eventos habilitados para Kafka.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um namespace de hubs de eventos habilitados para Kafka

1. Entre no [portal do Azure][Azure portal]e clique em **criar um recurso** na parte superior esquerda da tela.

2. Procure por hubs de eventos e selecione as opções mostradas aqui:
    
    ![Pesquisar hubs de eventos no portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Forneça um nome exclusivo e habilite Kafka no namespace. Clique em **Criar**.
    
    ![Criar um namespace](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Depois que o namespace for criado, na guia **configurações** , clique em **políticas de acesso compartilhado** para obter a cadeia de conexão.

    ![Clique em políticas de acesso compartilhado](./media/event-hubs-create/create-event-hub7.png)

5. Você pode escolher o **RootManageSharedAccessKey**padrão ou adicionar uma nova política. Clique no nome da política e copie a cadeia de conexão. 
    
    ![Selecionar uma política](./media/event-hubs-create/create-event-hub8.png)
 
6. Adicione essa cadeia de conexão à configuração do aplicativo Kafka.

Agora você pode transmitir eventos de seus aplicativos que usam o protocolo Kafka para os hubs de eventos.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre os hubs de eventos, visite estes links:

* [Transmita para os hubs de eventos de seus aplicativos Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Saiba mais sobre os hubs de eventos para Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Saiba mais sobre os hubs de eventos](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
