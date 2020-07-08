---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 434b79a2b178defd9543e1d3ad087bb5282cb287
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805490"
---
**Zonas DNS públicas**

| Recurso | Limite |
| --- | --- |
| Zonas DNS público por assinatura |250 <sup>1</sup> |
| Conjuntos de registros por zona DNS pública |10.000 <sup>1</sup> |
| Registros por conjunto de registro na zona DNS pública |20 |
| Número de registros de alias para um único recurso do Azure |20|
| Zonas de DNS privado por assinatura |1000|
| Conjuntos de registros por zona DNS privada |25000|
| Registros por conjunto de registro para zonas DNS privadas |20|
| Links de rede virtual por zona DNS privada |1000|
| Links de redes virtuais por zonas DNS privadas com o registro automático habilitado |100|
| Número de zonas DNS privadas às quais uma rede virtual pode ser vinculada com o registro automático habilitado |1|
| Número de zonas DNS privadas que uma rede virtual pode obter vinculada |1000|
| Número de consultas DNS que uma máquina virtual pode enviar para o resolvedor de DNS do Azure, por segundo |500 <sup>2</sup> |
| Número máximo de consultas DNS enfileiradas (resposta pendente) por máquina virtual |200 <sup>2</sup> |

<sup>1</sup>Se precisar aumentar esses limites, entre em contato com o Suporte do Azure.

<sup>2</sup> Esses limites são aplicados a todas as máquinas virtuais individuais e não no nível de rede virtual. As consultas DNS que excedem esses limites são descartadas.
