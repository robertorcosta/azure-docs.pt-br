---
title: Bloqueie os níveis de desempenho do armazenamento blob — O armazenamento do Azure
description: Discute a diferença entre os níveis de desempenho premium e padrão para o armazenamento blob de bloco do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270221"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Níveis de desempenho para armazenamento de bolhas de bloco

À medida que as empresas implantam aplicativos nativos de nuvem sensíveis ao desempenho, é importante ter opções para armazenamento de dados econômicos em diferentes níveis de desempenho.

O armazenamento de blob de bloco do Azure oferece dois níveis de desempenho diferentes:

- **Premium**: otimizado para altas taxas de transação e latência de armazenamento consistente de um dígito
- **Padrão**: otimizado para alta capacidade e alta produtividade

As seguintes considerações se aplicam aos diferentes níveis de desempenho:

| Área |Desempenho padrão  |Desempenho premium  |
|---------|---------|---------|
|Disponibilidade de região     |   Todas as regiões      | Em [regiões selecionadas](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Tipos de [conta de armazenamento](../common/storage-account-overview.md#types-of-storage-accounts) suportados     |     Propósito geral v2, BlobStorage, propósito geral v1    |    Armazenamento blockblob     |
|Suporta bolhas de [bloco de alta throughput](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Sim, em tamanhos maiores que 4 MiB PutBlock ou PutBlob     |    Sim, em tamanhos mais de 256 KiB PutBlock ou PutBlob    |
|Redundância     |     Ver [Tipos de contas de armazenamento](../common/storage-account-overview.md#types-of-storage-accounts)   |  Atualmente, suporta apenas o armazenamento localmente redundante (LRS) e o ZRS (Zone-Redudant Storage, armazenamento redudant de zona)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> O ZRS (Zone-redundante Storage, armazenamento redundante por zona) está disponível em regiões selecionadas para contas de armazenamento de blocos de desempenho premium.</div>

Em relação ao custo, o desempenho premium fornece preços otimizados para aplicativos com altas taxas de transação para ajudar a [reduzir o custo total de armazenamento](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) dessas cargas de trabalho.

## <a name="premium-performance"></a>Desempenho premium

O armazenamento de blob de bloco de desempenho premium disponibiliza dados por meio de hardware de alto desempenho. Os dados são armazenados em unidades de estado sólido (SSDs) que são otimizadas para baixa latência. Os SSDs fornecem maior rendimento em comparação com os discos rígidos tradicionais.

O armazenamento de desempenho premium é ideal para cargas de trabalho que requerem tempos de resposta rápidos e consistentes. É melhor para cargas de trabalho que realizam muitas pequenas transações. As cargas de trabalho de exemplo incluem:

- **Cargas de trabalho interativas**. Essas cargas de trabalho exigem atualizações instantâneas e feedback do usuário, como e-commerce e aplicativos de mapeamento. Por exemplo, em um aplicativo de e-commerce, itens menos vistos provavelmente não são armazenados em cache. No entanto, eles devem ser exibidos instantaneamente ao cliente demanda.

- **Análise .** Em um cenário de IoT, muitas operações menores de gravação podem ser empurradas para a nuvem a cada segundo. Grandes quantidades de dados podem ser tomadas, agregadas para fins de análise e, em seguida, excluídas quase imediatamente. Os altos recursos de ingestão de armazenamento premium de blob de bloco tornam-no eficiente para este tipo de carga de trabalho.

- **Inteligência artificial/machine learning (IA/ML)**. A IA/ML lida com o consumo e o processamento de diferentes tipos de dados, como visuais, fala e texto. Esse tipo de carga de trabalho de computação de alto desempenho lida com grandes quantidades de dados que exigem resposta rápida e tempos eficientes de ingestão para análise de dados.

- **Transformação de dados**. Processos que requerem edição constante, modificação e conversão de dados requerem atualizações instantâneas. Para uma representação precisa dos dados, os consumidores desses dados devem ver essas mudanças refletidas imediatamente.

## <a name="standard-performance"></a>Desempenho padrão

O desempenho padrão suporta [diferentes níveis de acesso](storage-blob-storage-tiers.md) para armazenar dados da maneira mais econômica. É otimizado para alta capacidade e alto throughput em grandes conjuntos de dados.

- **Conjuntos de dados de backup e recuperação de desastres**. O armazenamento de desempenho padrão oferece níveis econômicos, tornando-o um caso de uso perfeito para conjuntos de dados de recuperação de desastres de curto e longo prazo, backups secundários e arquivamento de dados de conformidade.

- **Conteúdo de mídia**. Imagens e vídeos geralmente são acessados com freqüência quando criados e armazenados pela primeira vez, mas esse tipo de conteúdo é usado com menos frequência à medida que envelhece. O armazenamento de desempenho padrão oferece níveis adequados para necessidades de conteúdo de mídia. 

- **Processamento de dados em massa**. Esses tipos de cargas de trabalho são adequadas para o armazenamento padrão porque requerem armazenamento de alto desempenho econômico em vez de baixa latência consistente. Grandes conjuntos de dados brutos são encenados para processamento e eventualmente migram para níveis mais frios.

## <a name="migrate-from-standard-to-premium"></a>Migrar do padrão para o premium

Você não pode converter uma conta de armazenamento de desempenho padrão existente em uma conta de armazenamento blob de bloco com desempenho premium. Para migrar para uma conta de armazenamento de desempenho premium, você deve criar uma conta BlockBlobStorage e migrar os dados para a nova conta. Para obter mais informações, consulte [Criar uma conta BlockBlobStorage](storage-blob-create-account-block-blob.md).

Para copiar blobs entre contas de armazenamento, você pode usar a versão mais recente da ferramenta de linha de comando [AzCopy.](../common/storage-use-azcopy-blobs.md) Outras ferramentas, como a Azure Data Factory, também estão disponíveis para movimentação e transformação de dados.

## <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob

O gerenciamento do ciclo de vida do armazenamento Blob oferece uma política rica e baseada em regras:

- **Premium**: Expirar dados no final de seu ciclo de vida.
- **Padrão**: Dados de transição para o melhor nível de acesso e dados expirados no final de seu ciclo de vida.

Para saber mais, consulte [Gerenciar o ciclo de vida do armazenamento Azure Blob](storage-lifecycle-management-concepts.md).

Você não pode mover dados armazenados em uma conta de armazenamento de blocos premium entre níveis quentes, legais e de arquivamento. No entanto, você pode copiar blobs de uma conta de armazenamento blob de bloco para o nível de acesso quente em uma conta *diferente.* Para copiar dados para uma conta diferente, use a API [put block from URL](/rest/api/storageservices/put-block-from-url) ou [AzCopy v10](../common/storage-use-azcopy-v10.md). A **API de colocar bloco de URL** copia sincronizadamente os dados no servidor. A chamada é concluída somente depois que todos os dados são movidos do local original do servidor para o local de destino.

## <a name="next-steps"></a>Próximas etapas

Avalie contas de armazenamento quentes, frias e de arquivamento em contas de armazenamento GPv2 e Blob.

- [Saiba mais sobre a reidratar dados blob do nível de arquivo](storage-blob-rehydration.md)
- [Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)
- [Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
