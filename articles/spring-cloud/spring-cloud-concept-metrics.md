---
title: Noções básicas sobre métricas do Azure Spring Cloud
description: Saiba como revisar métricas no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256751"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Entenda as métricas para Azure Spring Cloud

O Azure Metrics explorer é um componente do portal Microsoft Azure que permite traçar gráficos, correlacionar visualmente tendências e investigar picos e mergulhos em métricas. Use o Metrics Explorer para investigar a integridade e o uso dos recursos. 

No Azure Spring Cloud, existem dois pontos de vista para métricas.
* Gráficos em cada página de visão geral do aplicativo
* Página de métricas comuns

 ![Gráficos de métricas](media/metrics/metrics-1.png)

Gráficos na **visão geral** do aplicativo fornecem verificações rápidas de status para cada aplicativo. A página **Métricas comuns** contém todas as métricas disponíveis para referência. Você pode construir seus próprios gráficos na página de métricas comuns e fixá-los no Dashboard.

## <a name="application-overview-page"></a>Página de visão geral do aplicativo
Selecione um aplicativo no **Gerenciamento de Aplicativos** para encontrar gráficos na página de visão geral.  

 ![Gerenciamento de métricas de aplicativos](media/metrics/metrics-2.png)

A página **Visão Geral do Aplicativo** de cada aplicativo apresenta um gráfico de métricas que permite que você realize uma rápida verificação de status do seu aplicativo.  

 ![Visão geral das métricas do aplicativo](media/metrics/metrics-3.png)

O Azure Spring Cloud fornece esses cinco gráficos com métricas atualizadas a cada minuto:

* **Erros de servidor http**: contagem de erros para solicitações HTTP para o seu aplicativo
* **Dados Em**: Bytes recebidos pelo seu aplicativo
* **Data Out**: Bytes enviados pelo seu aplicativo
* **Solicitações**: Solicitações recebidas pelo seu aplicativo
* **Tempo médio de resposta**: Tempo médio de resposta do seu aplicativo

Para o gráfico, você pode selecionar um intervalo de tempo de uma hora a sete dias.

## <a name="common-metrics-page"></a>Página de métricas comuns

As métricas no painel de navegação à esquerda são **ligadas** à página métricas comuns.

Primeiro, selecione métricas para visualizar:

![Selecione exibição métrica](media/metrics/metrics-4.png)

Detalhes de todas as métricas podem ser encontrados na [seção](#user-metrics-options) abaixo.

Em seguida, selecione o tipo de agregação para cada métrica:

![Agregação Métrica](media/metrics/metrics-5.png)

O tipo de agregação indica como agregar pontos métricos no gráfico por tempo. Há um ponto métrico bruto a cada minuto, e o tipo de pré-agregação dentro de um minuto é pré-definido pelo tipo de métricas.
* Soma: Soma todos os valores como saída de destino.
* Média: Use o valor médio no período como saída alvo.
* Max/Min: Use o valor Max/Min no período como saída de destino.

O intervalo de tempo para mostrar também pode ser modificado. O intervalo de tempo pode ser escolhido de 30 minutos para durar 30 dias, ou um intervalo de tempo personalizado.

![Modificação métrica](media/metrics/metrics-6.png)

A exibição padrão inclui todas as métricas do aplicativo do azure Spring Cloud em conjunto. As métricas de um aplicativo ou instância podem ser filtradas no visor.  Clique **em Adicionar filtro,** defina a propriedade como **App**e selecione o aplicativo de destino que deseja monitorar na caixa de texto **Valores.** 

Você pode usar dois tipos de filtros (propriedades):
* Aplicativo: filtro pelo nome do aplicativo
* Exemplo: filtrar por instância do aplicativo

![Filtros métricos](media/metrics/metrics-7.png)

Você também pode usar a opção **Aplicar divisão,** que desenhará várias linhas para um aplicativo:

![Divisão métrica](media/metrics/metrics-8.png)

>[!TIP]
> Você pode criar seus próprios gráficos na página de métricas e fixá-los em seu **Painel**. Comece nomeando seu gráfico.  Em seguida, selecione **Fixar no painel no canto superior direito**. Agora você pode verificar seu aplicativo no **Painel** do Portal.

## <a name="user-metrics-options"></a>Opções de métricas do usuário

As tabelas a seguir mostram as métricas e detalhes disponíveis.

### <a name="error"></a>Erro
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do atuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Erro global da Tomcat | tomcat.global.error | Contagem | Número de erros ocorre de solicitações processadas |

### <a name="performance"></a>Desempenho
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do atuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>|Porcentagem de uso da CPU do sistema | system.cpu.use | Porcentagem | Uso recente da CPU para todo o sistema. Esse valor é um duplo no intervalo [0.0,1.0]. Um valor de 0,0 significa que todas as UCP estiveram ociosas durante o período recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam funcionando ativamente 100% do tempo durante o período recente sendo observado.|
>| Porcentagem de uso da CPU do aplicativo | Porcentagem de uso da CPU do aplicativo | Porcentagem | Uso recente da CPU para o processo Java Virtual Machine. Esse valor é um duplo no intervalo [0.0,1.0]. Um valor de 0,0 significa que nenhuma das CPUs estava executando threads do processo JVM durante o período recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam executando ativamente segmentos do JVM 100% do tempo durante o período recente observado. Os threads do JVM incluem os segmentos de aplicação, bem como os segmentos internos JVM.|
>| Memória do aplicativo atribuída | jvm.memory.committed | Bytes | Representa a quantidade de memória que é garantida para uso pelo JVM. O JVM pode liberar a memória para o sistema e comprometido pode ser menor do que init. comprometido será sempre maior ou igual ao usado. |
>| Memória do aplicativo usada | jvm.memory.used | Bytes | Representa a quantidade de memória usada atualmente em bytes. |
>| Memória do aplicativo Max | jvm.memory.max | Bytes | Representa a quantidade máxima de memória que pode ser usada para o gerenciamento de memória. A quantidade de memória usada e comprometida será sempre menor ou igual a max se max for definido. Uma alocação de memória pode falhar se tentar aumentar a memória usada de tal forma que usada > cometida mesmo se usada <= max ainda seria verdade (por exemplo, quando o sistema está com pouca memória virtual). |
>| Tamanho máximo de dados da geração antiga disponível | jvm.gc.max.data.size | Bytes | O pico de uso de memória do pool de memória da antiga geração desde que a máquina virtual Java foi iniciada. |
>| Tamanho de dados da geração antiga | jvm.gc.live.data.size | Bytes | Tamanho do pool de memória da velha geração depois de um GC completo. |
>| Promover ao tamanho de dados da geração antiga | jvm.gc.memory.promoted | Bytes | Contagem de aumentos positivos no tamanho do pool de memória da geração antiga antes de GC para depois de GC. |
>| Promover ao Tamanho de Dados da Geração Jovem | jvm.gc.memory.allocated | Bytes | Incrementado para um aumento no tamanho do pool de memória da geração jovem depois de um GC para antes do próximo. |
>| Contagem de pausa sinuosa gc | jvm.gc.pause (contagem total) | Contagem | Contagem total de GC após este JMV começou, incluindo Young e Old GC. |
>| TEMPO total de pausa do GC | jvm.gc.pause (tempo total) | Milissegundos | Tempo total de GC consumido após este JMV começou, incluindo Young e Old GC. |

### <a name="request"></a>Solicitação
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do atuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat Total Sent Bytes | tomcat.global.enviado | Bytes | Quantidade de dados que o servidor web Tomcat enviou |
>| Bytes recebidos no total de tomcat | tomcat.global.received | Bytes | Quantidade de dados que o servidor web Tomcat recebeu |
>| Tomcat Solicitar Tempo Total | tomcat.global.request (tempo total) | Milissegundos | Tempo total do servidor web Tomcat para processar as solicitações |
>| Contagem total de solicitações de tomcat | tomcat.global.request (contagem total) | Contagem | Contagem total de solicitações processadas pelo servidor web Tomcat |
>| Tomcat Solicitar Tempo Máximo | tomcat.global.request.max | Milissegundos | Tempo máximo do servidor web Tomcat para processar uma solicitação |

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome métrico do atuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Contagem ativa da sessão Tomcat | tomcat.sessions.active.max | Contagem | Número máximo de sessões que estiveram ativas ao mesmo tempo |
>| Tomcat Session Max Tempo Vivo | tomcat.sessions.alive.max | Milissegundos | Maior tempo (em segundos) que uma sessão expirada tinha sido vivo |
>| Contagem criada da sessão tomcat | tomcat.sessions.created | Contagem | Número de sessões que foram criadas |
>| Contagem expirada da sessão tomcat | tomcat.sessions.expirou | Contagem | Número de sessões que expiraram |
>| Contagem rejeitada da sessão de Tomcat | tomcat.sessions.rejeitado | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas chegou. |

## <a name="see-also"></a>Confira também
* [Introdução ao Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analisar registros e métricas com configurações de diagnóstico](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Próximas etapas
* [Tutorial: Monitore os recursos da Nuvem de Primavera usando alertas e grupos de ação](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Cotas e Planos de Serviços para Nuvem de Primavera do Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

