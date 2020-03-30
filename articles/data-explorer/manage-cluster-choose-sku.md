---
title: Selecione o SKU VM correto para o cluster Azure Data Explorer
description: Este artigo descreve como selecionar o tamanho Ideal do SKU para o cluster Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561843"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selecione o VM SKU correto para o cluster Azure Data Explorer 

Quando você cria um novo cluster ou otimiza um cluster para uma carga de trabalho em mudança, o Azure Data Explorer oferece várias SKUs de máquina virtual (VM) para escolher. As VMs foram cuidadosamente escolhidas para lhe dar o custo ideal para qualquer carga de trabalho. 

O tamanho e o SKU VM do cluster de gerenciamento de dados são totalmente gerenciados pelo serviço Azure Data Explorer. Eles são determinados por fatores como o tamanho da VM do motor e a carga de trabalho de ingestão. 

Você pode alterar o VM SKU para o cluster do motor a qualquer [momento, aumentando o cluster](manage-cluster-vertical-scaling.md). É melhor começar com o menor tamanho SKU que se encaixa no cenário inicial. Tenha em mente que a escalação do cluster resulta em um tempo de inatividade de até 30 minutos enquanto o cluster é recriado com o novo VM SKU.

> [!TIP]
> O RI [(Compute Reserved Instances, instâncias reservadas)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) é aplicável ao cluster Azure Data Explorer.  

Este artigo descreve várias opções de VM SKU e fornece os detalhes técnicos que podem ajudá-lo a fazer a melhor escolha.

## <a name="select-a-cluster-type"></a>Selecione um tipo de cluster

O Azure Data Explorer oferece dois tipos de clusters:

* **Produção**: Os clusters de produção contêm dois nós para clusters de gerenciamento de dados e de motor e são operados o Azure Data Explorer [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/).

* **Dev/Teste (sem SLA)**: Os clusters de dev/teste têm um único nó D11 v2 para o cluster do motor e um único nó D1 para o cluster de gerenciamento de dados. Este tipo de cluster é a configuração de menor custo devido à sua baixa contagem de instâncias e sem carga de marcação do motor. Não há SLA para esta configuração de cluster, porque falta redundância.

## <a name="sku-types"></a>Tipos de SKU

Ao criar um cluster Azure Data Explorer, selecione o SKU VM *ideal* para a carga de trabalho planejada. Você pode escolher entre as duas famílias SKU do Azure Data Explorer:

* **D v2**: O D SKU é otimizado para computação e vem em dois sabores:
    * A VM propriamente dita
    * A VM empacotada com discos de armazenamento premium

* **LS**: O L SKU é otimizado para armazenamento. Ele tem um tamanho DeSD muito maior do que o SKU de preço similar.

As principais diferenças entre os tipos de SKU disponíveis são descritas na tabela a seguir:
 
| Atributo | D SKU | L SKU |
|---|---|---
|**Pequeno Skus**|Tamanho mínimo é D11 com dois núcleos|Tamanho mínimo é L4 com quatro núcleos |
|**Disponibilidade**|Disponível em todas as regiões (a versão DS+PS tem disponibilidade mais limitada)|Disponível em algumas regiões |
|**Custo&nbsp;por GB cache por núcleo**|Alta com o D SKU, baixa com a versão DS+PS|Mais baixo com a opção Pay-As-You-Go |
|**Preços de Instâncias Reservadas (RI)**|Desconto alto (mais&nbsp;de 55% para um compromisso de três anos)|Menor desconto (20%&nbsp;para um compromisso de três anos) |  

## <a name="select-your-cluster-vm"></a>Selecione seu cluster VM 

Para selecionar sua VM de cluster, [configure o dimensionamento vertical](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Com várias opções de VM SKU para escolher, você pode otimizar os custos para os requisitos de desempenho e hot-cache para o seu cenário. 
* Se você precisar do desempenho ideal para um alto volume de consulta, o SKU ideal deve ser otimizado para computação. 
* Se você precisar consultar grandes volumes de dados com uma carga de consulta relativamente menor, o SKU otimizado para armazenamento pode ajudar a reduzir custos e ainda fornecer um excelente desempenho.

Como o número de instâncias por cluster para as Pequenas SKUs é limitado, é preferível usar VMs maiores que tenham ram maior. Mais RAM é necessária para alguns tipos de consulta que colocam mais `joins`demanda no recurso ram, como consultas que usam . Portanto, quando você está considerando opções de dimensionamento, recomendamos que você dimensione até um SKU maior em vez de dimensionar adicionando mais instâncias.

## <a name="vm-options"></a>Opções de VM

As especificações técnicas das VMs do cluster Azure Data Explorer estão descritas na tabela a seguir:

|**Nome**| **Categoria** | **Tamanho do SSD** | **Núcleos** | **Ram** | **Discos de armazenamento&nbsp;premium (1 TB)**| **Contagem mínima de instâncias por cluster** | **Contagem máxima de instâncias por cluster**
|---|---|---|---|---|---|---|---
|D11 v2| computação otimizada | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (exceto para dev/teste SKU, que é 1)
|D12 v2| computação otimizada | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| computação otimizada | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| computação otimizada | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| otimizado para armazenamento | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| otimizado para armazenamento | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| otimizado para armazenamento | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| otimizado para armazenamento | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| otimizado para armazenamento | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| otimizado para armazenamento | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| otimizado para armazenamento | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Você pode visualizar a lista VM SKU atualizada por região usando a API do Azure Data Explorer [Skus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Saiba mais sobre as [várias SKUs](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Próximas etapas

* Você pode [escalar ou reduzir](manage-cluster-vertical-scaling.md) o cluster do motor a qualquer momento alterando o VM SKU, dependendo das necessidades diferentes. 

* Você pode [escalar ou dimensionar](manage-cluster-horizontal-scaling.md) o tamanho do cluster do motor para alterar a capacidade, dependendo das demandas em mudança.

