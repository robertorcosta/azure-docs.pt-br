---
title: Mapeamento de materiais para formatos de modelos
description: Descreve a conversão padrão dos formatos de origem do modelo para o material PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680382"
---
# <a name="material-mapping-for-model-formats"></a>Mapeamento de materiais para formatos de modelos

Quando um ativo de origem é [convertido como modelo,](../how-tos/conversion/model-conversion.md)o conversor cria [materiais](../concepts/materials.md) para cada [malha](../concepts/meshes.md). A forma como os materiais são criados pode ser [substituída.](../how-tos/conversion/override-materials.md) No entanto, por padrão, a conversão criará [materiais PBR.](../overview/features/pbr-materials.md) Uma vez que cada formato de arquivo de origem, como o FBX, usa suas próprias convenções para definir materiais, essas convenções devem ser mapeadas para os parâmetros de material PBR da Renderização Remota Do Azure. 

Este artigo lista os mapeamentos exatos usados para converter materiais de ativos de origem em materiais de tempo de execução.

## <a name="gltf"></a>glTF

Quase tudo a partir da especificação glTF 2.0 é suportado na Renderização Remota Do Zure, exceto *EmissiveFactor* e *EmissiveTexture*.

A tabela abaixo mostra o mapeamento:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   textura de base  |   albedoMap                |
|   Fator metálico    |   metalidade                |
|   Textura metálica   |   metalnessMap             |
|   roughnessFactor   |   Rugosidade                |
|   rugosidadeTextura  |   roughnessMap             |
|   oclusionFactor   |   Oclusão                |
|   oclusãoTextura  |   oclusãoMap             |
|   textura normal     |   Normalmap                |
|   alfaCutoff       |   alfaClipThreshold       |
|   alfaMode.OPAQUE  |   alphaClipEnabled = false, isTransparent = false |
|   alfaMode.MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alfaMode.BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissivaFactor    |   -                        |
|   emissivaTextura   |   -                        |

Cada textura em glTF `texCoord` pode ter um valor, que também é suportado nos materiais de renderização remota do Azure.

### <a name="embedded-textures"></a>Texturas incorporadas

Texturas embutidas em * \** arquivos .bin ou * \*.glb* são suportadas.

### <a name="supported-gltf-extension"></a>Extensão glTF suportada

Além do conjunto de recursos base, a renderização remota do Azure suporta as seguintes extensões glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Corresponde a [materiais coloridos.](../overview/features/color-materials.md) Para materiais *emissivos,* recomenda-se usar esta extensão.
* [KHR_materials_pbrSpecularGlossiness:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)Em vez de texturas de rugosidade metálica, você pode fornecer texturas difusas-especular-glosscular. A implementação de renderização remota do Azure segue diretamente as fórmulas de conversão da extensão.

## <a name="fbx"></a>Fbx

O formato FBX é de código fechado e os materiais FBX não são compatíveis com materiais PBR em geral. FbX usa uma descrição complexa de superfícies com muitos parâmetros e propriedades únicas e **nem todas elas são usadas pelo pipeline de renderização remota Azure**.

> [!IMPORTANT]
> O pipeline de conversão do modelo de renderização remota Azure só suporta **FBX 2011 e superior**.

O formato FBX define uma abordagem conservadora para materiais, existem apenas dois tipos na especificação oficial fbx:

* *Lambert* - Não é comumente usado há algum tempo, mas ainda é suportado pela conversão para Phong na hora da conversão.
* *Phong* - Quase todos os materiais e a maioria das ferramentas de conteúdo usam esse tipo.

O modelo Phong é mais preciso e é usado como o *único* modelo para materiais FBX. Abaixo será referido como o *Material FBX*.

> Maya usa duas extensões personalizadas para FBX definindo propriedades personalizadas para tipos pbr e stingray de um material. Esses detalhes não estão incluídos na especificação FBX, portanto não é suportado pelo Azure Remote Rendering atualmente.

Os materiais FBX usam o conceito Diffuse-Specular-SpecularLevel, de modo que para converter de uma textura difusa para um mapa albedo precisamos calcular os outros parâmetros para subtraí-los de difusos.

> Todas as cores e texturas em FBX estão no espaço sRGB (também conhecido como espaço Gamma), mas a Renderização Remota Azure funciona com espaço linear durante a visualização e no final do quadro converte tudo de volta ao espaço sRGB. O pipeline de ativos de renderização remota do Azure converte tudo em espaço linear para enviá-lo como dados preparados para o renderizador.

Esta tabela mostra como as texturas são mapeadas de materiais FBX para materiais de renderização remota do Azure. Alguns deles não são usados diretamente, mas em combinação com outras texturas participantes das fórmulas (por exemplo, a textura difusa):

| Fbx | Azure Remote Rendering |
|:-----|:----|
| Cor ambiente | Mapa de Oclusão   |
| Cor difusa | *usado para Albedo, Metalness* |
| Transparentcolor | *usado para o canal alfa de Albedo* |
| TransparênciaFactor | *usado para o canal alfa de Albedo* |
| Opacidade | *usado para o canal alfa de Albedo* |
| SpecularColor | *usado para Albedo, Metalness, Aspereza* |
| Fator Especular| *usado para Albedo, Metalness, Aspereza* |
| ShininessExponent | *usado para Albedo, Metalness, Aspereza* |
| Normalmap | Normalmap |
| Colisão | *convertido em NormalMap* |
| EmissivaColor | - |
| Fator Emissivo | - |
| Cor de reflexão | - |
| Cor de deslocamento | - |

O mapeamento acima é a parte mais complexa da conversão material, devido a muitas suposições que devem ser feitas. Discutimos essas suposições abaixo.

Algumas definições utilizadas abaixo:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Vermelho ◗ 0,2125 + `Specular`. Verde ◗ 0,7154 + `Specular`. Azul ◗ 0,0721
* `DiffuseBrightness`= 0,299 `Diffuse`* . Vermelho<sup>2</sup> + 0,587 * `Diffuse`. Verde<sup>2</sup> + 0,114 * `Diffuse`. Azul<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Vermelho<sup>2</sup> + 0,587 * `Specular`. Verde<sup>2</sup> + 0,114 * `Specular`. Azul<sup>2</sup>
* `SpecularStrength`= máx.`Specular` Vermelho, `Specular`. Verde, `Specular`. Azul)

A fórmula SpecularIntensity é obtida a partir [daqui](https://en.wikipedia.org/wiki/Luma_(video)).
A fórmula de brilho é descrita nesta [especificação](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Rugosidade

`Roughness`é calculado `Specular` `ShininessExponent` a partir e usando [esta fórmula](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). A fórmula é uma aproximação da rugosidade do expoente especulativo phong:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalúrgica

`Metalness`é calculado `Diffuse` `Specular` a partir e usando esta [fórmula a partir da especificação glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

A ideia aqui é que resolvamos a equação: Ax<sup>2</sup> + Bx + C = 0.
Basicamente, as superfícies dielétricas refletem cerca de 4% da luz de forma especular, e o resto é difuso. Superfícies metálicas não refletem luz de forma difusa, mas tudo de forma especular.
Esta fórmula tem algumas desvantagens, pois não há como distinguir entre plástico brilhante e superfícies metálicas brilhantes. Assumimos que na maioria das vezes a superfície tem propriedades metálicas e, consequentemente, superfícies de plástico/borracha brilhantes podem não parecer como esperado.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`é computado `Specular`a `Metalness`partir de `Diffuse`, e .

Como descrito na seção Metalness, as superfícies dielétricas refletem cerca de 4% da luz.  
A ideia aqui é interpolar `Dielectric` linearmente entre e `Metal` cores usando `Metalness` o valor como fator. Se a `0.0`metalúrgica for, então, dependendo do especular, será uma cor escura (se o especular estiver alto) ou difusa não mudará (se não houver especulação). Se a metalúrgica é um grande valor, então a cor difusa desaparecerá em favor da cor especular.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`foi calculado pela fórmula acima, mas o canal alfa requer cálculos adicionais. O formato FBX é vago sobre transparência e tem muitas maneiras de defini-lo. Diferentes ferramentas de conteúdo usam métodos diferentes. A idéia aqui é unificar-los em uma fórmula. Isso torna alguns ativos incorretamente mostrados como transparentes, porém, se eles não são criados de forma comum.

Isto é `TransparentColor`calculado `TransparencyFactor` `Opacity`a partir de :

se `Opacity` for definido, então `AlbedoAlpha`  =  `Opacity` use-o diretamente: senão  
se `TransparencyColor` for definido, então `AlbedoAlpha` = 1,0 - (.`TransparentColor` Vermelho `TransparentColor`+ . Verde `TransparentColor`+ . Azul) / 3.0) mais  
se `TransparencyFactor`, `AlbedoAlpha` então = 1,0 -`TransparencyFactor`

A `Albedo` cor final tem quatro `AlbedoRGB` canais, `AlbedoAlpha`combinando o com o .

### <a name="summary"></a>Resumo

Resumindo aqui, `Albedo` será muito próximo `Diffuse`do `Specular` original, se for perto de zero. Caso contrário, a superfície se parecerá com uma superfície metálica e perde a cor difusa. A superfície vai parecer mais `ShininessExponent` polida e `Specular` reflexiva se for grande o suficiente e é brilhante. Caso contrário, a superfície parecerá áspera e mal refletirá o ambiente.

### <a name="known-issues"></a>Problemas conhecidos

* A fórmula atual não funciona bem para geometria colorida simples. Se `Specular` for brilhante o suficiente, então todas as geometrias se tornam superfícies metálicas reflexivas sem qualquer cor. A solução aqui é `Specular` diminuir para 30% do original ou usar a configuração de conversão [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Os materiais PBR `Maya` `3DS Max` foram recentemente adicionados e ferramentas de criação de conteúdo. Eles usam propriedades personalizadas de caixa preta definidas pelo usuário para passá-la para fbx. A renderização remota do Azure não lê essas propriedades adicionais porque elas não estão documentadas e o formato é de origem fechada.

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](../how-tos/conversion/model-conversion.md)
* [Materiais sobrepor durante a conversão do modelo](../how-tos/conversion/override-materials.md)
