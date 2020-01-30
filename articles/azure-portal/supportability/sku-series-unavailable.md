---
title: Região ou série de SKUs não disponíveis-Azure
description: Algumas séries de SKU não estão disponíveis para a assinatura selecionada para esta região, o que pode exigir solicitação de suporte de gerenciamento de assinatura.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
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

Ao criar uma solicitação de suporte para aumentar a cota de núcleo de computação, uma região ou uma família de SKUs não está disponível para seleção.

## <a name="solution"></a>Solução

Primeiro, é recomendável que você busque uma alternativa de região ou SKU que atenda às suas necessidades de negócios.

Se não for possível encontrar uma região ou SKU adequada, crie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de **Gerenciamento de assinatura** seguindo estas etapas:

1. No menu [portal do Azure](https://portal.azure.com) , selecione **ajuda + suporte**. Em seguida, selecione **Nova solicitação de suporte**.

1. Em **noções básicas**, para **tipo de problema**, selecione gerenciamento de **assinatura**.

1. Selecione uma **assinatura** e insira uma breve descrição em **Resumo**.

   ![Guia básico de nova solicitação de suporte](./media/SKU-series-unavailable/support-request-basics.png)

1. Para **tipo de problema**, escolha **Selecionar tipo de problema**.

1. Para **Selecionar tipo de problema**, escolha uma opção, por exemplo, **não é possível acessar minha assinatura ou recurso** > **meu problema não está listado acima**. Clique em **Salvar**.

   ![Especificar um problema para a solicitação](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecione **Avançar: soluções** para explorar as possíveis soluções. Se necessário, selecione **Avançar: detalhes** para continuar.

1. Insira as informações adicionais que você pode fornecer, juntamente com suas informações de contato.

1. Selecione **Examinar + criar**. Depois de verificar suas informações, selecione **criar** para criar a solicitação.

## <a name="send-us-your-suggestions"></a>Envie-nos suas sugestões

Estamos sempre abertos para comentários e sugestões! Envie-nos suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode entrar em contato conosco no [Twitter](https://twitter.com/azuresupport) ou nos [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Saiba mais

[Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq)
