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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597759"
---
Todos os dados armazenados no Azure Files são criptografados em repouso usando a criptografia do serviço de armazenamento Azure (SSE). A criptografia do serviço de armazenamento funciona de forma semelhante ao BitLocker no Windows: os dados são criptografados abaixo do nível do sistema de arquivos. Como os dados são criptografados abaixo do sistema de arquivos do compartilhamento de arquivos do Azure, como estão codificados em disco, você não precisa ter acesso à chave subjacente no cliente para ler ou gravar para o compartilhamento de arquivos do Azure.

Por padrão, os dados armazenados nos Arquivos Azure são criptografados com chaves gerenciadas pela Microsoft. Com as chaves gerenciadas pela Microsoft, a Microsoft detém as chaves para criptografar/descriptografar os dados e é responsável por rodá-los regularmente. Você também pode optar por gerenciar suas próprias chaves, o que lhe dá controle sobre o processo de rotação. Se você optar por criptografar seus compartilhamentos de arquivos com chaves gerenciadas pelo cliente, o Azure Files está autorizado a acessar suas chaves para atender às solicitações de leitura e gravação de seus clientes. Com chaves gerenciadas pelo cliente, você pode revogar essa autorização a qualquer momento, mas isso significa que o compartilhamento de arquivos do Azure não estará mais acessível via SMB ou pela API FileREST.

O Azure Files usa o mesmo esquema de criptografia que os outros serviços de armazenamento do Azure, como o armazenamento Azure Blob. Para saber mais sobre a criptografia do serviço de armazenamento Azure (SSE), consulte [a criptografia de armazenamento do Azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).