---
title: Idioma de consulta
titleSuffix: Azure Digital Twins
description: Entenda os conceitos básicos da linguagem de Repositório de Consultas gêmeos do Azure digital.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6da539ccd8ad293aed402a4a6d130b6701e7b9c2
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187108"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para o gêmeos digital do Azure

Lembre-se de que o centro do Azure digital gêmeos é o [**gráfico de entrelaçamento**](concepts-twins-graph.md), construído de gêmeos e **relações** **digitais** . Esse grafo pode ser consultado para obter informações sobre o gêmeos digital e as relações que ele contém. Essas consultas são gravadas em uma linguagem de consulta do tipo SQL personalizada chamada **linguagem de repositório de consultas do Azure digital gêmeos**.

Para enviar uma consulta ao serviço de um aplicativo cliente, você usará a **API de consulta**do gêmeos digital do Azure. Isso permite que os desenvolvedores gravem consultas e apliquem filtros para localizar conjuntos de gêmeos digitais no grafo de entrelaçamento e outras informações sobre o cenário do gêmeos digital do Azure.

## <a name="query-language-features"></a>Recursos de linguagem de consulta

O Azure digital gêmeos fornece recursos de consulta extensivos em relação ao grafo de entrelaçamento. As consultas são descritas usando a sintaxe do tipo SQL, em uma linguagem de consulta semelhante à [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-query-language.md) com muitos recursos comparáveis.

Aqui estão as operações disponíveis na linguagem Repositório de Consultas do Azure digital gêmeos:
* Obter gêmeos por propriedades de gêmeos digital.
* Obter gêmeos pelas interfaces do gêmeos digital.
* Obter gêmeos por propriedades de relação.
* Obter gêmeos em vários tipos de relação ( `JOIN` consultas). Há limitações no número de `JOIN` s permitido (um nível para visualização pública).
* Use a função personalizada `IS_OF_MODEL(twinCollection, twinTypeName)` , que permite a filtragem com base no [modelo](concepts-models.md)de entrelaçamento. Ele dá suporte à herança.
* Usar funções escalares:,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` , `STARTS_WITH` , `ENDS_WITH` .
* Usar operadores de comparação de consulta:,,,,, `IN` / `NIN` `=` `!=` `<` `>` `<=` , `>=` .
* Use qualquer combinação ( `AND` , `OR` , `NOT` operador) dos itens acima.
* Use a continuação: o objeto de consulta é instanciado com um tamanho de página (até 100). Você pode recuperar o digital gêmeos uma página por vez fornecendo o token de continuação em chamadas subsequentes para a API.

## <a name="next-steps"></a>Próximas etapas

Saiba como escrever consultas e ver exemplos de código de cliente em [como: consultar o grafo de entrelaçamento](how-to-query-graph.md).
