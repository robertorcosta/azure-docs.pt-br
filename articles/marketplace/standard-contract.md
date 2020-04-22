---
title: Contrato Padrão | Mercado Azure
description: Contrato padrão para Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681456"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

Para simplificar o processo de aquisição para os clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial da Microsoft, a fim de ajudar a facilitar as transações no mercado. Em vez de criar termos e condições personalizados, os editores de marketplace comercial podem optar por oferecer seu software sob o Contrato Padrão, que os clientes só precisam vetar e aceitar uma vez. O Contrato Padrão pode [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)ser encontrado aqui: .

Os termos e condições para uma oferta são definidos ao criar a oferta no Partner Center. Você pode selecionar para usar o Contrato Padrão para o mercado comercial da Microsoft em vez de fornecer seus próprios termos e condições personalizados.

>[!Note]
>Uma vez que você publica uma oferta usando o contrato Standard para o mercado comercial da Microsoft, você não pode usar seus próprios termos e condições personalizados. É um cenário "ou". Ou você oferece sua solução sob o Contrato Padrão *ou* seus próprios termos e condições. Se você quiser modificar os termos do Contrato Padrão, você pode fazê-lo através de Alterações de Contrato Padrão.

## <a name="standard-contract-amendments"></a>Alterações de contrato padrão

As Alterações de Contrato Padrão permitem que os editores selecionem o Contrato Padrão para simplicidade e com termos personalizados para seu produto ou negócio. Os clientes só precisam rever as alterações do contrato, se já revisaram e aceitaram o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para editores de mercado comercial:

* Emendas Universais: Essas alterações são aplicadas universalmente ao Contrato Padrão para todos os clientes. Alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e da alteração antes de poderem usar sua oferta.

* Alterações Personalizadas: Essas alterações são alterações especiais no Contrato Padrão que são direcionadas a clientes específicos apenas através de IDs de inquilinos do Azure. Os editores podem escolher o inquilino que querem atingir. Somente os clientes do inquilino serão apresentados com os termos de alteração aduaneira no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem usar sua oferta.

>[!Note]
>Esses dois tipos de alterações se acumulam em cima um do outro. Os clientes alvo de alterações personalizadas também receberão a alteração universal do Contrato Padrão durante a compra.

Você pode aproveitar o Contrato Padrão para o mercado comercial da Microsoft para os seguintes tipos de ofertas: Azure Applications (Modelos de Solução e Aplicativos Gerenciados), Máquinas Virtuais, Containers, Aplicativos de Contêineres, Módulos de Borda IoT e SaaS.

## <a name="customer-experience"></a>Experiência do cliente

Durante a experiência de descoberta no mercado Azure ou AppSource, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais.

![A experiência de descoberta de clientes do portal Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal Azure, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais e/ou específicas para inquilinos.

![A experiência de compra do cliente do portal Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem usar o Get-AzureRmMarketplaceTerms para recuperar os termos de uma oferta e aceitá-la. O Contrato Padrão e as alterações associadas serão devolvidas na saída do cmdlet.
