---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67839759"
---
| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório| Anotações |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|No|LAN|Sim|Essa porta é usada para se conectar a APIs REST de armazenamento de blog Data Box sobre HTTP. Se não se conectar a APIs REST, isso redirecionará automaticamente para a interface do usuário da Web local em 8443. |
| TCP 443 (HTTPS)|No|LAN|Sim|Essa porta é usada para se conectar a APIs REST de armazenamento de blog Data Box por HTTPS. Se não se conectar a APIs REST, isso redirecionará automaticamente para a interface do usuário da Web local em 8443. |
| TCP 8443 (HTTPS-Alt)|No|LAN|Sim|Essa é uma porta alternativa para HTTPS e é usada ao conectar-se à interface do usuário da Web local para o gerenciamento de dispositivos. |
| TCP 445 (SMB)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária somente se você estiver se conectando via SMB. |
| TCP 2049 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária somente se você estiver se conectando via NFS. |
| TCP 111 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta é usada para mapeamento de rpcbind/porta e necessária somente se você estiver se conectando via NFS.  |
