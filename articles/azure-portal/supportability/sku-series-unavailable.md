---
title: Série de região ou SKU não disponível – Azure
description: Algumas séries de SKU não estão disponíveis para a assinatura selecionada para esta região, o que pode exigir solicitação de suporte de gerenciamento de assinatura.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 87533b2e0c1397d0b8b2e9a89450a80a75c5e348
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763815"
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

Ao criar uma solicitação de suporte para aumentar a cota de núcleos de computação, uma região ou família de SKU não está disponível para seleção.

## <a name="solution"></a>Solução

Primeiro, é recomendável que você busque uma alternativa de região ou SKU que atenda às suas necessidades de negócios.

Se não conseguir encontrar uma região ou SKU adequada, crie uma **solicitação de suporte** para [Gerenciamento de Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguindo essas etapas:

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**. Em seguida, selecione **Nova solicitação de suporte**.

1. Em **Básico**, para **Tipo de problema**, selecione **Gerenciamento de assinatura**.

1. Selecione uma **Assinatura** e insira uma breve descrição em **Resumo**.

   ![Guia Básico da Nova solicitação de suporte](./media/SKU-series-unavailable/support-request-basics.png)

1. Para **Tipo de problema**, escolha **Selecionar tipo de problema**.

1. Para **Selecionar tipo de problema**, escolha uma opção, por exemplo, **Não é possível acessar minha assinatura ou recurso** > **Meu problema não está listado acima**. Clique em **Salvar**.

   ![Especificar um problema para a solicitação](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecione **Avançar: Soluções** para explorar possíveis soluções. Se necessário, selecione **Avançar: Detalhes** para continuar.

1. Insira as informações adicionais que você pode fornecer, juntamente com suas informações de contato.

1. Selecione **Examinar + criar**. Depois de verificar suas informações, selecione **Criar** para criar a solicitação.

## <a name="send-us-your-suggestions"></a>Envie-nos suas sugestões

Estamos sempre abertos a comentários e sugestões! Envie-nos suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode falar conosco pelo [Twitter](https://twitter.com/azuresupport) ou pela [Página de perguntas e respostas da Microsoft](https://docs.microsoft.com/answers/products/azure).

## <a name="learn-more"></a>Saiba mais

[Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq)
