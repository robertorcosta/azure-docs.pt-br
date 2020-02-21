---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: b674f8e31eb61328f60bb24866f73d02653b655f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495149"
---
**Zonas DNS públicas**

| Recurso | Limite padrão |
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

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o suporte do Azure.

<sup>2</sup> Esses limites são aplicados a todas as máquinas virtuais individuais e não no nível de rede virtual. As consultas DNS que excedem esses limites são descartadas.