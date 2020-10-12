---
title: Política de gerenciamento de API de exemplo – autorizar o acesso com base em declarações JWT
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como autorizar o acesso aos métodos HTTP específicos em uma API com base em declarações JWT.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75422175"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizar o acesso com base em declarações JWT

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como autorizar o acesso aos métodos HTTP específicos em uma API com base em declarações JWT. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

