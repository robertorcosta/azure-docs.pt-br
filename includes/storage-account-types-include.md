---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371538"
---
O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o melhor tipo de conta para seus aplicativos. Os tipos de conta de armazenamento são:

- **Contas v2 de uso geral**: Tipo básico de conta de armazenamento para blobs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários que usam o Armazenamento do Azure.
- **Contas v1 de uso geral**: Tipo de conta legado para blobs, arquivos, filas e tabelas. Use contas de uso geral v2 em vez desta opção, quando possível.
- **Contas blockBlobArmazenamento :** Contas de armazenamento com características de desempenho premium para blobs de bloco e blobs de apêndice. Recomendado para cenários com altas taxas de transações ou cenários que usam objetos menores ou requerem latência de armazenamento consistentemente baixa.
- **Contas de armazenamento de arquivos**: contas de armazenamento somente em arquivos com características de desempenho premium. Recomendado para aplicações corporativas ou de alta escala de desempenho.
- **Contas BlobStorage**: Contas de armazenamento somente blob legado. Use contas de uso geral v2 em vez desta opção, quando possível.

A tabela a seguir descreve os tipos de conta de armazenamento e suas funcionalidades:

| Tipo de conta de armazenamento | Serviços com suporte                       | Níveis de desempenho compatíveis      | Camadas de acesso compatíveis         | Opções de replicação               | Modelo de implantação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Criptografia<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral V2   | Blob, File, Fila, Tabela, Disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, legal, arquivo<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (pré-visualização), RA-GZRS (pré-visualização)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Gerenciador de Recursos             | Criptografado              |
| Uso geral V1   | Blob, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, Clássico    | Criptografado              |
| Armazenamento blockblob   | Blob (somente blobs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Gerenciador de Recursos             | Criptografado              |
| Armazenamento de arquivos   | Somente arquivo | Premium                       | N/D                            | LRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Gerenciador de Recursos             | Criptografado              |
| BlobStorage         | Blob (somente blobs de blocos e blobs de acréscimo) | Standard                      | Quente, legal, arquivo<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Gerenciador de Recursos             | Criptografado              |

<div id="deployment-model"><sup>1</sup>É recomendável usar o modelo de implantação do Azure Resource Manager. Ainda é possível criar contas de armazenamento que usam o modelo de implantação clássico em alguns locais, e as contas clássicas existentes ainda têm suporte. Para saber mais, confira <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos</a>.</div><br/>

<div id="encryption"><sup>2</sup>Todas as contas de armazenamento são criptografadas usando a SSE (Criptografia do Serviço de Armazenamento) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Criptografia de serviço do Armazenamento do Azure para dados em repouso</a>.</div><br/>

<div id="archive"><sup>3</sup> O armazenamento de arquivos e a hierarquiçação em nível de bolha só suportam bolhas de bloco. O nível Do Arquivo está disponível apenas no nível de uma bolha individual, não no nível da conta de armazenamento. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">o armazenamento Azure Blob: níveis de armazenamento hot, cool e archive</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> O armazenamento redundante de zona (ZRS) e o armazenamento redundante em geozona (GZRS/RA-GZRS) (visualização) estão disponíveis apenas para contas padrão de uso geral V2, BlockBlobStorage e FileStorage em determinadas regiões. Para obter mais informações sobre as opções de redundância do Azure Storage, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">a redundância do Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas v2 e v1 de uso geral está disponível apenas para disco e blob de página. O desempenho premium para blobs de bloco ou apêndice só está disponível em contas de armazenamento blockblob. O desempenho premium para arquivos só está disponível em contas de armazenamento de arquivos.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de big data, construído no armazenamento Azure Blob. Data Lake Storage Gen2 só é suportado em contas de armazenamento V2 de uso geral com namespace hierárquico ativado. Para obter mais informações sobre data lake storage Gen2, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Introdução ao Azure Data Lake Storage Gen2</a>.</div>
