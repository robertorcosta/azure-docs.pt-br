---
title: Forneça uma chave de criptografia em uma solicitação de armazenamento Blob
titleSuffix: Azure Storage
description: Os clientes que fazem solicitações contra o armazenamento Azure Blob têm a opção de fornecer uma chave de criptografia por solicitação (visualização). A inclusão da chave de criptografia na solicitação fornece controle granular sobre as configurações de criptografia para operações de armazenamento Blob.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410058"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Forneça uma chave de criptografia em uma solicitação de armazenamento Blob (visualização)

Os clientes que fazem solicitações contra o armazenamento Azure Blob têm a opção de fornecer uma chave de criptografia por solicitação (visualização). A inclusão da chave de criptografia na solicitação fornece controle granular sobre as configurações de criptografia para operações de armazenamento Blob. As chaves fornecidas pelo cliente podem ser armazenadas no Azure Key Vault ou em outra loja-chave.

## <a name="encrypting-read-and-write-operations"></a>Criptografando operações de leitura e gravação

Quando um aplicativo cliente fornece uma chave de criptografia na solicitação, o Azure Storage executa criptografia e descriptografia de forma transparente durante a leitura e escrita de dados blob. O Azure Storage grava um hash SHA-256 da chave de criptografia ao lado do conteúdo da bolha. O hash é usado para verificar se todas as operações subseqüentes contra a bolha usam a mesma chave de criptografia.

O Azure Storage não armazena ou gerencia a chave de criptografia que o cliente envia com a solicitação. A chave é descartada com segurança assim que o processo de criptografia ou descriptografia estiver concluído.

Quando um cliente cria ou atualiza uma bolha usando uma chave fornecida pelo cliente na solicitação, as solicitações subsequentes de leitura e gravação para essa bolha também devem fornecer a chave. Se a chave não for fornecida em uma solicitação de uma bolha que já tenha sido criptografada com uma chave fornecida pelo cliente, a solicitação falhará com o código de erro 409 (Conflito).

Se o aplicativo cliente enviar uma chave de criptografia na solicitação e a conta de armazenamento também for criptografada usando uma chave gerenciada pela Microsoft ou uma chave gerenciada pelo cliente, o Azure Storage usará a chave fornecida na solicitação de criptografia e descriptografia.

Para enviar a chave de criptografia como parte da solicitação, um cliente deve estabelecer uma conexão segura ao Armazenamento Azure usando HTTPS.

Cada instantâneo blob pode ter sua própria chave de criptografia.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Solicitar cabeçalhos para especificar chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para passar com segurança informações de chave de criptografia em uma solicitação de armazenamento Blob:

|Cabeçalho da Solicitação | Descrição |
|---------------|-------------|
|`x-ms-encryption-key` |Necessário tanto para escrever como para ler solicitações. Um valor-chave de criptografia AES-256 codificado pela Base64. |
|`x-ms-encryption-key-sha256`| Necessário tanto para escrever como para ler solicitações. O BASE64-codificado SHA256 da chave de criptografia. |
|`x-ms-encryption-algorithm` | Necessário para solicitações de gravação, opcional para solicitações de leitura. Especifica o algoritmo a ser usado ao criptografar dados usando a tecla dada. Deve ser AES256. |

A especificação das chaves de criptografia na solicitação é opcional. No entanto, se você especificar um dos cabeçalhos listados acima para uma operação de gravação, então você deve especificar todos eles.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento Blob que suportam chaves fornecidas pelo cliente

As seguintes operações de armazenamento Blob suportam o envio de chaves de criptografia fornecidas pelo cliente por uma solicitação:

- [Colocar Blob](/rest/api/storageservices/put-blob)
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list)
- [Colocar bloco](/rest/api/storageservices/put-block)
- [Colocar bloquear da URL](/rest/api/storageservices/put-block-from-url)
- [Colocar Página](/rest/api/storageservices/put-page)
- [Colocar página da URL](/rest/api/storageservices/put-page-from-url)
- [Acrescentar Bloco](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Definir Metadados de Blob](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Blob instantâneo](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Gire as chaves fornecidas pelo cliente

Para girar uma chave de criptografia que foi usada para criptografar uma bolha, baixe a bolha e, em seguida, recarregue-a com a nova chave de criptografia.

> [!IMPORTANT]
> O portal Azure não pode ser usado para ler ou gravar em um contêiner ou bolha que esteja criptografado com uma chave fornecida na solicitação.
>
> Certifique-se de proteger a chave de criptografia que você fornece em uma solicitação de armazenamento Blob em uma loja de chaves segura como o Azure Key Vault. Se você tentar uma operação de gravação em um contêiner ou bolha sem a chave de criptografia, a operação falhará e você perderá o acesso ao objeto.

## <a name="next-steps"></a>Próximas etapas

- [Especifique uma chave fornecida pelo cliente em uma solicitação de armazenamento Blob com .NET](../blobs/storage-blob-customer-provided-key.md)
- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
