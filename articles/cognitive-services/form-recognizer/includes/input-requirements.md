---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379411"
---
O Reconhecimento de Formulários funciona em documentos de entrada que atendem aos seguintes requisitos:

* O formato deve ser JPG, PNG, PDF (texto ou digitalizado) ou TIFF. PDFs com texto inserido são o melhor porque não há nenhuma possibilidade de erro na extração e na localização de caracteres.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* Documentos PDF e TIFF devem ter 200 páginas ou menos e o tamanho total do conjunto de dados de treinamento deve ser de 500 páginas ou menos.
* Para imagens, as dimensões devem estar entre 600 x 100 pixels e 4200 x 4200 pixels.
* Se documentos em papel forem digitalizados, os formulários deverão ser digitalizações de alta qualidade.
* O texto precisa usar o alfabeto latino (caracteres em português).
* Para aprendizado não supervisionado (sem dados rotulados), os dados devem conter chaves e valores.
* Para aprendizado não supervisionado (sem dados rotulados), as chaves devem aparecer acima ou à esquerda dos valores. Não podem aparecer abaixo nem à direita.

Atualmente o Reconhecimento de Formulários não dá suporte aos seguintes tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, células ou cabeçalhos mesclados e assim por diante).
* Caixas de seleção ou botões de opção.
