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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895284"
---
O armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar para criptografia de dados de BLOB e arquivo.

As chaves gerenciadas pelo cliente devem ser armazenadas em um Azure Key Vault. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre criptografia de armazenamento do Azure e gerenciamento de chaves, consulte [criptografia de armazenamento do Azure para dados em repouso](../articles/storage/common/storage-service-encryption.md). Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
