---
title: Política de gerenciamento de API de exemplo - Filtrar no endereço IP ao usar o Gateway de aplicativo
titleSuffix: Azure API Management
description: Amostra da política de gerenciamento da API do Azure - Demonstra como filtrar no endereço IP de solicitação ao usar um Gateway de aplicativo.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942471"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrar no endereço IP de solicitação ao usar um gateway de aplicativo

Este artigo mostra uma amostra de política de gerenciamento de API do Azure que demonstra como filtrar o endereço IP da solicitação quando a instância de gerenciamento de API é acessada através de um Gateway de aplicativo ou outro intermediário. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de restrição de acesso](../api-management-access-restriction-policies.md)
+ [Exemplos de política](../policy-samples.md)
