---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89082872"
---
| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório|   Observações |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Saída|WAN |Não|A porta de saída é usada para acesso à internet para recuperar atualizações. <br>O proxy Web de saída é configurável pelo usuário. |
| TCP 443 (HTTPS)|Saída|WAN|Sim|A porta de saída é usada para acessar dados na nuvem.<br>O proxy Web de saída é configurável pelo usuário.|
| UDP 123 (NTP)|Saída|WAN|Em alguns casos<br>Consulte as observações|Esta porta só será necessária se você estiver usando um servidor NTP baseado na internet.  |   
| UDP 53 (DNS)|Saída|WAN|Em alguns casos<br>Consulte as observações|Esta porta só será necessária se você estiver usando um servidor DNS baseado na internet.<br>É recomendado usar um servidor DNS local. |
| TCP 5985 (WinRM)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta é necessária para se conectar ao dispositivo por meio do PowerShell remoto via HTTP.  |
| UDP 67 (DHCP)|Saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver usando um servidor DHCP local.  |
| TCP 80 (HTTP)|Entrada/saída|LAN|Sim|Essa porta é a porta de entrada da interface do usuário local no dispositivo para gerenciamento local. <br>O acesso à interface do usuário local por HTTP será redirecionado automaticamente para HTTPS.  |
| TCP 443 (HTTPS)|Entrada/saída|LAN|Sim|Essa porta é a porta de entrada da interface do usuário local no dispositivo para gerenciamento local. Essa porta também é usada para conectar Azure Resource Manager às APIs locais do dispositivo, para conectar o armazenamento de BLOBs por meio de APIs REST e para o serviço de token de segurança (STS) para autenticar por meio de tokens de acesso e de atualização.|
| TCP 445 (SMB)|Em|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver se conectando via SMB. |
| TCP 2049 (NFS)|Em|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver se conectando via NFS. |


