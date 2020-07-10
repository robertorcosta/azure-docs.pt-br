---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200294"
---
| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório| Observações |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Em|LAN|Sim|Essa porta é usada para se conectar a APIs REST de armazenamento de blog Data Box sobre HTTP. Se não se conectar a APIs REST, isso redirecionará automaticamente para a interface do usuário da Web local em 8443. |
| TCP 443 (HTTPS)|Em|LAN|Sim|Essa porta é usada para se conectar a APIs REST de armazenamento de blog Data Box por HTTPS. Se não se conectar a APIs REST, isso redirecionará automaticamente para a interface do usuário da Web local em 8443. |
| TCP 8443 (HTTPS-Alt)|Em|LAN|Sim|Essa é uma porta alternativa para HTTPS e é usada ao conectar-se à interface do usuário da Web local para o gerenciamento de dispositivos. |
| TCP 445 (SMB)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária somente se você estiver se conectando via SMB. |
| TCP 2049 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária somente se você estiver se conectando via NFS. |
| TCP 111 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta é usada para mapeamento de rpcbind/porta e necessária somente se você estiver se conectando via NFS.  |
