---
title: Configure a conversão do modelo
description: Descrição de todos os parâmetros de conversão do modelo
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681565"
---
# <a name="configure-the-model-conversion"></a>Configure a conversão do modelo

Este capítulo documenta as opções para a conversão do modelo.

## <a name="settings-file"></a>Arquivo de configurações

Se um `ConversionSettings.json` arquivo chamado for encontrado no recipiente de entrada ao lado do modelo de entrada, ele será usado para fornecer configuração adicional para o processo de conversão do modelo.

O conteúdo do arquivo deve satisfazer o seguinte esquema json:

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

Um `ConversionSettings.json` arquivo de exemplo pode ser:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parâmetros de geometria

* `scaling`- Este parâmetro dimensiona um modelo uniformemente. O dimensionamento pode ser usado para crescer ou encolher um modelo, por exemplo, para exibir um modelo de construção em uma mesa. Uma vez que o motor de renderização espera que os comprimentos sejam especificados em medidores, outro importante uso deste parâmetro surge quando um modelo é definido em diferentes unidades. Por exemplo, se um modelo é definido em centímetros, então a aplicação de uma escala de 0,01 deve renderizar o modelo no tamanho correto.
Alguns formatos de dados de origem (por exemplo .fbx) fornecem uma dica de escala de unidade, nesse caso a conversão escala implicitamente o modelo para unidades de medidor. O dimensionamento implícito fornecido pelo formato de origem será aplicado em cima do parâmetro de escala.
O fator de escala final é aplicado aos vértices de geometria e às transformações locais dos nódulos gráficos de cena. O dimensionamento para a transformação da entidade raiz permanece não modificado.

* `recenterToOrigin`- Afirma que um modelo deve ser convertido para que sua caixa delimitador seja centrada na origem.
A centralação é importante se o modelo de origem for deslocado longe da origem, já que, nesse caso, problemas de precisão de ponto flutuante podem causar artefatos de renderização.

* `opaqueMaterialDefaultSidedness`- O motor de renderização pressupõe que os materiais opacos são de dupla dobra.
Se esse não for o comportamento pretendido, este parâmetro deve ser definido como "SingleSided". Para obter mais informações, consulte [renderização unilateral](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Substituições materiais

* `material-override`- Este parâmetro permite que o processamento de materiais seja personalizado durante a [conversão.](override-materials.md)

### <a name="color-space-parameters"></a>Parâmetros de espaço de cor

O motor de renderização espera que os valores de cor estejam em espaço linear.
Se um modelo for definido usando espaço gama, então essas opções devem ser definidas como verdadeiras.

* `gammaToLinearMaterial`- Converter cores materiais do espaço gama para o espaço linear
* `gammaToLinearVertex`- Converter cores de vértices do espaço gama para o espaço linear

> [!NOTE]
> Para arquivos FBX, essas `true` configurações são definidas como padrão. Para todos os outros tipos `false`de arquivos, o padrão é .

### <a name="scene-parameters"></a>Parâmetros de cena

* `sceneGraphMode`- Define como o gráfico de cena no arquivo de origem é convertido:
  * `dynamic`(padrão): Todos os objetos no arquivo são expostos como [entidades](../../concepts/entities.md) na API e podem ser transformados independentemente. A hierarquia do nó em tempo de execução é idêntica à estrutura do arquivo de origem.
  * `static`: Todos os objetos estão expostos na API, mas não podem ser transformados independentemente.
  * `none`: O gráfico da cena é colapsado em um objeto.

Cada modo tem um desempenho de tempo de execução diferente. No `dynamic` modo, o custo de desempenho é escalonado linearmente com o número de [entidades](../../concepts/entities.md) no gráfico, mesmo quando nenhuma peça é movida. Ele só deve ser usado quando peças móveis individualmente são necessárias para a aplicação, por exemplo, para uma animação de 'visualização de explosão'.

O `static` modo exporta o gráfico de cena completa, mas as partes dentro deste gráfico têm uma transformação constante em relação à sua parte raiz. O nó raiz do objeto, no entanto, ainda pode ser movido, girado ou dimensionado sem custo significativo de desempenho. Além disso, [as consultas espaciais](../../overview/features/spatial-queries.md) retornarão partes individuais e cada peça pode ser modificada através [de substituições de estado](../../overview/features/override-hierarchical-state.md). Com este modo, a sobrecarga de tempo de execução por objeto é insignificante. É ideal para cenas grandes onde você ainda precisa de inspeção por objeto, mas nenhuma mudança de transformação por objeto.

O `none` modo tem a sobrecarga de tempo de execução menor e também tempos de carregamento ligeiramente melhores. A inspeção ou transformação de objetos únicos não é possível neste modo. Os casos de uso são, por exemplo, modelos de fotogrametria que não têm um gráfico de cena significativo em primeiro lugar.

> [!TIP]
> Muitos aplicativos carregarão vários modelos. Você deve otimizar os parâmetros de conversão para cada modelo, dependendo de como ele será usado. Por exemplo, se você quiser exibir o modelo de um carro para o usuário desmontar e inspecionar em detalhes, você precisa convertê-lo com `dynamic` o modo. No entanto, se você ainda quiser colocar o carro em um `sceneGraphMode` ambiente `static` de `none`sala de exibição, esse modelo pode ser convertido com set para ou mesmo .

### <a name="physics-parameters"></a>Parâmetros da física

* `generateCollisionMesh`- Se você precisar de suporte para [consultas espaciais](../../overview/features/spatial-queries.md) em um modelo, esta opção deve ser habilitada. Na pior das hipóteses, a criação de uma malha de colisão pode dobrar o tempo de conversão. Modelos com barras de colisão demoram `dynamic` mais para carregar e, ao usar um gráfico de cena, eles também têm uma sobrecarga de desempenho de execução mais alta. Para obter um desempenho geral ideal, você deve desativar essa opção em todos os modelos nos quais você não precisa de consultas espaciais.

### <a name="unlit-materials"></a>Materiais não iluminados

* `unlitMaterials`- Por padrão, a conversão prefira criar [materiais PBR.](../../overview/features/pbr-materials.md) Esta opção diz ao conversor para tratar todos os materiais como [materiais de cor.](../../overview/features/color-materials.md) Se você tem dados que já incorporam iluminação, como modelos criados através da fotogrametria, esta opção permite que você aplique rapidamente a conversão correta para todos os materiais, sem a necessidade [de substituir cada material](override-materials.md) individualmente.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversão de formatos FBX mais antigos, com um modelo de material Phong

* `fbxAssumeMetallic`- Versões mais antigas do formato FBX definem seus materiais usando um modelo de material Phong. O processo de conversão tem que inferir como esses materiais mapeiam para o [modelo PBR](../../overview/features/pbr-materials.md)do renderizador. Normalmente isso funciona bem, mas uma ambiguidade pode surgir quando um material não tem texturas, altos valores especulares e uma cor albedo não-cinza. Nesta circunstância, a conversão tem que escolher entre priorizar os altos valores especulares, definir um material altamente reflexivo e metálico onde a cor do albedo se dissolve, ou priorizar a cor albedo, definindo algo como um plástico colorido brilhante. Por padrão, o processo de conversão assume que valores altamente especulares implicam um material metálico nos casos em que a ambiguidade se aplica. Este parâmetro pode ser `false` configurado para mudar para o oposto.

### <a name="coordinate-system-overriding"></a>Substituição do sistema de coordenadas

* `axis`- Para substituir os vetores unitários do sistema. Os valores padrão são `["+x", "+y", "+z"]`. Em teoria, o formato FBX tem um cabeçalho onde esses vetores são definidos e a conversão usa essa informação para transformar a cena. O formato glTF também define um sistema de coordenadas fixo. Na prática, alguns ativos têm informações incorretas em seu cabeçalho ou foram salvos com uma convenção diferente do sistema de coordenadas. Esta opção permite que você anule o sistema de coordenadas para compensar. Por exemplo: `"axis" : ["+x", "+z", "-y"]` trocará o eixo Z e o eixo Y e manterá a capacidade de coordenar o sistema invertendo a direção do eixo Y.

### <a name="vertex-format"></a>Formato vertex

É possível ajustar o formato do vértice para uma malha, para trocar precisão por economia de memória. Uma pegada de memória mais baixa permite carregar modelos maiores ou obter melhor desempenho. No entanto, dependendo dos seus dados, o formato errado pode impactar significativamente a qualidade da renderização.

> [!CAUTION]
> Mudar o formato do vértice deve ser um último recurso quando os modelos não se encaixam mais na memória, ou quando otimizam para o melhor desempenho possível. Mudanças podem facilmente introduzir artefatos de renderização, ambos óbvios e sutis. A menos que você saiba o que procurar, você não deve mudar o padrão.

Estes ajustes são possíveis:

* Fluxos de dados específicos podem ser explicitamente incluídos ou excluídos.
* A precisão dos fluxos de dados pode ser diminuída para reduzir a pegada de memória.

A `vertex` seção `.json` a seguir no arquivo é opcional. Para cada parte que não é explicitamente especificada, o serviço de conversão volta para a configuração padrão.

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

Ao forçar um `NONE`componente a , é garantido que a malha de saída não possui o respectivo fluxo.

#### <a name="component-formats-per-vertex-stream"></a>Formatos de componentes por fluxo de vértice

Esses formatos são permitidos para os respectivos componentes:

| Componente vertex | Formatos suportados (negrito = padrão) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**16_16_16_16_FLOAT |
|cor0| **8_8_8_8_UNSIGNED_NORMALIZED**, NENHUM |
|cor1| 8_8_8_8_UNSIGNED_NORMALIZED **NONE**|
|normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NENHUM |
|tangente| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NENHUM |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NENHUM |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NENHUM |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NENHUM |

#### <a name="supported-component-formats"></a>Formatos de componentes suportados

As pegadas de memória dos formatos são as seguintes:

| Formatar | Descrição | Bytes por vértice |
|:-------|:------------|:---------------|
|32_32_FLOAT|precisão de ponto flutuante de dois componentes|8
|16_16_FLOAT|precisão de ponto flutuante de dois componentes|4
|32_32_32_FLOAT|precisão de ponto flutuante de três componentes|12
|16_16_16_16_FLOAT|precisão de ponto flutuante de quatro componentes|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte de quatro componentes, normalizado para `[0; 1]` intervalo|4
|8_8_8_8_SIGNED_NORMALIZED|byte de quatro componentes, normalizado para `[-1; 1]` intervalo|4

#### <a name="best-practices-for-component-format-changes"></a>Melhores práticas para alterações de formato de componentes

* `position`: É raro que a precisão reduzida seja suficiente. **16_16_16_16_FLOAT** introduz artefatos de quantização perceptíveis, mesmo para modelos pequenos.
* `normal`, `tangent` `binormal`: Normalmente esses valores são alterados juntos. A menos que haja artefatos de iluminação perceptíveis que resultem da quantização normal, não há razão para aumentar sua precisão. Em alguns casos, porém, esses componentes podem ser definidos como **NONE**:
  * `normal`, `tangent`e `binormal` só são necessárias quando pelo menos um material do modelo deve ser aceso. Em ARR este é o caso quando um [material PBR](../../overview/features/pbr-materials.md) é usado no modelo a qualquer momento.
  * `tangent`e `binormal` só são necessários quando qualquer um dos materiais iluminados usa uma textura de mapa normal.
* `texcoord0`, `texcoord1` : As coordenadas de textura podem usar precisão `[0; 1]` reduzida **(16_16_FLOAT)** quando seus valores permanecem na faixa e quando as texturas endereçadas têm um tamanho máximo de 2048 x 2048 pixels. Se esses limites forem excedidos, a qualidade do mapeamento da textura sofrerá.

#### <a name="example"></a>Exemplo

Suponha que você tenha um modelo de fotogrametria, que tem iluminação assada nas texturas. Tudo o que é necessário para renderizar o modelo são posições de vértica e coordenadas de textura.

Por padrão, o conversor tem que assumir que você pode querer usar materiais `normal` `tangent`PBR `binormal` em um modelo em algum momento, para que ele gere , e dados para você. Consequentemente, o uso da `position` memória por vértice `texcoord0` é (12 `normal` bytes) + `tangent` (8 bytes) `binormal` + (4 bytes) + (4 bytes) + (4 bytes) = 32 bytes. Modelos maiores desse tipo podem facilmente ter muitos milhões de vértices resultando em modelos que podem assumir vários gigabytes de memória. Essas grandes quantidades de dados afetarão o desempenho e você pode até ficar sem memória.

Sabendo que você nunca precisa de iluminação dinâmica no modelo, e sabendo que todas as coordenadas de textura estão ao `[0; 1]` alcance, você `normal`pode definir , `tangent`e `binormal` `NONE` `texcoord0` até a metade precisão (),`16_16_FLOAT`resultando em apenas 16 bytes por vértice. Cortar os dados de malha ao meio permite carregar modelos maiores e potencialmente melhora o desempenho.

## <a name="typical-use-cases"></a>Casos de uso típicos

Encontrar boas configurações de importação para um determinado caso de uso pode ser um processo tedioso. Por outro lado, as configurações de conversão podem ter um impacto significativo no desempenho em tempo de execução.

Existem certas classes de casos de uso que se qualificam para otimizações específicas. Alguns exemplos são dados abaixo.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso de uso: Visualização arquitetônica / grandes mapas ao ar livre

* Esses tipos de cenas tendem a ser estáticas, o que significa que não precisam de peças móveis. Assim, o `sceneGraphMode` pode ser `static` definido `none`para ou até mesmo, o que melhora o desempenho em tempo de execução. Com `static` o modo, o nó raiz da cena ainda pode ser movido, girado e dimensionado, por exemplo, para alternar dinamicamente entre a escala 1:1 (para exibição em primeira pessoa) e uma exibição superior da tabela.

* Quando você precisa mover peças ao redor, isso normalmente também significa que você precisa de suporte para raycasts ou [outras consultas espaciais,](../../overview/features/spatial-queries.md)para que você possa escolher essas peças em primeiro lugar. Por outro lado, se você não pretende mover algo, as chances são altas de que você também não precisa `generateCollisionMesh` dele para participar de consultas espaciais e, portanto, pode desligar a bandeira. Este switch tem um impacto significativo nos tempos de conversão, tempos de carregamento e também nos custos de atualização por quadro.

* Se a aplicação não usar `opaqueMaterialDefaultSidedness` planos de [corte,](../../overview/features/cut-planes.md)a bandeira deve ser desligada. O ganho de desempenho é tipicamente de 20%-30%. Planos de corte ainda podem ser usados, mas não haverá back-faces quando olhar para as partes internas dos objetos, o que parece contra-intuitivo. Para obter mais informações, consulte [renderização unilateral](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso de uso: Modelos de fotogrametria

Ao renderizar modelos de fotogrametria, normalmente não há necessidade `sceneGraphMode` `none`de um gráfico de cena, então você pode definir o para . Uma vez que esses modelos raramente contêm um gráfico de cena complexo para começar, o impacto desta opção deve ser insignificante, no entanto.

Como a iluminação já está assada nas texturas, nenhuma iluminação dinâmica é necessária. Portanto:

* Defina `unlitMaterials` a `true` bandeira para transformar todos os materiais em [materiais de cor](../../overview/features/color-materials.md)não iluminados .
* Remova dados desnecessários do formato do vértice. Veja o [exemplo](#example) acima.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso de uso: Visualização de máquinas compactas, etc.

Nestes casos de uso, os modelos geralmente têm detalhes muito altos dentro de um pequeno volume. O renderizador é fortemente otimizado para lidar bem com esses casos. No entanto, a maioria das otimizações mencionadas no caso de uso anterior não se aplicam aqui:

* As peças individuais devem ser selecionáveis `sceneGraphMode` e móveis, `dynamic`de modo que devem ser deixadas para .
* Os moldes de raios são tipicamente parte integrante da aplicação, por isso as formas de colisão devem ser geradas.
* Os aviões cortados ficam melhor com a `opaqueMaterialDefaultSidedness` bandeira ativada.

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](model-conversion.md)
* [Materiais de cor](../../overview/features/color-materials.md)
* [Materiais PBR](../../overview/features/pbr-materials.md)
* [Materiais de substituição durante a conversão do modelo](override-materials.md)
