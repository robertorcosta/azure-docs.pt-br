---
title: O que é o serviço de Detecção Facial?
titleSuffix: Azure Cognitive Services
description: O serviço de Detecção Facial dos Serviços Cognitivos do Azure fornece algoritmos que são usados para detectar, reconhecer e analisar rostos humanos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403464"
---
# <a name="what-is-the-azure-face-service"></a>O que é o serviço de Detecção Facial do Azure?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Detecção Facial dos Serviços Cognitivos do Azure fornece algoritmos para detectar, reconhecer e analisar rostos humanos em imagens. A capacidade para processar as informações faciais humanas é importante em muitos cenários de software diferentes. Os exemplos de cenários incluem segurança, interface do usuário natural, análise e gerenciamento de conteúdo de imagens, aplicativos móveis e robótica.

O serviço de Detecção Facial fornece várias funções diferentes, que são descritas nas seções a seguir.

## <a name="face-detection"></a>Detecção facial

O serviço de Detecção Facial detecta rostos humanos em uma imagem e retorna as coordenadas do retângulo dos locais. Opcionalmente, a detecção facial pode extrair uma série de atributos relacionados à face. Os exemplos incluem pose da cabeça, gênero, idade, emoção, pelos faciais e óculos.

> [!NOTE]
> O recurso de detecção facial também está disponível por meio da [API Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Caso deseje executar outras operações com os dados faciais, use o serviço de Detecção Facial, que é o serviço abordado neste artigo.

![Uma imagem de uma mulher e um homem, com retângulos desenhados em torno de seus rostos e informações de idade e gênero exibidas](./Images/Face.detection.jpg)

Para saber mais sobre a detecção facial, confira o artigo de conceitos [Detecção facial](concepts/face-detection.md). Consulte também a documentação de referência [Detectar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Verificação facial

A API de Verificação executa uma autenticação com relação a dois rostos detectados ou de um rosto detectado com relação a um objeto pessoa. Na prática, ela avalia se dois rostos pertencem à mesma pessoa. Essa capacidade pode ser útil em cenários de segurança. Para saber mais, confira o guia de conceitos [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência [API de Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API de Localização de Semelhantes compare um rosto selecionado com um conjunto de rostos elegíveis e o restringe a um conjunto menor de rostos que são semelhantes ao rosto selecionado. Dois modos de trabalho, matchPerson e matchFace, são compatíveis. O modo matchPerson retorna rostos semelhantes após fazer a filtragem pela mesma pessoa usando a [API de Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo matchFace ignora a filtragem pela mesma pessoa. Retorna uma lista de rostos elegíveis parecidos que podem ou não podem pertencer à mesma pessoa.

O exemplo a seguir mostra o rosto selecionado:

![Uma mulher sorrindo](./Images/FaceFindSimilar.QueryFace.jpg)

E estas imagens são os rostos elegíveis:

![Cinco imagens de pessoas sorrindo. As imagens a e b mostram a mesma pessoa.](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro rostos semelhantes, o modo matchPerson retorna a e b, que mostram a mesma pessoa que o rosto buscado. O modo matchFace retornará a, b, c e d&mdash;, exatamente quatro possibilidades, mesmo se algumas não forem a pessoa selecionada ou tiverem semelhança baixa. Para saber mais, confira o guia de conceitos [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência [API de Localização de Semelhantes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Agrupamento facial

A API de Grupo divide um conjunto de rostos desconhecidos em vários grupos com base na similaridade. Cada grupo é um devido subconjunto separado do conjunto original de rostos. Todas as faces em um grupo provavelmente pertencem à mesma pessoa. Pode haver vários grupos diferentes para uma única pessoa. Os grupos são diferenciados por outro fator, como a expressão, por exemplo. Para saber mais, confira o guia de conceitos [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência [API de Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identificação pessoal

A API de Identificação é usada para identificar um rosto detectado comparado com um banco de dados de pessoas. Esse recurso pode ser útil para marcação de imagem automática em software de gerenciamento de fotos. Crie o banco de dados com antecedência e edite-o ao longo do tempo.

A imagem a seguir mostra um exemplo de um banco de dados chamado `"myfriends"`. Cada grupo pode conter até 1 milhão de objetos person diferentes. Cada pessoa objeto pode ter até 248 faces registradas.

![Uma grade com três colunas para pessoas diferentes, cada uma com três linhas de imagens de rosto](./Images/person.group.clare.jpg)

Depois de criar e treinar um banco de dados, você poderá fazer a identificação em relação ao grupo com um novo rosto detectado. Se a face for identificada como uma pessoa no grupo, ela é retornada.

Para saber mais sobre a identificação da pessoa, confira o guia de conceitos [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência [API de Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="containers"></a>Contêineres

[Use o contêiner de Detecção Facial](face-how-to-install-containers.md) para detectar, reconhecer e identificar rostos, instalando um contêiner do Docker padronizado mais próximos aos seus dados.

## <a name="sample-apps"></a>Aplicativos de exemplo

Os seguintes aplicativos de exemplo mostram algumas maneiras de usar o serviço de Detecção Facial:

- [API de Detecção Facial: a Biblioteca de Clientes do Windows e amostra](https://github.com/Microsoft/Cognitive-Face-Windows) é um aplicativo WPF que demonstra vários cenários de detecção facial, análise e identificação.
- [Aplicativo UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes) é um aplicativo UWP (Plataforma Universal do Windows) que usa identificação facial com fala, Cortana, tinta e câmera em um cenário de compartilhamento de anotações em família.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os recursos dos Serviços Cognitivos, os desenvolvedores que usam o serviço de Detecção Facial devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Para saber mais, confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Siga um início rápido para implementar um cenário de detecção facial em código:

- [Início Rápido: detectar faces em uma imagem usando o SDK do .NET com C#](quickstarts/csharp.md). Outras linguagens estão disponíveis.
