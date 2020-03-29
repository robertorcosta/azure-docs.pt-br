---
title: Série sku ou região indisponível - Azure
description: Algumas séries SKU não estão disponíveis para a assinatura selecionada para esta região, o que pode exigir solicitação de suporte ao gerenciamento de assinaturas.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843611"
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU não disponível

Este artigo descreve como resolver o problema de uma assinatura do Azure sem acesso a uma região ou a um SKU de VM.

## <a name="symptoms"></a>Sintomas

Ao implantar uma máquina virtual, você recebe uma das seguintes mensagens de erro:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

Ao adquirir Instâncias Reservadas de Máquina Virtual, você recebe uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Ao criar uma solicitação de suporte para aumentar a cota principal de cálculo, uma região ou uma família SKU não está disponível para seleção.

## <a name="solution"></a>Solução

Primeiro, é recomendável que você busque uma alternativa de região ou SKU que atenda às suas necessidades de negócios.

Se você não conseguir encontrar uma região adequada ou SKU, crie uma solicitação de [suporte de](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **gerenciamento de assinatura** seguindo estas etapas:

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**. Em seguida, selecione **Nova solicitação de suporte**.

1. No **Básico,** para **o tipo Demissão,** selecione Gerenciamento de **assinaturas.**

1. Selecione uma **assinatura** e digite uma breve descrição em **Resumo**.

   ![Guia básica da nova solicitação de suporte](./media/SKU-series-unavailable/support-request-basics.png)

1. Para **o tipo Problema,** escolha **Selecionar o tipo de problema**.

1. Para **selecionar o tipo de problema,** escolha uma opção, por exemplo, Não é possível acessar minha assinatura ou **recurso** > **Meu problema não está listado acima**. Selecione **Salvar**.

   ![Especifique um problema para a solicitação](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecione **A seguir: Soluções** para explorar possíveis soluções. Se necessário, **selecione Next: Detalhes** a continuar.

1. Digite qualquer informação adicional que você possa fornecer, juntamente com suas informações de contato.

1. Selecione **Revisão + criar**. Depois de verificar suas informações, selecione **Criar** para criar a solicitação.

## <a name="send-us-your-suggestions"></a>Envie-nos suas sugestões

Estamos sempre abertos a comentários e sugestões! Envie-nos suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode se envolver conosco no [Twitter](https://twitter.com/azuresupport) ou nos fóruns do [MSDN.](https://social.msdn.microsoft.com/Forums/azure)

## <a name="learn-more"></a>Saiba mais

[Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq)
