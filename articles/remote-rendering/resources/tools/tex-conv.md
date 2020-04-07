---
title: TexConv - Ferramenta de conversão de texturas
description: Manual do usuário para a ferramenta de linha de comando TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680018"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Ferramenta de conversão de texturas

TexConv é uma ferramenta de linha de comando para processar texturas de formatos típicos de entrada como PNG, TGA, JPEG e DDS em formatos otimizados para consumo em tempo de execução.
Embora o cenário mais comum seja `A.xxx` converter um `B.yyy`único arquivo de entrada em um formato otimizado, a ferramenta tem muitas opções adicionais para usos avançados.

## <a name="command-line-help"></a>Ajuda de linha de comando

A execução do TexConv.exe com o `--help` parâmetro listará todas as opções disponíveis. Além disso, a TexConv imprime as opções usadas quando é executada, para ajudar a entender o que está fazendo. Consulte esta saída para obter detalhes.

## <a name="general-usage"></a>Uso geral

TexConv sempre produz exatamente um arquivo **de saída.** Ele pode usar vários arquivos de **entrada** para montar a saída a partir de. Para a montagem, ele também precisa de um **mapeamento de canal**, que lhe diz qual canal *(Vermelho, Verde, Azul* ou *Alfa*) para tirar de qual arquivo de entrada e movê-lo para qual canal da imagem de saída.

A linha de comando mais direta é a seguinte:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`especifica o arquivo de saída e o formato
- `-in0`especifica a primeira imagem de entrada
- `-rgba`diz que a imagem de saída deve usar todos os quatro canais e que eles devem ser tirados 1:1 da imagem de entrada

## <a name="multiple-input-files"></a>Vários arquivos de entrada

Para montar a saída a partir de vários `-in` arquivos de entrada, especifique cada arquivo de entrada usando a opção com um número crescente:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Ao montar um cubo de texturas 2D, `-left` `-top`também `-bottom` `-front`pode-se `-nx` `-py`usar `-ny` `-right` `-pz`, `-nz`, , , ou `-back` `-px`, , , , , , . .

Para mapear essas entradas no arquivo de saída, é necessário um mapeamento de canal adequado.

## <a name="channel-mappings"></a>Mapeamentos de canais

As opções de mapeamento de canais especificam a partir de qual entrada para preencher os canais de saída dado. Você pode especificar a entrada para cada canal individualmente assim:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Aqui os canais RGB da saída seriam preenchidos usando a primeira imagem de entrada, mas vermelho e azul serão trocados. O canal alfa da saída seria preenchido com os valores do canal vermelho da segunda imagem de entrada.

Especificar o mapeamento para cada canal separadamente dá a maior flexibilidade. Para conveniência, o mesmo pode ser escrito usando operadores "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canais de saída

As seguintes opções de mapeamento de canais estão disponíveis:

- `-r`, `-g` `-b`, `-a` : Estes especificam atribuições de canal único
- `-rg`: Especificar as atribuições do canal vermelho e verde.
- `-rgb`: Especificar as atribuições do canal vermelho, verde e azul.
- `-rgba`: Especifica todas as quatro atribuições do canal.

Mencionando apenas o canal R, RG ou RGB, instrui a TexConv a criar um arquivo de saída com apenas 1, 2 ou 3 canais, respectivamente.

### <a name="input-swizzling"></a>Swizzling de entrada

Ao indicar qual textura de entrada deve preencher qual canal de saída, pode-se girar a entrada:

- `-rgba in0` equivale a `-rgba in0.rgba`
- `-rgba in0.bgra`vai girar os canais de entrada
- `-rgb in0.rrr`vai duplicar o canal vermelho em todos os canais

Também é de se preencher canais com preto ou branco:

- `-rgb in0 -a white`criará uma textura de saída de 4 canais, mas definir alfa para totalmente opaco
- `-rg black -b white`criará uma textura totalmente azul

## <a name="common-options"></a>Opções comuns

As opções mais interessantes estão listadas abaixo. Mais opções `TexConv --help`são listadas por .

### <a name="output-type"></a>Tipo de saída

- `-type 2D`: A saída será uma imagem 2D regular.
- `-type Cubemap`: A saída será uma imagem cubemap. Apenas suportado para arquivos de saída DDS. Quando isso é especificado, pode-se montar o cubemap a partir de 6 imagens de entrada 2D regulares.

### <a name="image-compression"></a>Compactação de imagem

- `-compression none`: A imagem da saída será descompactada.
- `-compression medium`: Se suportado, a imagem de saída usará compressão sem sacrificar muita qualidade.
- `-compression high`: Se suportado, a imagem de saída usará a qualidade de compactação e sacrifício em favor de um arquivo menor.

### <a name="mipmaps"></a>Mipmaps

Por padrão, o TexConv gera mipmaps quando o formato de saída o suporta.

- `-mipmaps none`: Não serão gerados Mipmaps.
- `-mipmaps Linear`: Se suportado, os mipmaps serão gerados usando um filtro de caixa.

### <a name="usage-srgb--gamma-correction"></a>Utilização (correção sRGB/gama)

A `-usage` opção especifica o propósito da saída e, portanto, informa a TexConv se deve aplicar correção gama aos arquivos de entrada e saída. O uso afeta apenas os canais RGB. O canal alfa é sempre considerado para conter valores 'lineares'. Se o uso não for especificado, o modo 'auto' tentará detectar o uso a partir do formato e nome do arquivo da primeira imagem de entrada. Por exemplo, formatos de saída de canal único e duplo são sempre lineares. Verifique a saída para ver que decisão a TexConv tomou.

- `-usage Linear`: A imagem de saída contém valores que não representam cores. Este é tipicamente o caso para texturas metálicas e rugosidade, bem como todos os tipos de máscaras.

- `-usage Color`: A imagem de saída representa cor, como mapas difusos/albedo. O sinalizador sRGB será definido no cabeçalho DDS de saída.

- `-usage HDR`: O arquivo de saída deve usar mais de 8 bits por pixel para codificação. Consequentemente, todos os valores são armazenados em espaço linear. Para texturas HDR não importa se os dados representam cor ou outros dados.

- `-usage NormalMap`: A imagem de saída representa um mapa normal do espaço tangente. Os valores serão normalizados e a computação mipmap será otimizada ligeiramente.

- `-usage NormalMap_Inverted`: A saída é um mapa normal de espaço tangente com Y apontando na direção oposta da entrada.

### <a name="image-rescaling"></a>Redimensionamento de imagem

- `-minRes 64`: Especifica a resolução mínima da saída. Se a imagem de entrada for menor, ela ficará upscale.
- `-maxRes 1024`: Especifica a resolução máxima da saída. Se a imagem de entrada for maior, ela será desampliada.
- `-downscale 1`: Se for maior que 0, as imagens de entrada serão reduzidas pela metade em tempos de resolução N. Use isso para aplicar uma redução geral da qualidade.

## <a name="examples"></a>Exemplos

### <a name="convert-a-color-texture"></a>Converta uma textura de cor

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Converta um mapa normal

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Criar um cubo HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Uma ótima fonte de cubos de HDR é [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Asse várias imagens em uma

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrair um único canal

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
