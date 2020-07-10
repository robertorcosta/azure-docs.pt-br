---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165644"
---
O fuso horário padrão usado com as expressões CRON é a Hora Universal Coordenada (UTC). Para que a expressão CRON se baseie em outro fuso horário, crie uma configuração de aplicativo para o aplicativo de funções denominada `WEBSITE_TIME_ZONE`. 

O valor dessa configuração depende do sistema operacional e do plano no qual seu aplicativo de funções é executado.

|Sistema operacional |Plano |Valor |
|-|-|-|
| **Windows** |Tudo | Defina o valor para o nome do fuso horário desejado, conforme mostrado no [índice de fuso horário da Microsoft] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Premium<br/>Dedicado |Defina o valor para o nome do fuso horário desejado, conforme mostrado no [banco de dados do TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`Não tem suporte no plano de consumo do Linux no momento.

Por exemplo, o *horário padrão do leste* (Windows) ou *América/New_York* (Linux) é o UTC-05:00. Para que o gatilho do temporizador seja acionado às 10:00 AM EST todos os dias, use a seguinte expressão NCRONTAB que conta para o fuso horário UTC:

```
"0 0 15 * * *"
``` 

Ou crie uma configuração de aplicativo para seu aplicativo de funções chamado `WEBSITE_TIME_ZONE` , defina o valor para `Eastern Standard Time` (Windows) ou `America/New_York` (Linux) e, em seguida, use a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando você usa `WEBSITE_TIME_ZONE`, o horário é ajustado para as alterações de hora no fuso horário específico, como o horário de verão. 
