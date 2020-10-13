---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 866640d90c66dd82e8be61d221bc903907575454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91641880"
---
Durante a visualização, o NFS tem as seguintes limitações:

- O NFS 4,1 atualmente dá suporte apenas aos recursos obrigatórios da [especificação de protocolo](https://tools.ietf.org/html/rfc5661). Recursos opcionais, como delegações e retorno de chamada de todos os tipos, atualizações de bloqueio e Downgrades e autenticação e criptografia Kerberos não têm suporte.
- Se a maioria de suas solicitações for centrada em metadados, a latência será pior quando comparada com as operações de leitura/gravação/atualização.
- Deve criar uma nova conta de armazenamento para criar um compartilhamento NFS.
- Somente as APIs REST do plano de gerenciamento têm suporte. As APIs REST do plano de dados não estão disponíveis, o que significa que ferramentas como o Gerenciador de armazenamento não funcionarão com compartilhamentos NFS, nem será possível procurar dados de compartilhamento NFS no portal do Azure.
- Disponível somente para a camada Premium.
- Atualmente, disponível apenas com LRS (armazenamento com redundância local).

### <a name="azure-storage-features-not-yet-supported"></a>Recursos de armazenamento do Azure ainda não suportados

Além disso, os seguintes recursos de arquivos do Azure não estão disponíveis com compartilhamentos NFS:

- Autenticação baseada em identidade
- Suporte de Backup do Azure
- Instantâneos
- Exclusão reversível
- Suporte completo para criptografia-em trânsito (para obter detalhes, consulte [segurança de NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Sincronização de Arquivos do Azure (disponível somente para clientes Windows, aos quais o NFS 4,1 não oferece suporte)