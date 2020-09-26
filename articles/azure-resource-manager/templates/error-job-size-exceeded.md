---
title: O tamanho do trabalho excedeu o erro
description: Descreve como solucionar erros quando o tamanho ou o modelo do trabalho é muito grande.
ms.topic: troubleshooting
ms.date: 09/25/2020
ms.openlocfilehash: 06645561964d9634d93061b3be4d100a578cc7e7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373069"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Resolução de erros para o tamanho do trabalho excedido

Este artigo descreve como resolver os erros **JobSizeExceededException** e **DeploymentSizeExceededException** .

## <a name="symptom"></a>Sintoma

Ao implantar um modelo, você receberá um erro informando que a implantação excedeu os limites.

## <a name="cause"></a>Causa

Você pode obter esse erro quando o tamanho do seu modelo exceder 4 MB. O limite de 4 MB se aplica ao estado final do modelo depois que ele é expandido para definições de recursos que usam [Copy](copy-resources.md) para criar várias instâncias. O estado final também inclui os valores resolvidos para variáveis e parâmetros.

O trabalho de implantação também inclui metadados sobre a solicitação. Para modelos grandes, os metadados combinados com o modelo podem exceder o tamanho permitido para um trabalho.

Outros limites para o modelo são:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

## <a name="solution-1---simplify-template"></a>Solução 1 – simplificar o modelo

Sua primeira opção é simplificar o modelo. Essa opção funciona quando o modelo implanta muitos tipos diferentes de recursos. Considere dividir o modelo em [modelos vinculados](linked-templates.md). Divida os tipos de recursos em grupos lógicos e adicione um modelo vinculado para cada grupo. Por exemplo, se você precisar implantar muitos recursos de rede, poderá mover esses recursos para um modelo vinculado.

Você pode definir outros recursos como dependentes do modelo vinculado e [obter valores da saída do modelo vinculado](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---use-serial-copy"></a>Solução 2 – usar a cópia serial

Sua segunda opção é alterar o loop de cópia de [paralelo para processamento serial](copy-resources.md#serial-or-parallel). Use esta opção somente quando você suspeitar de que o erro vem da implantação de um grande número de recursos por meio de cópia. Essa alteração pode aumentar significativamente o tempo de implantação porque os recursos não são implantados em paralelo.
