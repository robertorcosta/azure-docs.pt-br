---
title: Criar assinaturas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar assinaturas no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "70073533"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Criar assinaturas no Gerenciamento de API do Azure

Quando você publica APIs por meio do Gerenciamento de API do Azure, é fácil e normal proteger o acesso a essas APIs usando chaves de assinatura. Os aplicativos cliente que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP quando fazem chamadas a essas APIs. É necessária uma assinatura para obter uma chave de assinatura para acessar APIs. Para obter mais informações sobre assinaturas, consulte [assinaturas no gerenciamento de API do Azure](api-management-subscriptions.md).

Este artigo explica as etapas para criar assinaturas no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas neste artigo, os pré-requisitos são da seguinte maneira:

+ [Crie uma instância de gerenciamento de API](get-started-create-service-instance.md).
+ Compreenda as [assinaturas no Gerenciamento de API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

1. Selecione **Assinaturas** no menu à esquerda.
2. Selecione **Adicionar assinatura**.
3. Forneça um nome da assinatura e selecione o escopo.
4. Opcionalmente, escolha se a assinatura deve ser associada a um usuário.
5. Clique em **Salvar**.

![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

Depois de criar a assinatura, duas chaves de API são fornecidas para acessar as APIs. Uma chave é primária e uma é secundária. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Gerenciamento de API:

+ Aprenda outros [conceitos](api-management-terminology.md) no gerenciamento de API.
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o gerenciamento de API.
+ Confira nossa [página de perguntas frequentes](api-management-faq.md) para perguntas comuns.