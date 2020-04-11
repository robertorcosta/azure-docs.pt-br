---
title: Criptografia de armazenamento do Azure para dados em repouso
description: O Azure Storage protege seus dados criptografando-os automaticamente antes de permê-los na nuvem. Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia dos dados em sua conta de armazenamento ou gerenciar criptografia com suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f112a4523bc5af9ecae57e93dfb90795d3fe9c50
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113267"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia de armazenamento do Azure para dados em repouso

O Azure Storage criptografa automaticamente seus dados quando ele é persistido na nuvem. A criptografia do Azure Storage protege seus dados e para ajudá-lo a cumprir seus compromissos de segurança organizacional e conformidade.

## <a name="about-azure-storage-encryption"></a>Sobre a criptografia de armazenamento do Azure

Os dados no Azure Storage são criptografados e descriptografados de forma transparente usando [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

A criptografia do Azure Storage está habilitada para todas as contas de armazenamento, incluindo o Gerenciador de Recursos e contas clássicas de armazenamento. A criptografia de armazenamento do Azure não pode ser desativada. Como seus dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia do Azure Storage.

Os dados em uma conta de armazenamento são criptografados independentemente do nível de desempenho (padrão ou premium), nível de acesso (quente ou legal) ou modelo de implantação (Azure Resource Manager ou clássico). Todas as bolhas na camada de arquivo também são criptografadas. Todas as opções de redundância do Azure Storage suportam criptografia, e todos os dados nas regiões primária e secundária são criptografados quando a replicação geográfica é ativada. Todos os recursos do Azure Storage são criptografados, incluindo blobs, discos, arquivos, filas e tabelas. Todos os metadados do objeto também são criptografados. Não há custo adicional para a criptografia do Azure Storage.

Cada bolha de bloco, bolha de apêndice ou bolha de página que foi escrita no Azure Storage após 20 de outubro de 2017 é criptografada. Blobs criados antes desta data continuam a ser criptografados por um processo em segundo plano. Para forçar a criptografia de uma bolha que foi criada antes de 20 de outubro de 2017, você pode reescrever a bolha. Para saber como verificar o status de criptografia de uma bolha, consulte [Verifique o status de criptografia de uma bolha](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos criptográficos subjacentes à criptografia de armazenamento do Azure, consulte [API de criptografia: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Os dados em uma nova conta de armazenamento são criptografados com chaves gerenciadas pela Microsoft. Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia de seus dados, ou pode gerenciar criptografia com suas próprias chaves. Se você optar por gerenciar criptografia com suas próprias chaves, você tem duas opções:

- Você pode especificar uma *chave gerenciada pelo cliente* com o Azure Key Vault para usar para criptografar e descriptografar dados no armazenamento Blob e em Arquivos Azure. <sup>1,2</sup> Para obter mais informações sobre chaves gerenciadas pelo cliente, consulte [Usar chaves gerenciadas pelo cliente com o Azure Key Vault para gerenciar a criptografia de armazenamento do Azure](encryption-customer-managed-keys.md).
- Você pode especificar uma *chave fornecida pelo cliente* nas operações de armazenamento Blob. Um cliente que faz uma solicitação de leitura ou gravação contra o armazenamento Blob pode incluir uma chave de criptografia na solicitação de controle granular sobre como os dados blob são criptografados e descriptografados. Para obter mais informações sobre as chaves fornecidas pelo cliente, consulte [Fornecer uma chave de criptografia em uma solicitação de armazenamento Blob (visualização)](encryption-customer-provided-keys.md).

A tabela a seguir compara as principais opções de gerenciamento para criptografia de armazenamento do Azure.

|                                        |    Chaves gerenciadas pela Microsoft                             |    Chaves gerenciadas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de criptografia/descriptografia    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de armazenamento azure suportados    |    Todos                                                |    Armazenamento blob, Arquivos Azure<sup>1,2</sup>                                                                                                               |    Armazenamento de blob                                                                  |
|    Armazenamento de chaves                         |    Loja-chave da Microsoft    |    Cofre de Chave do Azure                                                                                                                              |    Azure Key Vault ou qualquer outra loja-chave                                                                 |
|    Responsabilidade de rotação chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Controle de chaves                          |    Microsoft                                     |    Cliente                                                                                                                    |    Cliente                                                                 |

<sup>1</sup> Para obter informações sobre como criar uma conta que suporte usando chaves gerenciadas pelo cliente com o armazenamento queue, consulte [Criar uma conta que suporte chaves gerenciadas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Para obter informações sobre a criação de uma conta que suporte usando chaves gerenciadas pelo cliente com o armazenamento da tabela, consulte [Criar uma conta que suporte chaves gerenciadas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

- [O que é o Cofre da Chave do Azure?](../../key-vault/key-vault-overview.md)
- [Configurar as chaves gerenciadas pelo cliente para a criptografia do Armazenamento do Azure do portal do Azure](storage-encryption-keys-portal.md)
- [Configurar as chaves gerenciadas pelo cliente para a criptografia do Armazenamento do Azure do PowerShell](storage-encryption-keys-powershell.md)
- [Configurar as chaves gerenciadas pelo cliente para a criptografia do Armazenamento do Azure do CLI do Azure](storage-encryption-keys-cli.md)
