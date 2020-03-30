---
title: APIs de cumprimento do SaaS | Mercado Azure
description: Apresenta as versões das APIs de preenchimento que permitem integrar suas ofertas SaaS com o Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275723"
---
# <a name="saas-fulfillment-apis"></a>APIs de cumprimento de SaaS

As APIs de preenchimento do SaaS permitem que os fornecedores independentes de software (ISVs) integrem seus aplicativos SaaS com o Azure Marketplace. Essas APIs permitem que os aplicativos ISV participem de todos os canais habilitados para o comércio: direto, liderado por parceiros (revendedor) e liderado por campo.  Eles são um requisito para listar ofertas transacíveis de SaaS no Azure Marketplace.

> [!WARNING]
> A versão atual desta API é a versão 2, que deve ser usada para todas as novas ofertas do SaaS.  A versão 1 da API é preterida e está sendo mantida para suportar ofertas existentes.


## <a name="business-model-support"></a>Suporte ao modelo de negócios

Esta API suporta os seguintes recursos de modelo de negócios; É possível:

* Especifique vários planos para uma oferta. Esses planos têm funcionalidades diferentes e podem ter preços diferentes.
* Forneça uma oferta por site ou por um modelo de faturamento do usuário.
* Fornecer opções de faturamento mensais e anuais (pagas antecipadamente).
* Fornecer preços privados a um cliente com base em um acordo comercial negociado.


## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, registre seu aplicativo SaaS no [portal Azure](https://ms.portal.azure.com) conforme explicado no [Register a Azure AD Application](./pc-saas-registration.md).  Posteriormente, use a versão mais atual desta interface para desenvolvimento: [SaaS Fulfillment API Versão 2](./pc-saas-fulfillment-api-v2.md).
