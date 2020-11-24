---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b8a72640ebe8daffedb196456df7d40bc41b7b1b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553820"
---
O Armazenamento do Azure oferece diversos tipos de contas de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o melhor tipo de conta para seus aplicativos. Os tipos de conta de armazenamento são:

- **Contas para uso geral v2**: tipo de conta de armazenamento básico para blobs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários que usam o Armazenamento do Azure.
- **Contas para uso geral v1**: tipo de conta herdada para blobs, arquivos, filas e tabelas. Use contas de uso geral v2 em vez desta opção, quando possível.
- **Contas de BlockBlobStorage**: contas de armazenamento com características de desempenho premium para blobs de blocos e blobs de acréscimo. Recomendadas para cenários com altas taxas de transações ou cenários que usam objetos menores ou exigem latência de armazenamento consistentemente baixa.
- **Contas de FileStorage**: contas de armazenamento somente para arquivos com características de desempenho premium. Recomendadas para aplicações de escala empresarial ou de alto desempenho.
- **Contas de BlobStorage**: contas de armazenamento somente em blobs herdadas. Use contas de uso geral v2 em vez desta opção, quando possível.

A tabela a seguir descreve os tipos de conta de armazenamento e suas funcionalidades:

| Tipo de conta de armazenamento | Serviços com suporte                       | Níveis de desempenho compatíveis      | Camadas de acesso compatíveis         | Opções de replicação               | Modelo de implantação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Criptografia<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral V2   | Blob, Arquivo, Fila, Tabela, Disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frequente, Esporádico, Arquivos<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (versão prévia), RA-GZRS (versão prévia)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Gerenciador de Recursos             | Criptografado              |
| Uso geral V1   | Blob, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, Clássico    | Criptografado              |
| BlockBlobStorage   | Blob (somente blobs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Gerenciador de Recursos             | Criptografado              |
| FileStorage   | Somente arquivo | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Gerenciador de Recursos             | Criptografado              |
| BlobStorage         | Blob (somente blobs de blocos e blobs de acréscimo) | Standard                      | Frequente, Esporádico, Arquivos<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Gerenciador de Recursos             | Criptografado              |

<div id="deployment-model"><sup>1</sup>É recomendável usar o modelo de implantação do Azure Resource Manager. Ainda é possível criar contas de armazenamento que usam o modelo de implantação clássico em alguns locais, e as contas clássicas existentes ainda têm suporte. Para saber mais, confira <a href="/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos</a>.</div><br/>

<div id="encryption"><sup>2</sup>Todas as contas de armazenamento são criptografadas usando a SSE (Criptografia do Serviço de Armazenamento) para dados em repouso. Para obter mais informações, consulte <a href="/azure/storage/common/storage-service-encryption">Criptografia de serviço do Armazenamento do Azure para dados em repouso</a>.</div><br/>

<div id="archive"><sup>3</sup> O armazenamento de Arquivos e as camadas no nível do blob só oferecem suporte aos blobs de bloco. A camada de Arquivos está disponível somente no nível de blob individual, e não no nível da conta de armazenamento. Para obter mais informações, consulte <a href="/azure/storage/blobs/storage-blob-storage-tiers">Armazenamento de Blobs do Azure: camadas de armazenamento de Arquivos, Frequente e Esporádico</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> O ZRS (armazenamento com redundância de zona) e o GZRS/RA-GZRS (armazenamento com redundância de zona geográfica) (versão prévia) estão disponíveis somente para contas de armazenamento padrão de uso geral v2, de BlockBlobStorage e de FileStorage em determinadas regiões. Para obter mais informações sobre as opções de redundância do Armazenamento do Azure, confira <a href="/azure/storage/common/storage-redundancy">Redundância no Armazenamento do Azure</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> O desempenho premium para as contas de uso geral v1 e v2 está disponível somente para o blob de páginas e de disco. O desempenho premium para blobs de blocos ou de acréscimo está disponível apenas para contas de BlockBlobStorage. O desempenho premium para arquivos está disponível apenas para contas de FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> O Azure Data Lake Storage Gen2 é um conjunto de funcionalidades dedicadas à análise de Big Data, criado com base no Armazenamento de Blobs do Azure. O Data Lake Storage Gen2 tem suporte apenas em contas de armazenamento de uso geral v2 com namespace hierárquico habilitado. Para obter mais informações sobre o Data Lake Storage Gen2, confira <a href="/azure/storage/blobs/data-lake-storage-introduction">Introdução ao Azure Data Lake Storage Gen2</a>.</div>