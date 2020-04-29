---
title: Criptografia de armazenamento do Azure para dados em repouso
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes de mantê-los para a nuvem. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia dos dados em sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457936"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia de armazenamento do Azure para dados em repouso

O armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos para a nuvem. A criptografia de armazenamento do Azure protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

## <a name="about-azure-storage-encryption"></a>Sobre a criptografia de armazenamento do Azure

Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

A criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento, incluindo as contas do Resource Manager e do armazenamento clássico. A criptografia de armazenamento do Azure não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure.

Os dados em uma conta de armazenamento são criptografados independentemente do nível de desempenho (Standard ou Premium), camada de acesso (quente ou frio) ou modelo de implantação (Azure Resource Manager ou clássico). Todos os BLOBs na camada de arquivo também são criptografados. Todas as opções de redundância de armazenamento do Azure dão suporte à criptografia e todos os dados nas regiões primária e secundária são criptografados quando a replicação geográfica está habilitada. Todos os recursos de armazenamento do Azure são criptografados, incluindo BLOBs, discos, arquivos, filas e tabelas. Todos os metadados de objeto também são criptografados. Não há nenhum custo adicional para a criptografia de armazenamento do Azure.

Todos os blobs de blocos, BLOB de acréscimo ou BLOB de páginas que foram gravados no armazenamento do Azure após 20 de outubro de 2017 são criptografados. Os BLOBs criados antes dessa data continuam a ser criptografados por um processo em segundo plano. Para forçar a criptografia de um blob que foi criado antes de 20 de outubro de 2017, você pode reescrever o blob. Para saber como verificar o status de criptografia de um blob, consulte [verificar o status de criptografia de um blob](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos de criptografia subjacentes à criptografia de armazenamento do Azure, consulte [API de criptografia: próxima geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Os dados em uma nova conta de armazenamento são criptografados com chaves gerenciadas pela Microsoft. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados ou pode gerenciar a criptografia com suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, terá duas opções:

- Você pode especificar uma *chave gerenciada pelo cliente* com Azure Key Vault a ser usada para criptografar e descriptografar dados no armazenamento de BLOBs e em arquivos do Azure. <sup>1, 2</sup> para obter mais informações sobre chaves gerenciadas pelo cliente, consulte [usar chaves gerenciadas pelo cliente com Azure Key Vault para gerenciar a criptografia de armazenamento do Azure](encryption-customer-managed-keys.md).
- Você pode especificar uma *chave fornecida pelo cliente* em operações de armazenamento de BLOBs. Um cliente que faz uma solicitação de leitura ou gravação no armazenamento de blob pode incluir uma chave de criptografia na solicitação de controle granular sobre como os dados de blob são criptografados e descriptografados. Para obter mais informações sobre chaves fornecidas pelo cliente, consulte [fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs (versão prévia)](encryption-customer-provided-keys.md).

A tabela a seguir compara as principais opções de gerenciamento de criptografia do armazenamento do Azure.

|                                        |    Chaves gerenciadas pela Microsoft                             |    Chaves gerenciadas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de criptografia/descriptografia    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de armazenamento do Azure com suporte    |    Todos                                                |    Armazenamento de BLOBs, arquivos do Azure<sup>1, 2</sup>                                                                                                               |    Armazenamento de Blobs                                                                  |
|    Armazenamento de chaves                         |    Repositório de chaves da Microsoft    |    Cofre de Chave do Azure                                                                                                                              |    Repositório de chaves próprio do cliente                                                                 |
|    Responsabilidade de rotação de chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Controle de chave                          |    Microsoft                                     |    Cliente                                                                                                                    |    Cliente                                                                 |

<sup>1</sup> para obter informações sobre como criar uma conta que ofereça suporte ao uso de chaves gerenciadas pelo cliente com o armazenamento de filas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> para obter informações sobre como criar uma conta que ofereça suporte ao uso de chaves gerenciadas pelo cliente com o armazenamento de tabelas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

- [O que é o Azure Key Vault?](../../key-vault/general/overview.md)
- [Configurar as chaves gerenciadas pelo cliente para a criptografia do Armazenamento do Azure do portal do Azure](storage-encryption-keys-portal.md)
- [Configurar as chaves gerenciadas pelo cliente para a criptografia do Armazenamento do Azure do PowerShell](storage-encryption-keys-powershell.md)
- [Configurar as chaves gerenciadas pelo cliente para a criptografia do Armazenamento do Azure do CLI do Azure](storage-encryption-keys-cli.md)
