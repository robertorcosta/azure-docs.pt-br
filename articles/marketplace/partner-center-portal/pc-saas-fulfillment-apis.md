---
title: APIs de preenchimento de SaaS | Azure Marketplace
description: Apresenta as versões das APIs de preenchimento que permitem que você integre suas ofertas de SaaS com o Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275723"
---
# <a name="saas-fulfillment-apis"></a>APIs de cumprimento de SaaS

As APIs de cumprimento de SaaS permitem que fornecedores de software independentes (ISVs) integrem seus aplicativos SaaS com o Azure Marketplace. Essas APIs permitem que aplicativos ISV participem de todos os canais habilitados para comércio: direto, orientado por parceiros (revendedor) e orientado por campo.  Eles são um requisito para listar as ofertas de SaaS transactáveis no Azure Marketplace.

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
