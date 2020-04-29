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
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597759"
---
Todos os dados armazenados nos arquivos do Azure são criptografados em repouso usando a SSE (criptografia do serviço de armazenamento) do Azure. A criptografia do serviço de armazenamento funciona de forma semelhante ao BitLocker no Windows: os dados são criptografados abaixo do nível do sistema de arquivos. Como os dados são criptografados abaixo do sistema de arquivos do compartilhamento de arquivos do Azure, pois eles são codificados no disco, você não precisa ter acesso à chave subjacente no cliente para ler ou gravar no compartilhamento de arquivos do Azure.

Por padrão, os dados armazenados nos arquivos do Azure são criptografados com chaves gerenciadas pela Microsoft. Com as chaves gerenciadas pela Microsoft, a Microsoft mantém as chaves para criptografar/descriptografar os dados e é responsável por girá-los regularmente. Você também pode optar por gerenciar suas próprias chaves, o que lhe dá controle sobre o processo de rotação. Se você optar por criptografar seus compartilhamentos de arquivos com chaves gerenciadas pelo cliente, os arquivos do Azure serão autorizados a acessar suas chaves para atender a solicitações de leitura e gravação de seus clientes. Com as chaves gerenciadas pelo cliente, você pode revogar essa autorização a qualquer momento, mas isso significa que o compartilhamento de arquivos do Azure não estará mais acessível via SMB ou pela API filerest.

Os arquivos do Azure usam o mesmo esquema de criptografia que os outros serviços de armazenamento do Azure, como o armazenamento de BLOBs do Azure. Para saber mais sobre a SSE (criptografia do serviço de armazenamento) do Azure, confira [criptografia de armazenamento do Azure para dados em repouso](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).