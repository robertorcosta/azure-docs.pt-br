---
title: APIs de preenchimento de SaaS no Microsoft Commercial Marketplace
description: Uma introdução às APIs de preenchimento que permitem que você integre suas ofertas de SaaS no Microsoft AppSource e no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858079"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>APIs de preenchimento de SaaS no Microsoft Commercial Marketplace

As APIs de cumprimento de SaaS permitem que fornecedores de software independentes (ISVs) integrem seus aplicativos SaaS no Microsoft AppSource e no Azure Marketplace. Essas APIs permitem que aplicativos ISV participem de todos os canais habilitados para comércio: direto, orientado por parceiros (revendedor) e orientado por campo. Eles são necessários para listar as ofertas de SaaS que podem ser acessadas no Microsoft AppSource e no Azure Marketplace.

> [!WARNING]
> A versão atual desta API é a versão 2, que deve ser usada para todas as novas ofertas de SaaS.  A versão 1 da API foi preterida e está sendo mantida para dar suporte às ofertas existentes.

## <a name="business-model-support"></a>Suporte a modelos de negócios

Esta API dá suporte aos seguintes recursos de modelo de negócios; É possível:

* Especifique vários planos para uma oferta. Esses planos têm uma funcionalidade diferente e podem ter preços diferentes.
* Forneça uma oferta por site ou por modelo de cobrança por usuário.
* Forneça opções de cobrança mensal e anual (pagas antecipadas).
* Forneça preços privados a um cliente com base em um contrato de negócios negociado.


## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, Registre seu aplicativo SaaS na [portal do Azure](https://ms.portal.azure.com) , conforme explicado em [registrar um aplicativo do Azure ad](./pc-saas-registration.md).  Posteriormente, use a versão mais atual desta interface para desenvolvimento: [API de preenchimento de SaaS versão 2](./pc-saas-fulfillment-api-v2.md).
