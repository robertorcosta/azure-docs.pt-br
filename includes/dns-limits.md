---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335067"
---
**Zonas públicas de DNS**

| Recurso | Limite |
| --- | --- |
| Zonas Públicas de DNS por assinatura |250 <sup>1</sup> |
| Conjuntos de recordes por zona de DNS pública |10.000 <sup>1</sup> |
| Recordes por registro estabelecido satiner na zona dns pública |20 |
| Número de registros do Alias para um único recurso do Azure |20|
| Zonas de DNS privadas por assinatura |1000|
| Conjuntos de registros por zona de DNS privada |25000|
| Registros por registro definido para zonas privadas de DNS |20|
| Links de rede virtual por zona de DNS privada |1000|
| Links de redes virtuais por zonas de DNS privadas com registro automático ativado |100|
| Número de zonas DNS privadas a que uma rede virtual pode ser vinculada ao registro automático ativado |1|
| Número de zonas DNS privadas uma rede virtual pode ser vinculada |1000|
| Número de consultas de DNS que uma máquina virtual pode enviar para o Azure DNS resolver, por segundo |500 <sup>2</sup> |
| Número máximo de consultas DNS enfileiradas (resposta pendente) por máquina virtual |200 <sup>2</sup> |

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o Suporte Azure.

<sup>2</sup> Esses limites são aplicados a cada máquina virtual individual e não ao nível da rede virtual. As consultas de DNS que excedem esses limites são retiradas.