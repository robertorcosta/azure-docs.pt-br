---
title: Executando a exportação invocando $export comando na API do Azure para FHIR
description: Este artigo descreve como configurar e usar a exportação de-identificada
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ecc2134d1a528ee22710cb447f996e0c5e31a8de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308173"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR

Antes de usar $export, você desejará certificar-se de que a API do Azure para FHIR está configurada para usá-la. Para definir configurações de exportação e criar conta de armazenamento do Azure, consulte [a página configurar dados de exportação](configure-export-data.md).

## <a name="using-export-command"></a>Usando o comando $export

Depois de configurar a API do Azure para FHIR para exportação, você pode usar o comando $export para exportar os dados para fora do serviço. Os dados serão armazenados na conta de armazenamento que você especificou ao configurar a exportação. Para saber como invocar $export comando no servidor FHIR, leia a documentação sobre a [especificação de $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

O comando $export na API do Azure para FHIR usa um parâmetro de _ \_ contêiner_ opcional que especifica o contêiner dentro da conta de armazenamento configurada onde os dados devem ser exportados.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Cenários com suporte

A API do Azure para FHIR dá suporte a $export no nível do sistema, do paciente e do grupo. Para a exportação de grupo, exportamos todos os recursos relacionados, mas não exportamos as características do grupo.

> [!Note] 
> $export irá exportar recursos duplicados se o recurso estiver em um compartimento de mais de um recurso ou em vários grupos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a exportar recursos do FHIR usando o comando $export. Em seguida, você pode examinar nossos recursos com suporte
 
>[!div class="nextstepaction"]
>[Recursos compatíveis](fhir-features-supported.md)
