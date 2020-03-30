---
title: Descubra o ponto final do gerenciamento de instâncias gerenciadas
description: Saiba como obter o endereço IP público do ponto de extremidade de gerenciamento de Instância Gerenciada do Banco de Dados SQL do Azure e verificar sua proteção de firewall interno
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825710"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto de extremidade de gerenciamento

O cluster virtual da Instância Gerenciada do Banco de Dados SQL do Azure contém um ponto de extremidade de gerenciamento que a Microsoft usa para operações de gerenciamento. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível da rede e verificação de certificado mútua no nível do aplicativo. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento, mas não pode acessar esse ponto de extremidade.

Para determinar o endereço IP de gerenciamento, faça uma procurar DNS `mi-name.zone_id.database.windows.net`na sua instância gerenciada FQDN: . Isso retornará uma entrada DNS `trx.region-a.worker.vnet.database.windows.net`que é como . Em seguida, você pode fazer uma procurar DNS neste FQDN com ".vnet" removido. Isso devolverá o endereço IP do gerenciamento. 

Este PowerShell fará tudo por você \<se você\> substituir o MI FQDN `mi-name.zone_id.database.windows.net`pela entrada DNS da sua instância gerenciada: :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre Instâncias Gerenciadas e conectividade, veja [Arquitetura de Conectividade da Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-connectivity-architecture.md).
