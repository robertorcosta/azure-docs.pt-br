---
title: Política de gerenciamento de API de exemplo – enviar erros para Stackify para registro em log
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como adicionar uma política de registro em log de erros para enviar erros para Stackify para registro em log.
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442450"
---
# <a name="send-errors-to-stackify-for-logging"></a>Enviar erros para o Stackify para registro em log

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar uma política de registro em log de erros para enviar erros para o Stackify para registro em log. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

