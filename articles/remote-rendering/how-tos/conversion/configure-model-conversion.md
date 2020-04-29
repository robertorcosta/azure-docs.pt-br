---
title: Configurar a conversão de modelo
description: Descrição de todos os parâmetros de conversão de modelo
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681565"
---
# <a name="configure-the-model-conversion"></a>Configurar a conversão de modelo

Este capítulo documenta as opções para a conversão de modelo.

## <a name="settings-file"></a>Arquivo de configurações

Se um arquivo chamado `ConversionSettings.json` for encontrado no contêiner de entrada ao lado do modelo de entrada, ele será usado para fornecer configuração adicional para o processo de conversão de modelo.

O conteúdo do arquivo deve satisfazer o seguinte esquema JSON:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Um exemplo `ConversionSettings.json` de arquivo pode ser:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parâmetros de geometria

* `scaling`-Esse parâmetro dimensiona um modelo uniformemente. O dimensionamento pode ser usado para aumentar ou reduzir um modelo, por exemplo, para exibir um modelo de construção em uma tabela de cima. Como o mecanismo de renderização espera que os comprimentos sejam especificados em metros, outro uso importante desse parâmetro surge quando um modelo é definido em unidades diferentes. Por exemplo, se um modelo for definido em centímetros, a aplicação de uma escala de 0, 1 deverá renderizar o modelo no tamanho correto.
Alguns formatos de dados de origem (por exemplo. fbx) fornecem uma dica de dimensionamento de unidade; nesse caso, a conversão dimensiona implicitamente o modelo para unidades de medidor. O dimensionamento implícito fornecido pelo formato de origem será aplicado na parte superior do parâmetro de dimensionamento.
O fator de dimensionamento final é aplicado aos vértices de geometria e às transformações locais dos nós do grafo de cena. O dimensionamento para a transformação da entidade raiz permanece inalterado.

* `recenterToOrigin`-Informa que um modelo deve ser convertido para que sua caixa delimitadora seja centralizada na origem.
A centralização será importante se o modelo de origem for inserido longe da origem, pois, nesse caso, problemas de precisão de ponto flutuante podem causar artefatos de renderização.

* `opaqueMaterialDefaultSidedness`-O mecanismo de renderização pressupõe que os materiais opacos são duplas.
Se esse não for o comportamento pretendido, esse parâmetro deverá ser definido como "SingleSided". Para obter mais informações, consulte [renderização de um lado](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Substituições de material

* `material-override`-Esse parâmetro permite que o processamento de materiais seja [personalizado durante a conversão](override-materials.md).

### <a name="color-space-parameters"></a>Parâmetros de espaço de cor

O mecanismo de renderização espera que os valores de cor estejam em espaço linear.
Se um modelo for definido usando o espaço gama, essas opções deverão ser definidas como true.

* `gammaToLinearMaterial`-Converter cores de material do espaço gama em espaço linear
* `gammaToLinearVertex`-Converter cores de vértice de espaço gama em espaço linear

> [!NOTE]
> Para Arquivos FBX, essas configurações são definidas `true` como por padrão. Para todos os outros tipos de arquivo, o `false`padrão é.

### <a name="scene-parameters"></a>Parâmetros de cena

* `sceneGraphMode`-Define como o grafo de cena no arquivo de origem é convertido:
  * `dynamic`(padrão): todos os objetos no arquivo são expostos como [entidades](../../concepts/entities.md) na API e podem ser transformados de forma independente. A hierarquia de nó em tempo de execução é idêntica à estrutura no arquivo de origem.
  * `static`: Todos os objetos são expostos na API, mas não podem ser transformados de forma independente.
  * `none`: O grafo de cena é recolhido em um objeto.

Cada modo tem um desempenho de tempo de execução diferente. No `dynamic` modo, o custo de desempenho é dimensionado linearmente com o número de [entidades](../../concepts/entities.md) no grafo, mesmo quando nenhuma parte é movida. Ele só deve ser usado ao mover partes individualmente é necessário para o aplicativo, por exemplo, para uma animação de "exibição de explosão".

O `static` modo exporta o grafo de cena completo, mas partes dentro desse grafo têm uma transformação constante em relação à sua parte raiz. O nó raiz do objeto, no entanto, ainda pode ser movido, girado ou dimensionado sem custo de desempenho significativo. Além disso, as [consultas espaciais](../../overview/features/spatial-queries.md) retornarão partes individuais e cada parte pode ser modificada por meio de [substituições de estado](../../overview/features/override-hierarchical-state.md). Com esse modo, a sobrecarga de tempo de execução por objeto é insignificante. É ideal para grandes cenas em que você ainda precisa de inspeção por objeto, mas nenhuma transformação por objeto é alterada.

O `none` modo tem a sobrecarga de tempo de execução mínima e também um tempo de carregamento ligeiramente melhor. A inspeção ou a transformação de objetos únicos não é possível nesse modo. Os casos de uso são, por exemplo, modelos Photogrammetry que não têm um grafo de cena significativo em primeiro lugar.

> [!TIP]
> Muitos aplicativos carregarão vários modelos. Você deve otimizar os parâmetros de conversão para cada modelo, dependendo de como ele será usado. Por exemplo, se você quiser exibir o modelo de um carro para que o usuário entre e inspecione em detalhes, você precisará convertê-lo com `dynamic` o modo. No entanto, se você quiser inserir o carro em um ambiente de apresentação, esse modelo poderá ser convertido com `sceneGraphMode` definido como `static` ou mesmo `none`.

### <a name="physics-parameters"></a>Parâmetros de física

* `generateCollisionMesh`-Se você precisar de suporte para [consultas espaciais](../../overview/features/spatial-queries.md) em um modelo, essa opção deverá ser habilitada. Na pior das hipóteses, a criação de uma malha de colisão pode dobrar o tempo de conversão. Modelos com malhas de colisão demoram mais para carregar e `dynamic` ao usar um grafo de cena, eles também têm uma sobrecarga de desempenho de tempo de execução maior. Para obter um desempenho ideal geral, você deve desabilitar essa opção em todos os modelos nos quais não precisa de consultas espaciais.

### <a name="unlit-materials"></a>Materiais de unlit

* `unlitMaterials`-Por padrão, a conversão prefere criar [materiais de PBR](../../overview/features/pbr-materials.md). Essa opção informa o conversor para tratar todos os materiais como [materiais de cor](../../overview/features/color-materials.md) em vez disso. Se você tiver dados que já incorporam iluminação, como modelos criados por meio de Photogrammetry, essa opção permitirá que você aplique rapidamente a conversão correta para todos os materiais, sem a necessidade de [substituir cada material](override-materials.md) individualmente.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Convertendo de formatos FBX mais antigos, com um modelo de material Phong

* `fbxAssumeMetallic`-Versões mais antigas do formato FBX definem seus materiais usando um modelo de material Phong. O processo de conversão precisa inferir como esses materiais são mapeados para o [modelo PBR](../../overview/features/pbr-materials.md)do renderizador. Normalmente isso funciona bem, mas uma ambiguidade pode surgir quando um material não tem texturas, valores especulares altos e uma cor de albedo não cinza. Nessa circunstância, a conversão deve escolher entre priorizar os valores especulares altos, definir um material metálico altamente reflexivo, em que a cor albedo é descartada ou priorizar a cor albedo, definindo algo como um plástico colorido brilhante. Por padrão, o processo de conversão pressupõe que valores altamente especulares sugerem um material metálico nos casos em que a ambiguidade se aplica. Esse parâmetro pode ser definido como `false` para mudar para o oposto.

### <a name="coordinate-system-overriding"></a>Coordenar substituição do sistema

* `axis`-Para substituir os vetores de unidade do sistema de coordenadas. Os valores padrão `["+x", "+y", "+z"]`são. Teoricamente, o formato FBX tem um cabeçalho onde esses vetores são definidos e a conversão usa essas informações para transformar a cena. O formato glTF também define um sistema de coordenadas fixo. Na prática, alguns ativos têm informações incorretas em seu cabeçalho ou foram salvos com uma Convenção de sistema de coordenadas diferente. Essa opção permite que você substitua o sistema de coordenadas para compensar. Por exemplo: `"axis" : ["+x", "+z", "-y"]` vai trocar o eixo Z e o eixo y e manter o sistema de coordenadas destromente invertendo a direção do eixo Y.

### <a name="vertex-format"></a>Formato de vértice

É possível ajustar o formato de vértice para uma malha, a fim de trocar a precisão pela economia de memória. Uma superfície de memória menor permite que você carregue modelos maiores ou obtenha um melhor desempenho. No entanto, dependendo de seus dados, o formato incorreto pode afetar significativamente a qualidade da renderização.

> [!CAUTION]
> A alteração do formato de vértice deve ser um último recurso quando os modelos não couberem mais na memória, ou ao otimizar para o melhor desempenho possível. As alterações podem introduzir facilmente artefatos de renderização, tanto os óbvios quanto os sutis. A menos que você saiba o que procurar, você não deve alterar o padrão.

Esses ajustes são possíveis:

* Fluxos de dados específicos podem ser incluídos ou excluídos explicitamente.
* A precisão dos fluxos de dados pode ser diminuída para reduzir o volume de memória.

A seção `vertex` a seguir no `.json` arquivo é opcional. Para cada parte que não é especificada explicitamente, o serviço de conversão volta à sua configuração padrão.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Ao forçar um componente ao `NONE`, é garantido que a malha de saída não tenha o respectivo fluxo.

#### <a name="component-formats-per-vertex-stream"></a>Formatos de componente por fluxo de vértice

Esses formatos são permitidos para os respectivos componentes:

| Componente de vértice | Formatos com suporte (negrito = padrão) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, nenhum |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **nenhum**|
|normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, nenhum |
|tangente| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, nenhum |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, nenhum |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, nenhum |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, nenhum |

#### <a name="supported-component-formats"></a>Formatos de componentes com suporte

Os vestígios de memória dos formatos são os seguintes:

| Formatar | Descrição | Bytes por vértice |
|:-------|:------------|:---------------|
|32_32_FLOAT|precisão de ponto flutuante completo de dois componentes|8
|16_16_FLOAT|precisão de ponto flutuante de dois componentes|4
|32_32_32_FLOAT|precisão de ponto flutuante completo de três componentes|12
|16_16_16_16_FLOAT|precisão de ponto flutuante de quatro componentes|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte de quatro componentes, normalizado para `[0; 1]` intervalo|4
|8_8_8_8_SIGNED_NORMALIZED|byte de quatro componentes, normalizado para `[-1; 1]` intervalo|4

#### <a name="best-practices-for-component-format-changes"></a>Práticas recomendadas para alterações de formato de componente

* `position`: É raro que a precisão reduzida seja suficiente. **16_16_16_16_FLOAT** introduz artefatos de quantificação perceptíveis, mesmo para modelos pequenos.
* `normal`, `tangent`, `binormal`: Normalmente esses valores são alterados juntos. A menos que haja artefatos de iluminação perceptíveis que resultem da quantificação normal, não há motivo para aumentar sua precisão. Em alguns casos, no entanto, esses componentes podem ser definidos como **nenhum**:
  * `normal`, `tangent`e `binormal` são necessários apenas quando pelo menos um material no modelo deve estar aceso. Em ARR, esse é o caso quando um [material de PBR](../../overview/features/pbr-materials.md) é usado no modelo a qualquer momento.
  * `tangent`e `binormal` são necessários apenas quando qualquer um dos materiais acesos usa uma textura de mapa normal.
* `texcoord0`, `texcoord1` : As coordenadas de textura podem usar a**16_16_FLOAT** `[0; 1]` precisão reduzida (16_16_FLOAT) quando seus valores permanecem no intervalo e quando as texturas endereçadas têm um tamanho máximo de 2048 x 2048 pixels. Se esses limites forem excedidos, a qualidade do mapeamento de textura será prejudicada.

#### <a name="example"></a>Exemplo

Suponha que você tenha um modelo Photogrammetry, que tem inclusas de iluminação nas texturas. Tudo o que é necessário para renderizar o modelo são as posições de vértice e as coordenadas de textura.

Por padrão, o conversor deve pressupor que você talvez queira usar os materiais de PBR em um modelo em algum momento, para que `normal`ele `tangent`gere dados `binormal` , e para você. Consequentemente, o uso de memória por `position` vértice é (12 bytes `texcoord0` ) + (8 bytes `normal` ) + (4 bytes `tangent` ) + (4 bytes `binormal` ) + (4 bytes) = 32 bytes. Modelos maiores desse tipo podem facilmente ter muitos milhões de vértices, resultando em modelos que podem ocupar vários gigabytes de memória. Essas grandes quantidades de dados afetarão o desempenho e você poderá até mesmo ficar sem memória.

Sabendo que você nunca precisa de iluminação dinâmica no modelo e sabendo que todas as coordenadas de textura estão `[0; 1]` no intervalo, você pode `normal`definir `tangent`, e `binormal` para `NONE` e `texcoord0` a metade de precisão`16_16_FLOAT`(), resultando em apenas 16 bytes por vértice. Cortar os dados de malha na metade permite que você carregue modelos maiores e potencialmente melhora o desempenho.

## <a name="typical-use-cases"></a>Casos de uso típicos

Encontrar boas configurações de importação para um determinado caso de uso pode ser um processo entediante. Por outro lado, as configurações de conversão podem ter um impacto significativo no desempenho do tempo de execução.

Há certas classes de casos de uso que se qualificam para otimizações específicas. Alguns exemplos são fornecidos abaixo.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso de uso: visualização da arquitetura/mapas grandes do ar

* Esses tipos de cenas tendem a ser estáticos, o que significa que eles não precisam de partes móveis. Da mesma forma `sceneGraphMode` , o pode ser `static` definido como `none`ou até mesmo, o que melhora o desempenho do tempo de execução. Com `static` o modo, o nó raiz da cena ainda pode ser movido, girado e dimensionado, por exemplo, para alternar dinamicamente entre a escala de 1:1 (para a primeira exibição de pessoa) e uma exibição de tabela superior.

* Quando você precisa mover partes, isso normalmente também significa que você precisa de suporte para raycasts ou outras [consultas espaciais](../../overview/features/spatial-queries.md), para que você possa escolher essas partes em primeiro lugar. Por outro lado, se você não pretende mover algo, é muito provável que você também não precise dela para participar de consultas espaciais e, portanto, pode desativar o `generateCollisionMesh` sinalizador. Essa opção tem um impacto significativo nos tempos de conversão, tempos de carregamento e também custos de atualização por quadro em tempo de execução.

* Se o aplicativo não usar [planos de recorte](../../overview/features/cut-planes.md), `opaqueMaterialDefaultSidedness` o sinalizador deverá ser desativado. O lucro de desempenho geralmente é de 20% a 30%. Os planos de recorte ainda podem ser usados, mas não haverá faces de volta ao examinar as partes internas de objetos, o que parece um contador intuitivo. Para obter mais informações, consulte [renderização de um lado](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso de uso: modelos de Photogrammetry

Ao renderizar modelos Photogrammetry, normalmente não há necessidade de um grafo de cena, portanto, você `sceneGraphMode` pode `none`definir como. No entanto, como esses modelos raramente contêm um grafo de cena complexo para começar, o impacto dessa opção deve ser insignificante.

Como a iluminação já está inclusas nas texturas, nenhuma iluminação dinâmica é necessária. Portanto:

* Defina o `unlitMaterials` sinalizador como `true` para transformar todos os materiais em [materiais de cores](../../overview/features/color-materials.md)unlits.
* Remova os dados desnecessários do formato de vértice. Consulte o [exemplo](#example) acima.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso de uso: visualização de máquinas compactos etc.

Nesses casos de uso, os modelos geralmente têm muitos detalhes em um volume pequeno. O renderizador é altamente otimizado para lidar com esses casos. No entanto, a maioria das otimizações mencionadas no caso de uso anterior não se aplicam aqui:

* As partes individuais devem ser selecionáveis e móveis, `sceneGraphMode` portanto, o deve `dynamic`ser deixado em.
* Os Ray casts normalmente são parte integrante do aplicativo, portanto, as malhas de colisão devem ser geradas.
* Os planos de corte parecem melhores `opaqueMaterialDefaultSidedness` com o sinalizador habilitado.

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](model-conversion.md)
* [Materiais de cores](../../overview/features/color-materials.md)
* [Materiais de PBR](../../overview/features/pbr-materials.md)
* [Substituir materiais durante a conversão de modelo](override-materials.md)
