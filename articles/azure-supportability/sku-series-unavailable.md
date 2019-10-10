---
title: Série SKU não disponível | Microsoft Docs
description: Algumas séries de SKU não estão disponíveis para a assinatura selecionada para essa região.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249038"
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU não disponível
Este artigo descreve como resolver o problema de uma assinatura do Azure sem acesso a uma região ou a um SKU de VM.

## <a name="symptoms"></a>Sintomas

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Ao implantar uma máquina virtual, você recebe uma das seguintes mensagens de erro:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Ao adquirir Instâncias Reservadas de Máquina Virtual, você recebe uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Ao criar uma solicitação de suporte para aumentar a cota de núcleos de computação, uma região ou família de SKU não está disponível para seleção.

## <a name="solution"></a>Solução
Primeiro, é recomendável que você busque uma alternativa de região ou SKU que atenda às suas necessidades de negócios. Se não conseguir encontrar uma região ou SKU adequada, crie uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para "Gerenciamento de Assinaturas" seguindo as etapas abaixo:


- Na página Noções Básicas, selecione o tipo de Problema como "Gerenciamento de Assinaturas" e clique em "Avançar".

![Folha de Noções básicas](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na página Problema, selecione o tipo de Problema, como "Outras Perguntas Gerais".
- Na seção Detalhes:
  - Indique se você deseja implantar máquinas virtuais ou comprar Instâncias Reservadas de Máquina Virtual
  - Especifique a região, SKU e o número de instâncias de máquina virtual que você planeja implantar ou comprar


![Problema](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Digite seus detalhes de contato e clique em "Criar".

![Informações de contato](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Comentários
Estamos sempre abertos a comentários e sugestões! Envie-nos suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode interagir conosco pelo [Twitter](https://twitter.com/azuresupport) ou nos [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Saiba mais
[Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq)

