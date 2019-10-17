---
title: Visão geral da versão prévia do Azure HPC Cache
description: Descreve o Azure HPC Cache, uma solução de acelerador de acesso a arquivos para computação de alto desempenho
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: bfbbcd2d7d1bb44e260bedda54ca38ed6860ea67
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254491"
---
# <a name="what-is-azure-hpc-cache-preview"></a>O que é o Azure HPC Cache? (Visualização)

O Azure HPC Cache acelera o acesso a suas tarefas de HPC (computação de alto desempenho). Ao armazenar arquivos em cache no Azure, o Azure HPC Cache traz a escalabilidade da computação em nuvem para seu fluxo de trabalho existente. Esse serviço pode ser usado até mesmo para fluxos de trabalho em que seus dados são armazenados em links WAN, como no ambiente de NAS do datacenter local.

O Azure HPC Cache é fácil de iniciar e monitorar no portal do Azure. O armazenamento de NFS existente ou novos contêineres de blobs podem se tornar parte de seu namespace agregado, o que simplifica o acesso do cliente mesmo quando você altera o destino de armazenamento de back-end.

## <a name="use-cases"></a>Casos de uso

O Azure HPC Cache é mais adequado para aumento de produtividade em fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a arquivos com leitura intensa
* Dados armazenados em um armazenamento acessível por NFS, Blob do Azure ou ambos
* Farms de computação com até 75.000 núcleos de CPU

O Azure HPC Cache pode ser adicionado a uma ampla variedade de fluxos de trabalho em diversos setores. Qualquer sistema em que um grande número de máquinas precisa acessar um conjunto de arquivos em escala e com baixa latência se beneficiará desse serviço. As seções a seguir fornecem exemplos específicos.

### <a name="visual-effects-vfx-rendering"></a>Renderização VFX (de efeitos visuais)

Em mídia e entretenimento, o Azure HPC Cache pode acelerar o acesso a dados em projetos de renderização com limitações de tempo. Fluxos de trabalho de renderização VFX geralmente exigem processamento de último minuto por um grande número de nós de computação. Os dados desses fluxos de trabalho normalmente estão localizados em um ambiente NAS local. O Azure HPC Cache pode armazenar em cache esses dados de arquivo na nuvem para reduzir a latência e aumentar a flexibilidade para renderização sob demanda.

### <a name="life-sciences"></a>Ciências da vida

Muitos fluxos de trabalho de ciências biológicas podem se beneficiar do cache de arquivos de expansão.

Um instituto de pesquisa que deseja portar seus fluxos de trabalho de análise de genoma para o Azure pode deslocá-los facilmente usando o Azure HPC Cache. Como o cache oferece acesso de arquivo POSIX, nenhuma alteração do lado do cliente é necessária para executar seu fluxo de trabalho de cliente existente na nuvem.

O Azure HPC Cache também pode ser aproveitado para aumentar a eficiência em tarefas como análise secundária, simulação farmacológica ou análise de imagens orientada por IA.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Uma implantação do Azure HPC Cache pode ajudar a acelerar cálculos de análise quantitativa, cargas de trabalho de análise de risco e simulações Monte Carlo para fornecer a empresas de serviços financeiros melhores insights para tomar decisões estratégicas.

## <a name="region-availability"></a>Disponibilidade de região

O Azure HPC Cache está disponível nessas regiões do Azure:

* Leste dos EUA
* Leste dos EUA 2
* Norte da Europa
* Europa Ocidental
* Sudeste Asiático
* Oeste dos EUA 2

Verifique a [página do produto do Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) para obter as informações de disponibilidade mais recentes.

## <a name="preview-availability"></a>Disponibilidade da versão prévia

A versão prévia pública do Azure HPC Cache é restrita para garantir a qualidade do serviço. Solicite acesso preenchendo [este formulário](https://aka.ms/onboard-hpc-cache). Depois que sua assinatura for adicionada à lista de acesso, será possível criar caches de teste.

## <a name="next-steps"></a>Próximas etapas

* Leia a [página do produto do Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) para saber mais sobre seus recursos
* Saiba mais sobre os [pré-requisitos](hpc-cache-prereqs.md) do produto
* [Crie um Azure HPC Cache](hpc-cache-create.md) no portal do Azure
