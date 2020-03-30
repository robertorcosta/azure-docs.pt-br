---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361525"
---
### <a name="database-tier"></a>Camada de banco de dados

O nível banco de dados contém as instâncias do banco de dados para o aplicativo. O banco de dados pode ser um sistema Oracle DB, Oracle RAC ou Oracle Exadata Database. 

Se a escolha for usar o Oracle DB, a instância do banco de dados pode ser implantada no Azure através das imagens Oracle DB disponíveis no Azure Marketplace. Alternativamente, você pode usar a interconexão entre o Azure e o OCI para implantar o Oracle DB em um modelo PaaS no OCI.

Para oracle RAC, você pode implantar o Oracle RAC no Azure CloudSimple no modelo IaaS ou no OCI no modelo PaaS. Recomenda-se que você use um sistema RAC de dois nós. 

Finalmente, para sistemas Exadata, use a interconexão OCI e implante o sistema Exadata em OCI. O diagrama de arquitetura anterior acima mostra um sistema Exadata implantado no OCI em duas sub-redes.

Para cenários de produção, implante várias instâncias do banco de dados em duas zonas de disponibilidade (se estiver implantando no Azure) ou em dois domínios de disponibilidade (em OCI). Use o Oracle Active Data Guard para sincronizar os bancos de dados primários e em espera.

O nível de banco de dados só recebe solicitações do nível intermediário. Recomenda-se que você configure um grupo de segurança de rede (lista de segurança se implantar o banco de dados no OCI) para permitir apenas solicitações na porta 1521 do nível médio e na porta 22 do servidor bastião por razões administrativas.

Para bancos de dados implantados no OCI, uma rede de nuvem virtual separada deve ser configurada com um gateway de roteamento dinâmico (DRG) conectado ao circuito FastConnect.