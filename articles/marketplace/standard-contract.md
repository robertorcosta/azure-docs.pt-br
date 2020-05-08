---
title: Contrato padrão do Microsoft Commercial Marketplace
description: Contrato padrão para o Azure Marketplace e o AppSource no Partner Center
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 3886b29a47edcfb36114cedf8b8edb3799e0c345
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871441"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contrato padrão do Microsoft Commercial Marketplace

A Microsoft oferece um contrato padrão para o Microsoft Commercial Marketplace. Isso ajuda a simplificar o processo de aquisição para clientes, reduzir a complexidade legal para fornecedores de software e facilitar as transações no Marketplace. Em vez de criar termos e condições personalizados, como um editor de mercado comercial, você pode optar por oferecer seu software sob o [contrato padrão](https://go.microsoft.com/fwlink/?linkid=2041178), que os clientes precisam apenas examinaremos e aceitar uma vez.

Os termos e condições de uma oferta são definidos ao criar a oferta no Partner Center. Você pode optar por usar o contrato padrão para o Marketplace comercial da Microsoft em vez de fornecer seus próprios termos e condições personalizados.

>[!Note]
>Depois de publicar uma oferta usando o contrato padrão do Microsoft Commercial Marketplace, você não poderá usar seus próprios termos e condições personalizados. É um cenário de "ou". Você pode oferecer sua solução sob o contrato Standard *ou* seus próprios termos e condições. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

## <a name="standard-contract-amendments"></a>Emendas de contrato padrão

As emendas de contrato padrão permitem que os editores selecionem o contrato padrão para simplificar e com termos personalizados para seus produtos ou negócios. Os clientes precisam apenas examinar as emendas ao contrato, caso já tenham revisado e aceito o contrato padrão da Microsoft.

Há dois tipos de emendas disponíveis para editores de mercado comercial:

* Emendas universais: essas emendas são aplicadas universalmente ao contrato padrão para todos os clientes. As emendas universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do contrato padrão e o aditamento antes de poderem usar sua oferta.

* Emendas personalizadas: essas emendas são emendas especiais ao contrato padrão que são direcionados a clientes específicos somente por meio de IDs de locatário do Azure. Os editores podem escolher o locatário que desejam direcionar. Somente os clientes do locatário serão apresentados com os termos personalizados de emenda no fluxo de compra da oferta.  Os clientes devem aceitar os termos do contrato padrão e as emendas antes de poderem usar sua oferta.

>[!Note]
>Esses dois tipos de emendas se empilham um sobre o outro. Os clientes destinados a emendas personalizadas também terão a emenda universal ao contrato padrão durante a compra.

Você pode aproveitar o contrato padrão do Microsoft Commercial Marketplace para os seguintes tipos de oferta: aplicativos do Azure (modelos de solução e aplicativos gerenciados), máquinas virtuais, contêineres, aplicativos de contêiner, módulos de IoT Edge e SaaS.

## <a name="customer-experience"></a>Experiência do cliente

Durante a experiência de descoberta no Azure Marketplace ou no AppSource, os clientes poderão ver os termos associados à oferta como contrato padrão para o Marketplace comercial da Microsoft e quaisquer emendas universais.

![A experiência de descoberta do cliente portal do Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal do Azure, os clientes poderão ver os termos associados à oferta como o contrato padrão para o Marketplace comercial da Microsoft e quaisquer emendas universais e/ou específicas de locatário.

![A experiência de compra portal do Azure cliente.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem usar Get-Azurermmarketplacetermsget para recuperar os termos de uma oferta e aceitá-lo. O contrato padrão e as emendas associadas serão retornados na saída do cmdlet.
