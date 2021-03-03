---
title: Idioma bicep para modelos de Azure Resource Manager
description: Descreve a linguagem bicep para implantar a infraestrutura no Azure por meio de modelos de Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744311"
---
# <a name="what-is-bicep-preview"></a>O que é o bicep (versão prévia)?

Bicep é uma linguagem para a implantação declarativa de recursos do Azure. Ele simplifica a experiência de criação fornecendo uma sintaxe concisa e melhor suporte para modularidade e reutilização de código. Bicep é uma DSL (linguagem específica de domínio), o que significa que ele foi projetado para um determinado cenário ou domínio. O bicep não é destinado a uma linguagem de programação geral para escrever aplicativos.

Bicep é uma abstração transparente sobre modelos de Azure Resource Manager (modelos ARM). Cada arquivo bicep é compilado em um modelo ARM padrão. Os tipos de recursos, as versões de API e as propriedades que são válidas em um modelo ARM são válidas em um arquivo bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>Introdução

Para começar com o bicep, [Instale as ferramentas](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Depois de instalar as ferramentas, experimente o [tutorial do bicep](./bicep-tutorial-create-first-bicep.md). A série de tutoriais orienta você pela estrutura e pelos recursos do bicep. Você implanta arquivos bicep e converte um modelo ARM no arquivo bicep equivalente.

Para exibir arquivos JSON e bicep equivalentes lado a lado, consulte [bicep playground](https://aka.ms/bicepdemo).

Se você tiver um modelo ARM existente que deseja converter em bicep, consulte [descompilar JSON para bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>Aprimoramentos do bicep

O bicep oferece uma sintaxe mais fácil e concisa quando comparado ao JSON equivalente. Você não usa `[...]` expressões. Em vez disso, você chama diretamente funções, obtém valores de parâmetros e variáveis e recursos de referência. Para obter uma comparação completa da sintaxe, consulte comparando [JSON e bicep para modelos](compare-template-syntax.md).

O bicep gerencia automaticamente as dependências entre os recursos. Você pode evitar `dependsOn` a configuração quando o nome simbólico de um recurso é usado em outra declaração de recurso.

Com o bicep, você pode dividir o projeto em vários módulos.

A estrutura do arquivo bicep é mais flexível do que o modelo JSON. Você pode declarar parâmetros, variáveis e saídas em qualquer lugar no arquivo. No JSON, você precisa declarar todos os parâmetros, variáveis e saídas dentro das seções correspondentes do modelo.

A extensão de VS Code para bicep oferece validação mais rica e IntelliSense. Por exemplo, a extensão tem o IntelliSense para obter as propriedades de um recurso.

## <a name="faq"></a>Perguntas frequentes

**Por que criar um novo idioma em vez de usar um existente?**

Você pode considerar o bicep como uma revisão para a linguagem de modelo ARM existente em vez de uma nova linguagem. A sintaxe foi alterada, mas a funcionalidade principal e o tempo de execução permanecem os mesmos.

Antes de desenvolver o bicep, consideramos o uso de uma linguagem de programação existente. Decidimos que nosso público-alvo acharia mais fácil aprender a bicep em vez de começar a usar outra linguagem.

**Por que não concentrar sua energia no Terraform ou outra infraestrutura de terceiros como ofertas de código?**

Usuários diferentes preferem linguagens de configuração e ferramentas diferentes. Queremos ter certeza de que todas essas ferramentas fornecem uma ótima experiência no Azure. Bicep faz parte desse esforço.

Se você estiver satisfeito usando Terraform, não há motivo para mudar. A Microsoft está comprometida em garantir que o Terraform no Azure seja o melhor possível.

Para clientes que selecionaram modelos de ARM, acreditamos que o bicep melhora a experiência de criação. O bicep também ajuda com a transição para clientes que não adotaram a infraestrutura como código.

**O é bicep somente para o Azure?**

Bicep é uma DSL concentrada na implantação de soluções completas no Azure. A reunião dessa meta requer o trabalho com algumas APIs que estão fora do Azure. Esperamos fornecer pontos de extensibilidade para esses cenários.

**O que acontece com meus modelos de ARM existentes?**

Eles continuam a funcionar exatamente como sempre têm. Você não precisa fazer nenhuma alteração. Continuaremos a dar suporte à linguagem JSON de modelo ARM subjacente. Os arquivos bicep são compilados em JSON e esse JSON é enviado para o Azure para implantação.

Quando estiver pronto, você poderá [converter os arquivos JSON em bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Próximas etapas

Introdução ao tutorial do [bicep](./bicep-tutorial-create-first-bicep.md).
