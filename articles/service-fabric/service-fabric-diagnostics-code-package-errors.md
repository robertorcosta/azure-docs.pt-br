---
title: Diagnosticar erros comuns do pacote de código usando o Service Fabric
description: Saiba como solucionar erros comuns do pacote de código com o Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463104"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnosticar erros comuns do pacote de código usando o Service Fabric

Este artigo descreve o que significa que um pacote de código terminar inesperadamente. Ele fornece informações sobre possíveis causas de códigos de erro comuns, juntamente com etapas de solução de problemas.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando um processo ou contêiner termina inesperadamente?

Quando o Azure Service Fabric recebe uma solicitação para iniciar um pacote de código, ele começa a preparar o ambiente no sistema local de acordo com as opções definidas nos manifestos app e serviço. Essas preparações podem incluir reservar pontos finais ou recursos de rede, configurar regras de firewall ou configurar restrições de governança de recursos. 

Depois que o ambiente foi configurado corretamente, o Service Fabric tenta trazer o pacote de código. Esta etapa é considerada bem-sucedida se o sistema operacional ou o tempo de execução do contêiner informarem que o processo ou o contêiner foi ativado com sucesso. Se a ativação não for bem sucedida, você deve ver uma mensagem de saúde no SFX que se assemelha ao seguinte:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Depois que o pacote de código foi ativado com sucesso, o Service Fabric começa a monitorar sua vida útil. Neste ponto, um processo ou contêiner pode terminar a qualquer momento por uma série de razões. Por exemplo, ele pode ter falhado em inicializar uma DLL, ou o sistema operacional poderia ter ficado sem espaço de pilha de desktop. Se o pacote de código for encerrado, você deve ver a seguinte mensagem de saúde no SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

O código de saída nesta mensagem de saúde é a única pista que o processo ou contêiner fornece sobre por que ele terminou. Pode ser gerado por qualquer nível da pilha. Por exemplo, este código de saída pode estar relacionado a um erro do SISTEMA OPERACIONAL ou a um problema .NET, ou pode ter sido levantado pelo seu código. Use este artigo como ponto de partida para diagnosticar a fonte de códigos de saída de rescisão e possíveis soluções. Mas tenha em mente que essas são soluções gerais para cenários comuns e podem não se aplicar ao erro que você está vendo.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Como posso dizer se a Service Fabric encerrou meu pacote de código?

A Service Fabric pode ser responsável por encerrar seu pacote de código por uma variedade de razões. Por exemplo, ele pode decidir colocar o pacote de código em outro nó para fins de balanceamento de carga. Você pode verificar se a Service Fabric encerrou seu pacote de código se você ver qualquer um dos códigos de saída na tabela a seguir.

>[!NOTE]
> Se o seu processo ou contêiner terminar com um código de saída diferente dos códigos na tabela a seguir, a Service Fabric não será responsável por terminá-lo.

Código de saída | Descrição
--------- | -----------
7147 | Indica que o Service Fabric desligou graciosamente o processo ou o recipiente enviando-o com um sinal Ctrl+C.
7148 | Indica que a Service Fabric encerrou o processo ou o contêiner. Às vezes, este código de erro indica que o processo ou contêiner não respondeu em tempo hábil após o envio de um sinal Ctrl+C, e teve que ser encerrado.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Outros códigos de erro comuns e suas possíveis correções

Código de saída | Valor hexadecimal | Descrição breve | Causa raiz | Correção potencial
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Este erro às vezes significa que a máquina ficou sem espaço de pilha de desktop. Essa causa é especialmente provável se você tiver inúmeros processos que pertencem ao seu aplicativo em execução no nó. | Se o programa não foi construído para responder aos sinais Ctrl+C, você pode ativar a configuração **EnableActivateNoWindow** no manifesto Cluster. Ativar essa configuração significa que o pacote de código será executado sem uma janela de GUI e não receberá sinais Ctrl+C. Essa ação também reduz a quantidade de espaço de pilha de desktop que cada processo consome. Se o seu pacote de código precisar receber sinais Ctrl+C, você pode aumentar o tamanho do heap de desktop do seu nó.
3762504530 | 0xe0434352 | N/D | Este valor representa o código de erro para uma exceção não tratada do código gerenciado (ou seja, .NET). | Este código de saída indica que seu aplicativo levantou uma exceção que permanece não tratada e que encerrou o processo. Como o primeiro passo para determinar o que desencadeou esse erro, depurar os registros do aplicativo e desovar arquivos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o diagnóstico de outros cenários comuns.](service-fabric-diagnostics-common-scenarios.md)
* Obtenha uma visão geral mais detalhada dos registros do Monitor Do Azure e do que eles oferecem lendo [a visão geral do Azure Monitor](../operations-management-suite/operations-management-suite-overview.md).
* Saiba mais sobre os registros do Azure Monitor [alertando](../log-analytics/log-analytics-alerts.md) para ajuda na detecção e diagnóstico.
* Familiarize-se com os recursos de [pesquisa e consulta de log](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs do Monitor Do Azure.
