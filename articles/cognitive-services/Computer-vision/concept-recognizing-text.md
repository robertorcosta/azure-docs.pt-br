---
title: Reconhecimento de texto impresso e escrito à mão - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao reconhecimento de texto impresso e manuscrito em imagens usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220155"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconhecer texto impresso e manuscrito

O Computer Vision fornece uma série de serviços que detectam e extraem textos impressos ou manuscritos que aparecem nas imagens. Isso é útil em uma variedade de cenários, como tomada de notas, registros médicos, segurança e bancos. As três seções seguintes detalham três APIs de reconhecimento de texto diferentes, cada uma otimizada para diferentes casos de uso.

## <a name="read-api"></a>Leia a API

A API Read detecta conteúdo de texto em uma imagem usando nossos modelos de reconhecimento mais recentes e converte o texto identificado em um fluxo de caracteres legível por máquina. É otimizado para imagens de texto pesado (como documentos que foram digitalizados) e para imagens com muito ruído visual. Ele determinará qual modelo de reconhecimento usar para cada linha de texto, suportando imagens com texto impresso e manuscrito. A API Read é executada de forma assíncrona porque documentos maiores podem levar vários minutos para retornar um resultado.

A operação Read mantém os agrupamentos de linhas originais de palavras reconhecidas em sua produção. Cada linha vem com coordenadas de caixa delimitador, e cada palavra dentro da linha também tem suas próprias coordenadas. Se uma palavra foi reconhecida com baixa confiança, essa informação também é transmitida. Consulte os [docs de referência da API Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e [leia os docs de referência da API Preview](https://go.microsoft.com/fwlink/?linkid=2118322) para saber mais.

> [!NOTE]
> Este recurso está disponível apenas para texto em inglês e espanhol (preview).

### <a name="image-requirements"></a>Requisitos de imagem

A API Leia funciona com imagens que atendam aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, BMP, PDF ou TIFF.
- As dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels. As páginas PDF devem ter 17 x 17 polegadas ou menores.
- O tamanho do arquivo da imagem deve ser inferior a 20 megabytes (MB).

### <a name="limitations"></a>Limitações

Se você estiver usando uma assinatura de nível livre, a API Read só processará as duas primeiras páginas de um documento PDF ou TIFF. Com uma assinatura paga, ele processará até 200 páginas. Observe também que a API detectará um máximo de 300 linhas por página.

## <a name="ocr-optical-character-recognition-api"></a>API OCR (reconhecimento óptico de caracteres)

A API de reconhecimento óptico de caracteres (OCR) da Computer Vision é semelhante à API Read, mas é executada de forma sincronizada e não é otimizada para documentos grandes. Ele usa um modelo de reconhecimento anterior, mas funciona com mais idiomas; ver [suporte ao idioma](language-support.md#text-recognition) para uma lista completa dos idiomas suportados.

Se necessário, o OCR corrige a rotação do texto reconhecido, retornando o deslocamento de rotação em graus do eixo horizontal da imagem. O CR também fornece as coordenadas de quadro de cada palavra, como visto na ilustração a seguir.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/vision-overview-ocr.png)

Consulte os [docs de referência do OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API OCR funciona em imagens que atendem aos seguintes requisitos:

* A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP.
* O tamanho da imagem de entrada deve ser entre 50 x 50 e 4200 x 4200 pixels.
* O texto na imagem pode ser girado por um ângulo múltiplo de 90 graus mais um ângulo múltiplo de 40 graus.

### <a name="limitations"></a>Limitações

Em fotografias em que o texto é dominante, falsos positivos podem vir de palavras reconhecidas parcialmente. Em algumas fotografias, especialmente fotos sem qualquer texto, a precisão pode variar dependendo do tipo de imagem.

## <a name="recognize-text-api"></a>Reconhecer API de texto

> [!NOTE]
> A API Reconhecer Texto está sendo preterida em favor da API Leia. A API Read tem recursos semelhantes e é atualizada para lidar com arquivos PDF, TIFF e multipáginas.

A API Reconhecer Texto é semelhante ao OCR, mas executa de forma assíncrona e usa modelos de reconhecimento atualizados. Consulte os [docs de referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) de texto para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API Reconhecer texto funciona com imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG ou BMP.
- As dimensões da imagem devem estar entre 50 x 50 e 4200 x 4200 pixels.
- O tamanho do arquivo da imagem deve ser inferior a 4 megabytes (MB).

## <a name="limitations"></a>Limitações

A precisão das operações de reconhecimento de texto depende da qualidade das imagens. Os seguintes fatores podem causar uma leitura imprecisa:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de fonte artísticos.
* Tamanho de texto pequeno.
* Telas de fundo complexas, sombras, brilho sobre o texto ou distorção da perspectiva.
* Letras maiúsculas enormes ou ausentes no início das palavras.
* Texto subscrito, sobrescrito ou tachado.

## <a name="next-steps"></a>Próximas etapas

Siga o [texto Extrair (Leia)](./QuickStarts/CSharp-hand-text.md) para implementar o reconhecimento de texto em um aplicativo C# simples.
