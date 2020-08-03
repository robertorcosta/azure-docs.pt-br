---
title: Funções nas consultas de log do Azure Monitor | Microsoft Docs
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 155f27b9f04db74955640aad8f582fdf05539368
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511931"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Usar funções nas consultas de log no Azure Monitor

Para usar uma consulta de log com outra consulta, você pode salvá-la como uma função. Isso permite que você simplifique consultas complexas, dividindo-os em partes e permite a reutilização de código comum com várias consultas.

## <a name="create-a-function"></a>Criar uma função

Crie uma função com o Log Analytics no portal do Azure clicando em **Salvar** e, em seguida, fornecendo as informações na tabela a seguir.

| Configuração | Descrição |
|:---|:---|
| Nome           | Nome de exibição para a consulta no **Gerenciador de consultas**. |
| Salvar como        | Função |
| Alias da função | Nome curto para usar a função em outras consultas. Não pode conter espaços e deve ser exclusivo. |
| Categoria       | Uma categoria para organizar consultas salvas e funções na **Explorador de consultas**. |




## <a name="use-a-function"></a>Use uma função
Use uma função, incluindo seu alias em outra consulta. Pode ser usado como qualquer outra tabela.

## <a name="function-parameters"></a>Parâmetros de função 
Você pode adicionar parâmetros a uma função para que você possa fornecer valores para determinadas variáveis ao chamá-lo. A única maneira de criar atualmente uma função com parâmetros é usar um modelo do Resource Manager. Consulte [exemplos de modelo do Resource Manager para consultas de log no Azure monitor](../samples/resource-manager-log-queries.md#parameterized-function) para obter um exemplo.

## <a name="example"></a>Exemplo
A consulta padrão a seguir retorna todas as atualizações de segurança faltantes relatadas no último dia. Salve essa consulta como função com o alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Criar outra consulta e referência na função _security_updates_last_day_ ao procurar por atualizações de segurança necessárias relacionadas ao SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Próximas etapas
Confira outras lições para escrever consultas de log no Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Gráficos](charts.md)
