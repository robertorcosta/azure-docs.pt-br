---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839759"
---
| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório| Observações |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|No|LAN|Sim|Esta porta é usada para se conectar às APIs rest do Data Box Blog sobre HTTP. Se não estiver se conectando às APIs REST, isso redireciona automaticamente para a UI da Web local sobre 8443. |
| TCP 443 (HTTPS)|No|LAN|Sim|Esta porta é usada para se conectar às APIs rest do Data Box Blog sobre HTTPS. Se não estiver se conectando às APIs REST, isso redireciona automaticamente para a UI da Web local sobre 8443. |
| TCP 8443 (HTTPS-Alt)|No|LAN|Sim|Esta é uma porta alternativa para HTTPS e é usada ao se conectar à ui web local para gerenciamento de dispositivos. |
| TCP 445 (SMB)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta só é necessária se você estiver se conectando via SMB. |
| TCP 2049 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta só é necessária se você estiver se conectando via NFS. |
| TCP 111 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta é usada para mapeamento rpcbind/port e é necessária apenas se você estiver se conectando via NFS.  |
