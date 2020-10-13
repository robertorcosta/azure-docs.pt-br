---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 5de121a1a905a58f8b5eaf8e60c1f8da71ee8cd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276928"
---
O Reconhecimento de Formulários funciona em documentos de entrada que atendem aos seguintes requisitos:

* O formato deve ser JPG, PNG, PDF (texto ou digitalizado) ou TIFF. PDFs com texto inserido são o melhor porque não há nenhuma possibilidade de erro na extração e na localização de caracteres.
* O tamanho do arquivo deve ser inferior a 20 MB.
* As imagens de dimensões devem estar entre 50 x 50 pixels e 10.000 x 10.000 pixels.
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou A3 e menores.
* Para PDF e TIFF, somente as primeiras 200 páginas são processadas (com uma assinatura de camada gratuita, somente as duas primeiras páginas são processadas).
* O tamanho total do conjunto de dados de treinamento deve ser de até 500 páginas.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* Se documentos em papel forem digitalizados, os formulários deverão ser digitalizações de alta qualidade.
* O texto precisa usar o alfabeto latino (caracteres em português).
* Para aprendizado não supervisionado (sem dados rotulados), os dados devem conter chaves e valores.
* Para aprendizado não supervisionado (sem dados rotulados), as chaves devem aparecer acima ou à esquerda dos valores. Não podem aparecer abaixo nem à direita.

Atualmente o Reconhecimento de Formulários não dá suporte aos seguintes tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, células ou cabeçalhos mesclados e assim por diante).
* Caixas de seleção ou botões de opção.
