---
author: baanders
description: arquivo de inclusão para operações de consulta do Azure digital gêmeos
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486639"
---
## <a name="query-language-features"></a>Recursos de linguagem de consulta

O Azure digital gêmeos fornece recursos de consulta extensivos em relação ao grafo de entrelaçamento. As consultas são descritas usando a sintaxe do tipo SQL, em uma linguagem de consulta semelhante à [linguagem de consulta do Hub IOT](../articles/iot-hub/iot-hub-devguide-query-language.md) com muitos recursos comparáveis.

> [!NOTE]
> Todas as operações de consulta do Azure digital gêmeos diferenciam maiúsculas de minúsculas.

Aqui estão as operações disponíveis na linguagem Repositório de Consultas do Azure digital gêmeos.

Obter gêmeos digital por seus...
* modelo (usando o `IS_OF_MODEL` operador)
* Propriedades (incluindo [Propriedades de marca](../articles/digital-twins/how-to-use-tags.md))
* interfaces
* relacionamentos
  - Propriedades das relações

Você pode aprimorar ainda mais suas consultas com as seguintes operações:
* Obter gêmeos em vários tipos de relação ( `JOIN` consultas). 
  - Há limitações no número de `JOIN` s permitido (um nível para visualização pública).
* Selecionar somente os resultados da consulta superior ( `Select TOP` operador)
* Usar funções escalares:,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Usar operadores de comparação de consulta:,,,,, `IN` / `NIN` `=` `!=` `<` `>` `<=` , `>=` .
* Use qualquer combinação ( `AND` , `OR` , `NOT` operador) de `IS_OF_MODEL` , funções escalares e operadores de comparação.
* Use a continuação: o objeto de consulta é instanciado com um tamanho de página (até 100). Você pode recuperar o digital gêmeos uma página por vez fornecendo o token de continuação em chamadas subsequentes para a API.