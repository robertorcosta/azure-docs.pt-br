---
title: Balanceamento de métricas subagrupadas
description: O efeito das restrições de colocação no equilíbrio e como lidar com isso
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430637"
---
# <a name="balancing-of-subclustered-metrics"></a>Balanceamento de métricas subagrupadas

## <a name="what-is-subclustering"></a>O que é subclustering

O subclustering acontece quando serviços com diferentes restrições de colocação têm uma métrica comum e ambos relatam carga para ele. Se a carga relatada pelos serviços difere significativamente, a carga total nos nós terá um grande desvio padrão e pareceria que o cluster está desequilibrado, mesmo quando tiver o melhor equilíbrio possível.

## <a name="how-subclustering-affects-load-balancing"></a>Como o subclustering afeta o balanceamento de carga

Se a carga relatada pelos serviços em diferentes nós difere significativamente, pode parecer que há um grande desequilíbrio onde não há nenhum. Além disso, se o falso desequilíbrio causado pelo subclustering for maior do que o desequilíbrio real, ele tem o potencial de confundir o algoritmo de balanceamento do Resource Manager e produzir equilíbrio abaixo do ideal no cluster.

Por exemplo, digamos que temos quatro serviços e todos relatam uma carga para métrica métrica1:

* Serviço A – tem uma restrição de colocação "NodeType=Type1", relata uma carga de 10
* Serviço B – tem uma restrição de colocação "NodeType=Type1", relata uma carga de 10
* Serviço C – tem uma restrição de colocação "NodeType=Type2", relata uma carga de 100
* Serviço D – tem uma restrição de colocação "NodeType=Type2", relata uma carga de 100
* E nós temos quatro nós. Dois deles têm nodeType definido como "Type1" e os outros dois são "Type2"

E temos a seguinte colocação:

<center>

![Exemplo de colocação subclustered][Image1]
</center>

O cluster pode parecer desequilibrado, temos uma grande carga nos nós 3 e 4, mas essa colocação cria o melhor equilíbrio possível nesta situação.

O Resource Manager pode reconhecer situações de subclustering e, em quase todos os casos, pode produzir o equilíbrio ideal para a situação dada.

Para algumas situações excepcionais, quando o Resource Manager não é capaz de equilibrar de forma otimizada uma métrica subclustered, ele ainda detectará subclustering e gerará um relatório de saúde para aconselhá-lo a corrigir o problema.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Tipos de subclustering e como eles são tratados

As situações de subagrupamento podem ser classificadas em três categorias diferentes. A categoria de uma situação específica de subclustering determina como ela será tratada pelo Gerenciador de Recursos.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Primeira categoria – subagrupamento plano com grupos de nó desarticulado

Esta categoria tem a forma mais simples de subagrupamento onde os nados podem ser separados em grupos diferentes e cada serviço só pode ser colocado em nós em um desses grupos. Cada nó pertence a um grupo e apenas a um grupo. A situação descrita acima pertence a esta categoria, assim como a maioria das situações de subagrupamento. 

Para as situações desta categoria, o Gestor de Recursos pode produzir o equilíbrio ideal e nenhuma intervenção adicional é necessária.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Segunda categoria – subagrupamento com grupos de nó hierárquico

Essa situação acontece quando um grupo de nódulos permitidos para um serviço é um subconjunto do grupo de nódulos permitidos para outro serviço. O exemplo mais comum dessa situação é quando algum serviço tem uma restrição de colocação definida e outro serviço não tem restrição de colocação e pode ser colocado em qualquer nó.

Exemplo:

* Serviço A: sem restrição de colocação
* Serviço B: restrição de colocação "NodeType=Type1"
* Serviço C: restrição de colocação "NodeType=Type2"

Essa configuração cria uma relação subconjunto-superset entre grupos de nós para diferentes serviços.

<center>

![Subconjunto de subaglomerados][Image2]
</center>

Nesta situação, há uma chance de que um equilíbrio subótimo seja feito.

O Gerenciador de Recursos reconhecerá essa situação e produzirá um relatório de saúde aconselhando-o a dividir o Serviço A em dois serviços – Serviço A1 que pode ser colocado em nós Tipo1 e Serviço A2 que podem ser colocados em nós Tipo2. Isso nos levará de volta à situação da primeira categoria que pode ser equilibrada de forma ideal.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Terceira categoria – subagrupamento com sobreposição parcial entre conjuntos de nós

Essa situação acontece quando há uma sobreposição parcial entre conjuntos de nós sobre os quais alguns serviços podem ser colocados.

Por exemplo, se temos uma propriedade de nós chamada NodeColor e temos três nós:

* Nó 1: NodeColor=Red
* Nó 2: NodeColor=Blue
* Nó 2: NodeColor=Green

E temos dois serviços:

* Serviço A: com restrição de colocação "Color==Red || Cor==Azul"
* Serviço B: com restrição de colocação "Color==Blue || Cor==Verde"

Por causa disso, o Serviço A pode ser colocado nos nós 1 e 2 e o Serviço B pode ser colocado nos nós 2 e 3.

Nesta situação, há uma chance de que um equilíbrio subótimo seja feito.

O Gestor de Recursos reconhecerá essa situação e produzirá um relatório de saúde aconselhando-o a dividir alguns dos serviços.

Para essa situação, o Gestor de Recursos não é capaz de dar uma proposta de como dividir os serviços, uma vez que várias divisões podem ser feitas e não há como estimar qual seria o caminho ideal para dividir os serviços.

## <a name="configuring-subclustering"></a>Configuração de subclustering

O comportamento do Gerenciador de recursos sobre subclustering pode ser modificado modificando os seguintes parâmetros de configuração:
* SubclusteringEnabled - parâmetro determina se o Gerenciador de recursos levará em conta o subclustering ao fazer o balanceamento de carga. Se esse parâmetro for desligado, o Resource Manager ignorará o subclustering e tentará obter o equilíbrio ideal em um nível global. O valor padrão deste parâmetro é falso.
* SubclusteringReportingPolicy - determina como o Resource Manager emite relatórios de saúde para subagrupamento hierárquico e parcialmente sobreposto. Um valor de zero significa que os relatórios de saúde sobre subclustering são desligados, "1" significa que os relatórios de saúde de advertência serão produzidos para situações de subaglomeração subótimas e um valor de "2" produzirá relatórios de saúde "OK". O valor padrão para este parâmetro é "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
* Para saber como seus serviços podem ser limitados a serem colocados apenas em certos nódulos, consulte [propriedades do Nó e restrições de colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
