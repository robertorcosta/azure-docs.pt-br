---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895284"
---
O Azure Storage criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para controle adicional sobre chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar para criptografia de dados de blob e arquivo.

As chaves gerenciadas pelo cliente devem ser armazenadas em um Cofre de Chaves Azure. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre criptografia de armazenamento do Azure e gerenciamento de chaves, consulte [a criptografia de armazenamento do Azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md). Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
