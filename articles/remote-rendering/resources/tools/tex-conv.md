---
title: TexConv-ferramenta de conversão de textura
description: Manual do usuário para a ferramenta de linha de comando TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80680018"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv-ferramenta de conversão de textura

TexConv é uma ferramenta de linha de comando para processar texturas de formatos de entrada típicos como PNG, TGA, JPEG e DDS em formatos otimizados para consumo de tempo de execução.
Embora o cenário mais comum seja converter um único arquivo de entrada `A.xxx` em um formato otimizado `B.yyy` , a ferramenta tem muitas opções adicionais para usos avançados.

## <a name="command-line-help"></a>Ajuda da linha de comando

A execução de TexConv.exe com o `--help` parâmetro listará todas as opções disponíveis. Além disso, o TexConv imprime as opções usadas quando ele é executado, para ajudar a entender o que está fazendo. Consulte esta saída para obter detalhes.

## <a name="general-usage"></a>Uso geral

TexConv sempre produz **exatamente um** arquivo de saída. Ele pode usar **vários** arquivos de entrada para montar a saída de. Para o assembly, ele também precisa de um **mapeamento de canal**, que informa qual canal (*vermelho, verde, azul* ou *alfa*) deve ser levado de qual arquivo de entrada e movê-lo para o canal da imagem de saída.

A linha de comando mais direta é esta:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`Especifica o arquivo de saída e o formato
- `-in0`Especifica a primeira imagem de entrada
- `-rgba`informa que a imagem de saída deve usar todos os quatro canais e que eles devem ser levados a 1:1 da imagem de entrada

## <a name="multiple-input-files"></a>Vários arquivos de entrada

Para montar a saída de vários arquivos de entrada, especifique cada arquivo de entrada usando a `-in` opção com um número crescente:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Ao montar um cubemap de texturas 2D, também é possível usar `-right` , `-left` ,,, ou,,,, `-top` `-bottom` `-front` `-back` `-px` `-nx` `-py` `-ny` `-pz` , `-nz` .

Para mapear essas entradas para o arquivo de saída, é necessário um mapeamento de canal adequado.

## <a name="channel-mappings"></a>Mapeamentos de canal

As opções de mapeamento de canal especificam de qual entrada para preencher os canais de saída fornecidos. Você pode especificar a entrada para cada canal individualmente da seguinte maneira:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Aqui, os canais RGB da saída seriam preenchidos usando a primeira imagem de entrada, mas vermelho e azul serão trocados. O canal alfa da saída seria preenchido com os valores do canal vermelho da segunda imagem de entrada.

A especificação do mapeamento para cada canal separadamente proporciona a maior flexibilidade. Por conveniência, o mesmo pode ser escrito usando operadores "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canais de saída

As seguintes opções de mapeamento de canal estão disponíveis:

- `-r`,,, `-g` `-b` `-a` : Eles especificam atribuições de canal único
- `-rg`: Especifique as atribuições de canal vermelho e verde.
- `-rgb`: Especifique as atribuições de canal vermelho, verde e azul.
- `-rgba`: Especifica todas as quatro atribuições de canal.

Mencionando apenas o canal R, RG ou RGB, instrui o TexConv a criar um arquivo de saída com apenas 1, 2 ou 3 canais, respectivamente.

### <a name="input-swizzling"></a>Swizzling de entrada

Ao indicar qual textura de entrada deve preencher qual canal de saída, uma pode swizzle a entrada:

- `-rgba in0` equivale a `-rgba in0.rgba`
- `-rgba in0.bgra`irá swizzle os canais de entrada
- `-rgb in0.rrr`duplicará o canal vermelho em todos os canais

Um deles também pode preencher canais com preto ou branco:

- `-rgb in0 -a white`criará uma textura de saída de 4 canais, mas definirá alfa como totalmente opaca
- `-rg black -b white`criará uma textura totalmente azul

## <a name="common-options"></a>Opções comuns

As opções mais interessantes estão listadas abaixo. Mais opções são listadas por `TexConv --help` .

### <a name="output-type"></a>Tipo de saída

- `-type 2D`: A saída será uma imagem 2D regular.
- `-type Cubemap`: A saída será uma imagem cubemap. Com suporte apenas para arquivos de saída de DDS. Quando isso é especificado, um pode montar o cubemap de 6 imagens de entrada 2D regulares.

### <a name="image-compression"></a>Compactação de imagem

- `-compression none`: A imagem de saída será descompactada.
- `-compression medium`: Se houver suporte, a imagem de saída usará a compactação sem sacrificar muita qualidade.
- `-compression high`: Se houver suporte, a imagem de saída usará a compactação e prejudicará a qualidade em favor de um arquivo menor.

### <a name="mipmaps"></a>Mipmaps

Por padrão, TexConv gera mipmaps quando o formato de saída dá suporte a ela.

- `-mipmaps none`: Mipmaps não será gerado.
- `-mipmaps Linear`: Se houver suporte, mipmaps será gerado usando um filtro de caixa.

### <a name="usage-srgb--gamma-correction"></a>Uso (correção de sRGB/gama)

A `-usage` opção especifica a finalidade da saída e, portanto, informa ao TexConv se a correção gama deve ser aplicada aos arquivos de entrada e saída. O uso afeta apenas os canais RGB. O canal alfa é sempre considerado para conter valores ' lineares '. Se o uso não for especificado, o modo ' auto ' tentará detectar o uso do formato e do nome do arquivo da primeira imagem de entrada. Por exemplo, os formatos de saída de canal único e duplo são sempre lineares. Verifique a saída para ver qual decisão TexConv tomada.

- `-usage Linear`: A imagem de saída contém valores que não representam cores. Normalmente, esse é o caso de texturas metálicas e de irregularidades, bem como todos os tipos de máscaras.

- `-usage Color`: A imagem de saída representa a cor, como mapas difuso/albedo. O sinalizador sRGB será definido no cabeçalho DDS de saída.

- `-usage HDR`: O arquivo de saída deve usar mais de 8 bits por pixel para codificação. Consequentemente, todos os valores são armazenados em espaço linear. Para texturas HDR, não importa se os dados representam cor ou outros dados.

- `-usage NormalMap`: A imagem de saída representa um mapa normal de espaço tangente. Os valores serão normalizados e a computação mipmap será otimizada ligeiramente.

- `-usage NormalMap_Inverted`: A saída é um mapa normal de espaço tangente com Y apontando na direção oposta do que a entrada.

### <a name="image-rescaling"></a>Redimensionamento de imagem

- `-minRes 64`: Especifica a resolução mínima da saída. Se a imagem de entrada for menor, ela será dimensionada.
- `-maxRes 1024`: Especifica a resolução máxima da saída. Se a imagem de entrada for maior, ela receberá downscaled.
- `-downscale 1`: Se for maior que 0, as imagens de entrada serão divididas em uma resolução N vezes. Use isso para aplicar uma redução de qualidade geral.

## <a name="examples"></a>Exemplos

### <a name="convert-a-color-texture"></a>Converter uma textura de cor

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Converter um mapa normal

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Criar um cubemap HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Uma grande fonte para cubemaps HDR é [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Distortar várias imagens em uma

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrair um único canal

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
