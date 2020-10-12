---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569264"
---
O fuso horário padrão usado com as expressões CRON é a Hora Universal Coordenada (UTC). Para que a expressão CRON se baseie em outro fuso horário, crie uma configuração de aplicativo para o aplicativo de funções denominada `WEBSITE_TIME_ZONE`. 

O valor dessa configuração depende do sistema operacional e do plano no qual seu aplicativo de funções é executado.

|Sistema operacional |Plano |Valor |
|-|-|-|
| **Windows** |Todos | Defina o valor para o nome do fuso horário desejado, conforme mostrado no [Índice de fuso horário da Microsoft](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). |
| **Linux** |Premium<br/>Dedicado |Defina o valor para o nome do fuso horário desejado, conforme mostrado no [banco de dados do TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` Não tem suporte no plano de consumo do Linux no momento.

Por exemplo, o *horário padrão do leste* (Windows) ou *América/New_York* (Linux) é o UTC-05:00. Para que o gatilho do temporizador seja acionado às 10:00 AM EST todos os dias, use a seguinte expressão NCRONTAB que conta para o fuso horário UTC:

```
"0 0 15 * * *"
``` 

Ou crie uma configuração de aplicativo para seu aplicativo de funções chamado `WEBSITE_TIME_ZONE` , defina o valor para `Eastern Standard Time` (Windows) ou `America/New_York` (Linux) e, em seguida, use a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando você usa `WEBSITE_TIME_ZONE`, o horário é ajustado para as alterações de hora no fuso horário específico, como o horário de verão. 
