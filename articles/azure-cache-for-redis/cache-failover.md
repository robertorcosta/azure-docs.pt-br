---
title: Failover e aplicação de patches – Cache Redis
description: Saiba mais sobre failover, patches e o processo de atualização do Azure Cache for Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122192"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e patches para Cache Azure para Redis

Para criar aplicativos clientes resilientes e bem-sucedidos, é fundamental entender o failover no contexto do serviço Azure Cache for Redis. Um failover pode fazer parte de operações de gerenciamento planejadas, ou pode ser causado por falhas de hardware ou rede não planejadas. Um uso comum de failover de cache ocorre quando o serviço de gerenciamento patches do Cache Azure para binários Redis. Este artigo abrange o que é um failover, como ele ocorre durante o patch e como construir um aplicativo cliente resiliente.

## <a name="what-is-a-failover"></a>O que é um failover?

Vamos começar com uma visão geral do failover do Azure Cache para Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Um rápido resumo da arquitetura de cache

Um cache é construído de várias máquinas virtuais com endereços IP separados e privados. Cada máquina virtual, também conhecida como nó, está conectada a um balanceador de carga compartilhado com um único endereço IP virtual. Cada nó executa o processo do servidor Redis e é acessível por meio do nome do host e das portas Redis. Cada nó é considerado um mestre ou um nó de réplica. Quando um aplicativo cliente se conecta a um cache, seu tráfego passa por esse balanceador de carga e é automaticamente encaminhado para o nó mestre.

Em um cache Básico, o nó único é sempre um mestre. Em um cache Standard ou Premium, há dois nós: um é escolhido como o mestre e o outro é a réplica. Como os caches Standard e Premium têm vários nós, um nó pode estar indisponível enquanto o outro continua processando solicitações. Caches agrupados são feitos de muitos fragmentos, cada um com nós mestres e réplicas distintas. Um fragmento pode estar caído enquanto os outros permanecem disponíveis.

> [!NOTE]
> Um cache Básico não tem vários nós e não oferece um contrato de nível de serviço (SLA) para sua disponibilidade. Caches básicos são recomendados apenas para fins de desenvolvimento e teste. Use um cache Padrão ou Premium para uma implantação de vários áderes, para aumentar a disponibilidade.

### <a name="explanation-of-a-failover"></a>Explicação de um failover

Um failover ocorre quando um nó de réplica se promove para se tornar um nó mestre, e o antigo nó mestre fecha as conexões existentes. Depois que o nó mestre volta, ele percebe a mudança de papéis e se rebaixa para se tornar uma réplica. Em seguida, ele se conecta ao novo mestre e sincroniza dados. Um failover pode ser planejado ou não.

Um *failover planejado* ocorre durante as atualizações do sistema, como patches redis ou upgrades do sistema operacional, e operações de gerenciamento, como dimensionamento e reinicialização. Como os nós recebem aviso prévio da atualização, eles podem trocar de função cooperativamente e atualizar rapidamente o balanceador de carga da alteração. Um failover planejado normalmente termina em menos de 1 segundo.

Um *failover não planejado* pode acontecer devido a falha de hardware, falha de rede ou outras paralisações inesperadas no nó mestre. O nó da réplica se promove para dominar, mas o processo leva mais tempo. Um nó de réplica deve primeiro detectar que seu nó mestre não está disponível antes de iniciar o processo de failover. O nó de réplica também deve verificar se essa falha não planejada não é transitória ou local, para evitar um failover desnecessário. Esse atraso na detecção significa que um failover não planejado normalmente termina dentro de 10 a 15 segundos.

## <a name="how-does-patching-occur"></a>Como ocorre o patching?

O serviço Azure Cache for Redis atualiza regularmente seu cache com os recursos e correções mais recentes da plataforma. Para corrigir um cache, o serviço segue estas etapas:

1. O serviço de gerenciamento seleciona um nó a ser corrigido.
1. Se o nó selecionado for um nó mestre, o nó de réplica correspondente se promoverá cooperativamente. Esta promoção é considerada um failover planejado.
1. O nó selecionado reinicializa para levar as novas alterações e volta como um nó de réplica.
1. O nó de réplica se conecta ao nó mestre e sincroniza dados.
1. Quando a sincronização de dados estiver concluída, o processo de correção se repetirá para os demais.

Como o patching é um failover planejado, o nó de réplica rapidamente se promove para se tornar um mestre e começa a atender solicitações e novas conexões. Os caches básicos não têm um nó de réplica e não estão disponíveis até que a atualização esteja concluída. Cada fragmento de um cache agrupado é corrigido separadamente e não fecha conexões com outro fragmento.

> [!IMPORTANT]
> Os nós são corrigidos um de cada vez para evitar a perda de dados. Caches básicos terão perda de dados. Caches agrupados são corrigidos um fragmento de cada vez.

Vários caches no mesmo grupo de recursos e região também são corrigidos um de cada vez.  Caches que estão em diferentes grupos de recursos ou diferentes regiões podem ser corrigidos simultaneamente.

Como a sincronização completa de dados acontece antes do processo se repetir, é improvável que a perda de dados ocorra quando você usa um cache Standard ou Premium. Você pode se proteger ainda mais contra a perda de dados [exportando](cache-how-to-import-export-data.md#export) dados e permitindo [persistência](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Carga de cache adicional

Sempre que ocorre um failover, os caches Standard e Premium precisam replicar dados de um nó para o outro. Essa replicação causa algum aumento de carga tanto na memória do servidor quanto na CPU. Se a instância de cache já estiver fortemente carregada, os aplicativos clientes podem sofrer aumento de latência. Em casos extremos, os aplicativos de clientes podem receber exceções de tempo. Para ajudar a mitigar o impacto dessa carga `maxmemory-reserved` adicional, [configure](cache-configure.md#memory-policies) a configuração do cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>Como um failover afeta meu aplicativo de cliente?

O número de erros vistos pelo aplicativo do cliente depende de quantas operações estavam pendentes nessa conexão no momento do failover. Qualquer conexão que seja roteada através do nó que fechou suas conexões verá erros. Muitas bibliotecas de clientes podem lançar diferentes tipos de erros quando as conexões quebram, incluindo exceções de tempo, exceções de conexão ou exceções de soquete. O número e o tipo de exceções dependem de onde no caminho de código a solicitação é quando o cache fecha suas conexões. Por exemplo, uma operação que envia uma solicitação, mas não recebeu uma resposta quando o failover ocorre pode obter uma exceção de tempo. Novas solicitações no objeto de conexão fechada recebem exceções de conexão até que a reconexão aconteça com sucesso.

A maioria das bibliotecas de clientes tenta se reconectar ao cache se estiverem configuradas para fazê-lo. No entanto, os erros imprevistos podem ocasionalmente colocar os objetos da biblioteca em um estado irrecuperável. Se os erros persistirem por mais tempo do que uma quantidade de tempo pré-configurada, o objeto de conexão deve ser recriado. Em Microsoft.NET e outras linguagens orientadas a objetos, recriar a conexão sem reiniciar o aplicativo pode ser realizado usando [um padrão T\<\> preguiçoso](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Como faço para tornar minha aplicação resiliente?

Como você não pode evitar failovers completamente, escreva seus aplicativos clientepara resiliência a quebras de conexão e solicitações com falha. Embora a maioria das bibliotecas de clientes se reconecte automaticamente ao ponto final do cache, poucas delas tentam tentar novamente solicitações com falha. Dependendo do cenário da aplicação, pode fazer sentido usar a lógica de repetição com backoff.

Para testar a resiliência de um aplicativo cliente, use uma [reinicialização](cache-administration.md#reboot) como gatilho manual para quebras de conexão. Além disso, recomendamos que você [agende atualizações](cache-administration.md#schedule-updates) em um cache. Diga ao serviço de gerenciamento para aplicar patches de tempo de execução Redis durante janelas semanais especificadas. Essas janelas são normalmente períodos em que o tráfego de aplicativos do cliente é baixo, para evitar possíveis incidentes.

### <a name="client-network-configuration-changes"></a>Alterações na configuração da rede do cliente

Certas alterações na configuração da rede do lado do cliente podem desencadear erros de "Nenhuma conexão disponível". Tais alterações podem incluir:

- Trocando o endereço IP virtual de um aplicativo cliente entre slots de estágio e produção.
- Dimensionamento do tamanho ou número de instâncias do seu aplicativo.

Tais mudanças podem causar um problema de conectividade que dura menos de um minuto. Seu aplicativo cliente provavelmente perderá sua conexão com outros recursos de rede externa, além do serviço Azure Cache for Redis.

## <a name="next-steps"></a>Próximas etapas

- [Agende atualizações](cache-administration.md#schedule-updates) para o seu cache.
- Teste a resiliência do aplicativo usando uma [reinicialização](cache-administration.md#reboot).
- [Configure](cache-configure.md#memory-policies) reservas e políticas de memória.
