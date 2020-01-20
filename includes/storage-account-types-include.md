---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6e8c0e1c7fef884844b8aaae9dc4c7e3eaa220a2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274542"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o melhor tipo de conta para seus aplicativos. Os tipos de conta de armazenamento são:

- **Contas de uso geral v2**: tipo de conta de armazenamento básico para BLOBs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários que usam o Armazenamento do Azure.
- **Contas de uso geral v1**: tipo de conta herdada para BLOBs, arquivos, filas e tabelas. Use contas de uso geral v2 em vez desta opção, quando possível.
- **Contas do BlockBlobStorage**: contas de armazenamento com características de desempenho premium para BLOBs de bloco e blobs de acréscimo. Recomendado para cenários com altas taxas de transações ou cenários que usam objetos menores ou exigem latência de armazenamento consistentemente baixa.
- **Contas de armazenamento**de arquivo: contas de armazenamento somente de arquivos com características de desempenho premium. Recomendado para aplicativos de escala empresarial ou de alto desempenho.
- **Contas BlobStorage**: contas de armazenamento somente blob herdadas. Use contas de uso geral v2 em vez desta opção, quando possível.

A tabela a seguir descreve os tipos de conta de armazenamento e suas funcionalidades:

| Tipo de conta de armazenamento | Serviços com suporte                       | Níveis de desempenho compatíveis      | Camadas de acesso compatíveis         | Opções de replicação               | Modelo de implantação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Criptografia<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral V2   | BLOB, arquivo, fila, tabela, disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (visualização), RA-GZRS (versão prévia)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Criptografado              |
| Uso geral V1   | Blob, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, Clássico    | Criptografado              |
| BlockBlobStorage   | Blob (somente blobs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Criptografado              |
| Armazenamento de   | Somente arquivo | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Criptografado              |
| BlobStorage         | Blob (somente blobs de blocos e blobs de acréscimo) | Padrão                      | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Criptografado              |

<div id="deployment-model"><sup>1</sup>É recomendável usar o modelo de implantação do Azure Resource Manager. Ainda é possível criar contas de armazenamento que usam o modelo de implantação clássico em alguns locais, e as contas clássicas existentes ainda têm suporte. Para saber mais, confira <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos</a>.</div>

<div id="encryption"><sup>2</sup>Todas as contas de armazenamento são criptografadas usando a SSE (Criptografia do Serviço de Armazenamento) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Criptografia de serviço do Armazenamento do Azure para dados em repouso</a>.</div>

<div id="archive"><sup>3</sup>A camada de acesso aos arquivos está disponível somente no nível de blob individual, e não no nível da conta de armazenamento. Somente os blobs de bloco e de acréscimo podem ser arquivados. Para saber mais, confira <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Armazenamento de Blobs do Azure: camadas de armazenamento quente, frio e de arquivos</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> O ZRS (armazenamento com redundância de zona) e o armazenamento com redundância de zona geográfica (GZRS/RA-GZRS) (versão prévia) estão disponíveis somente para contas de armazenamento de, BlockBlobStorage e de uso geral padrão em determinadas regiões. Para saber mais sobre o ZRS, confira <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">ZRS (armazenamento com redundância de zona): aplicativos do Armazenamento do Azure altamente disponíveis</a>. Para obter mais informações sobre GZRS/RA-GZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)</a>. Para saber mais sobre outras opções de replicação, confira <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Replicação do Armazenamento do Azure</a>.</div>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas de uso geral V2 e V1 de finalidade geral está disponível somente para blob de disco e de página. O desempenho premium para BLOBs de bloco ou de acréscimo só está disponível em contas BlockBlobStorage. O desempenho premium para arquivos só está disponível em contas de armazenamento de arquivo.</div>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no armazenamento de BLOBs do Azure. Só há suporte para Data Lake Storage Gen2 em contas de armazenamento v2 de uso geral com namespace hierárquico habilitado. Para obter mais informações sobre Data Lake Storage Gen2, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">introdução ao Azure data Lake Storage Gen2</a>.</div>
