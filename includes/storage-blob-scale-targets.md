---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
ms.openlocfilehash: 43a72d915fa5a00c54bef7a06fe3815a7d63f2fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805599"
---
| Recurso | Destino | Destino (versão prévia) |
|-|-|-|
| Tamanho máximo do contêiner de blob único | Igual à capacidade máxima da conta de armazenamento |  |
| Número máximo de blocos em um blob de blocos ou BLOB de acréscimo | 50.000 blocos |  |
| Tamanho máximo de um bloco em um blob de blocos | 100 MiB | 4000 MiB (versão prévia) |
| Tamanho máximo de um blob de blocos | 50.000 X 100 MiB (aproximadamente 4,75 TiB) | 50.000 X 4000 MiB (aproximadamente 190,7 TiB) (visualização) |
| Tamanho máximo de um bloco em um blob de acréscimo | 4 MiB |  |
| Tamanho máximo de um blob de acréscimo | 50.000 x 4 MiB (aproximadamente 195 GiB) |  |
| Tamanho máximo de um blob de páginas | 8 TiB |  |
| Número máximo de políticas de acesso armazenadas por contêiner de BLOB | 5 |  |
| Taxa de solicitação de destino para um único blob | Até 500 solicitações por segundo |  |
| Taxa de transferência de destino para um blob de página única | Até 60 MiB por segundo |  |
| Taxa de transferência de destino para um único blob de blocos | Até os limites de entrada/saída da conta de armazenamento<sup>1</sup> |  |

<sup>1</sup> a taxa de transferência para um único blob depende de vários fatores, incluindo, mas não se limitando a: simultaneidade, tamanho da solicitação, nível de desempenho, velocidade da origem para carregamentos e destino para downloads. Para aproveitar os aprimoramentos de desempenho de [blobs de blocos de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), carregue BLOBs ou blocos maiores. Especificamente, chame a operação [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) com um tamanho de bloco ou BLOB maior que 4 MIB para contas de armazenamento padrão. Para o blob de blocos Premium ou para contas de armazenamento Data Lake Storage Gen2, use um tamanho de bloco ou BLOB maior que 256 KiB.

A tabela a seguir descreve os tamanhos máximos de bloco e BLOB permitidos pela versão do serviço.

| Versão do serviço | Tamanho máximo do bloco (via bloco put) | Tamanho máximo do blob (por meio da lista de blocos put) | Tamanho máximo do blob por meio de uma única operação de gravação (via Put Blob) |
|-|-|-|-|
| Versão 2019-12-12 e posterior | 4000 MiB (versão prévia) | Aproximadamente 190,7 TiB (4000 MiB X 50.000 blocos) (visualização) | 5000 MiB (versão prévia) |
| Versão 2016-05-31 até a versão 2019-07-07 | 100 MiB | Aproximadamente 4,75 TiB (100 MiB X 50.000 blocos) | 256 MiB |
| Versões anteriores a 2016-05-31 | 4 MiB | Aproximadamente 195 GiB (4 MiB X 50.000 blocos) | 64 MiB |
