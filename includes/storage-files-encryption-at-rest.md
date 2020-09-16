---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563414"
---
Todos os dados armazenados nos Arquivos do Azure são criptografados em repouso usando a SSE (Criptografia do Serviço de Armazenamento) do Azure. A criptografia do serviço de armazenamento funciona de maneira semelhante ao BitLocker no Windows: os dados são criptografados abaixo do nível do sistema de arquivos. Como os dados são criptografados abaixo do sistema de arquivos do compartilhamento de arquivo do Azure, pois eles são codificados no disco, você não precisa ter acesso à chave subjacente no cliente para fazer a leitura ou gravação no compartilhamento de arquivo do Azure. A criptografia em repouso aplica-se aos protocolos SMB e NFS.

Por padrão, os dados armazenados nos Arquivos do Azure são criptografados com as chaves gerenciadas pela Microsoft. Com as chaves gerenciadas pela Microsoft, a Microsoft mantém as chaves para criptografar/descriptografar os dados e é responsável pela rotação regular delas. Você também pode optar por gerenciar as próprias chaves, o que dá controle a você sobre o processo de rotação. Se você optar por criptografar seus compartilhamentos de arquivo com chaves gerenciadas pelo cliente, os Arquivos do Azure terão autorização para acessar suas chaves para atender a solicitações de leitura e gravação de seus clientes. Com as chaves gerenciadas pelo cliente, você pode revogar essa autorização a qualquer momento, mas isso significa que o compartilhamento de arquivo do Azure não estará mais acessível pelo SMB ou pela API FileREST.

Os Arquivos do Azure usam o mesmo esquema de criptografia que os outros serviços de armazenamento do Azure, como o Armazenamento de Blobs do Azure. Para saber mais sobre a SSE (Criptografia do Serviço de Armazenamento) do Azure, confira [Criptografia do armazenamento do Azure para dados em repouso](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).