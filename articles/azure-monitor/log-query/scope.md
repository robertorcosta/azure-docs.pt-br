---
title: Escopo de consulta de log no Azure Monitor Log Analytics | Microsoft Docs
description: Descreve o escopo e o intervalo de tempo para uma consulta de log no Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249588"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Escopo de consulta de log e intervalo de tempo no Azure Monitor Log Analytics
Quando você executa uma [consulta de log](log-query-overview.md) no Log [Analytics no portal Azure,](get-started-portal.md)o conjunto de dados avaliados pela consulta depende do escopo e do intervalo de tempo selecionado. Este artigo descreve o escopo e o intervalo de tempo e como você pode definir cada um, dependendo de seus requisitos. Também descreve o comportamento de diferentes tipos de escopos.


## <a name="query-scope"></a>Escopo da consulta
O escopo da consulta define os registros avaliados pela consulta. Isso geralmente incluirá todos os registros em um único espaço de trabalho do Log Analytics ou no aplicativo Application Insights. O Log Analytics também permite definir um escopo para um determinado recurso monitorado do Azure. Isso permite que um proprietário de recursos se concentre apenas em seus dados, mesmo que esse recurso seja gravado em vários espaços de trabalho.

O escopo é sempre exibido no canto superior esquerdo da janela Log Analytics. Um ícone indica se o escopo é um espaço de trabalho do Log Analytics ou um aplicativo Application Insights. Nenhum ícone indica outro recurso do Azure.

![Escopo](media/scope/scope.png)

O escopo é determinado pelo método que você usa para iniciar o Log Analytics e, em alguns casos, você pode alterar o escopo clicando nele. A tabela a seguir lista os diferentes tipos de escopo utilizados e diferentes detalhes para cada um.

| Escopo da consulta | Registros em escopo | Como selecionar | Alterando o escopo |
|:---|:---|:---|:---|
| Espaço de trabalho do Log Analytics | Todos os registros no espaço de trabalho do Log Analytics. | Selecione **Logs** no menu **Azure Monitor** ou no menu **Log Analytics workspaces.**  | Pode alterar o escopo para qualquer outro tipo de recurso. |
| Aplicativo Application Insights | Todos os registros no aplicativo Application Insights. | Selecione **'Análise'** na página **Visão geral** do Application Insights. | Só pode alterar o escopo para outro aplicativo do Application Insights. |
| Resource group | Registros criados por todos os recursos do grupo de recursos. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Logs** no menu do grupo de recursos. | Não é possível alterar o escopo.|
| Subscription | Registros criados por todos os recursos da assinatura. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Logs** no menu de assinatura.   | Não é possível alterar o escopo. |
| Outros recursos do Azure | Registros criados pelo recurso. Pode incluir dados de vários espaços de trabalho do Log Analytics.  | Selecione **Logs** no menu de recursos.<br>OU<br>Selecione **Logs** no menu **Do Monitor do Azure** e selecione um novo escopo. | Só pode alterar o escopo para o mesmo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitações quando escopo de um recurso

Quando o escopo da consulta é um espaço de trabalho do Log Analytics ou um aplicativo Application Insights, todas as opções no portal e todos os comandos de consulta estão disponíveis. Quando escopo de um recurso, porém, as seguintes opções no portal não estão disponíveis porque estão associadas a um único espaço de trabalho ou aplicativo:

- Salvar
- Gerenciador de consultas
- Nova regra de alerta

Você não pode usar os seguintes comandos em uma consulta quando escopo para um recurso, uma vez que o escopo de consulta já incluirá quaisquer espaços de trabalho com dados para esse recurso ou conjunto de recursos:

- [App](app-expression.md)
- [Espaço](workspace-expression.md)
 

## <a name="query-limits"></a>Limites de consulta
Você pode ter requisitos de negócios para um recurso do Azure para gravar dados em vários espaços de trabalho do Log Analytics. O espaço de trabalho não precisa estar na mesma região que o recurso, e um único espaço de trabalho pode coletar dados de recursos em uma variedade de regiões.  

Definir o escopo de um recurso ou conjunto de recursos é um recurso particularmente poderoso do Log Analytics, uma vez que permite consolidar automaticamente dados distribuídos em uma única consulta. Pode afetar significativamente o desempenho, porém, se os dados precisarem ser recuperados de espaços de trabalho em várias regiões do Azure.

O Log Analytics ajuda a proteger contra sobrecarga excessiva de consultas que abrangem espaços de trabalho em várias regiões, emitindo um aviso ou erro quando um determinado número de regiões está sendo usado. Sua consulta receberá um aviso se o escopo incluir espaços de trabalho em 5 ou mais regiões. ele ainda vai correr, mas pode levar tempo excessivo para completar.

![Aviso de consulta](media/scope/query-warning.png)

Sua consulta será bloqueada se o escopo incluir espaços de trabalho em 20 ou mais regiões. Neste caso, você será solicitado a reduzir o número de regiões do espaço de trabalho e tentar executar a consulta novamente. A parada exibirá todas as regiões no escopo da consulta, e você deve reduzir o número de regiões antes de tentar executar a consulta novamente.

![Falha na consulta](media/scope/query-failed.png)


## <a name="time-range"></a>Intervalo de horas
O intervalo de tempo especifica o conjunto de registros avaliados para a consulta com base em quando o registro foi criado. Isso é definido por uma propriedade padrão em cada registro no espaço de trabalho ou aplicativo, conforme especificado na tabela a seguir.

| Location | Propriedade |
|:---|:---|
| Espaço de trabalho do Log Analytics          | TimeGenerated |
| Aplicativo Application Insights |  timestamp     |

Defina o intervalo de tempo selecionando-o no seletor de tempo na parte superior da janela Log Analytics.  Você pode selecionar um período predefinido ou selecionar **Personalizado** para especificar um intervalo de tempo específico.

![Seletor de tempo](media/scope/time-picker.png)

Se você definir um filtro na consulta que usa a propriedade de tempo padrão como mostrado na tabela acima, o seletor de tempo será alterado para **Definir em consulta**e o seletor de tempo será desativado. Neste caso, é mais eficiente colocar o filtro na parte superior da consulta, de modo que qualquer processamento subseqüente só precisa trabalhar com os registros filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Se você usar o comando [workspace](workspace-expression.md) ou [app](app-expression.md) para recuperar dados de outro espaço de trabalho ou aplicativo, o seletor de tempo pode se comportar de forma diferente. Se o escopo for um espaço de trabalho do Log Analytics e você usar **o aplicativo,** ou se o escopo for um aplicativo application Insights e você usar **o espaço de trabalho,** então o Log Analytics pode não entender que a propriedade usada no filtro deve determinar o filtro de tempo.

No exemplo a seguir, o escopo é definido como um espaço de trabalho do Log Analytics.  A consulta usa **espaço de trabalho** para recuperar dados de outro espaço de trabalho do Log Analytics. O seletor de tempo muda para **Definir em consulta** porque ele vê um filtro que usa a propriedade **TimeGenerated** esperada.

![Consulta com espaço de trabalho](media/scope/query-workspace.png)

Se a consulta usar o **aplicativo** para recuperar dados de um aplicativo Application Insights, porém, o Log Analytics não reconhece a propriedade do **carimbo de tempo** no filtro e o seletor de tempo permanece inalterado. Neste caso, ambos os filtros são aplicados. No exemplo, apenas registros criados nas últimas 24 horas são incluídos na consulta, embora especifique 7 dias na cláusula **onde.**

![Consulta com aplicativo](media/scope/query-app.png)

## <a name="next-steps"></a>Próximas etapas

- Faça um [tutorial sobre como usar o Log Analytics no portal Azure](get-started-portal.md).
- Passe por um [tutorial sobre como escrever consultas.](get-started-queries.md)
