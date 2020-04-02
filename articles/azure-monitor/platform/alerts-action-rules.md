---
title: Regras de ação para alertas do Monitor Do Azure
description: Entender quais são as regras de ação no Azure Monitor e como configurá-las e gerenciá-las.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 0498325984641641d6dfc9ee6b89f66800b5c7d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546477"
---
# <a name="action-rules-preview"></a>Regras de ação (visualização)

As regras de ação ajudam você a definir ou suprimir ações em qualquer escopo do Azure Resource Manager (assinatura do Azure, grupo de recursos ou recurso de destino). Eles têm vários filtros que ajudam você a reduzir o subconjunto específico de instâncias de alerta em que você deseja agir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]

## <a name="why-and-when-should-you-use-action-rules"></a>Por que e quando você deve usar regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Existem muitos cenários em que é útil suprimir as notificações que os alertas geram. Esses cenários variam desde a supressão durante uma janela de manutenção planejada até a supressão durante o horário não comercial. Por exemplo, a equipe responsável pela **ContosoVM** quer suprimir as notificações de alerta para o próximo fim de semana, pois **a ContosoVM** está passando por manutenção planejada. 

Embora a equipe possa desativar cada regra de alerta configurada manualmente no **ContosoVM** (e habilitá-la novamente após a manutenção), não é um processo simples. As regras de ação ajudam a definir a supressão de alerta em escala com a capacidade de configurar de forma flexível o período de supressão. No exemplo anterior, a equipe pode definir uma regra de ação sobre **ContosoVM** que suprime todas as notificações de alerta para o fim de semana.


### <a name="actions-at-scale"></a>Ações em escala

Embora as regras de alerta ajudem a definir o grupo de ação que é acionado quando o alerta é gerado, os clientes geralmente têm um grupo de ação comum em todo o seu escopo de operações. Por exemplo, uma equipe responsável pelo grupo de recursos **ContosoRG** provavelmente definirá o mesmo grupo de ação para todas as regras de alerta definidas dentro **do ContosoRG**. 

As regras de ação ajudam a simplificar esse processo. Ao definir ações em escala, um grupo de ação pode ser acionado para qualquer alerta gerado no escopo configurado. No exemplo anterior, a equipe pode definir uma regra de ação no **ContosoRG** que acionará o mesmo grupo de ação para todos os alertas gerados dentro dele.

> [!NOTE]
> Atualmente, as regras de ação não se aplicam aos alertas de saúde do Azure Service.

## <a name="configuring-an-action-rule"></a>Configuração de uma regra de ação

Você pode acessar o recurso selecionando **Gerenciar ações** da página de **aterrissagem alertas** no Monitor Azure. Em seguida, selecione **Regras de ação (visualização)**. Você pode acessar as regras selecionando regras de **ação (visualização)** no painel da página de pouso para alertas.

![Regras de ação da página de aterrissagem do Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ Nova regra de ação**. 

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-new-rule.png)

Alternativamente, você pode criar uma regra de ação enquanto estiver configurando uma regra de alerta.

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-alert-rule.png)

Agora você deve ver a página de fluxo para criar regras de ação. Configure os seguintes elementos: 

![Novo fluxo de criação de regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Escopo

Primeiro escolha o escopo (assinatura do Azure, grupo de recursos ou recurso de destino). Você também pode selecionar várias vezes uma combinação de escopos dentro de uma única assinatura.

![Escopo de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critérios de filtragem

Além disso, você pode definir filtros para reduzi-los a um subconjunto específico dos alertas. 

Os filtros disponíveis são: 

* **Gravidade**: A opção de selecionar uma ou mais gravidades de alerta. **Gravidade = Sev1** significa que a regra de ação é aplicável para todos os alertas definidos para Sev1.
* **Serviço de monitor**: Um filtro baseado no serviço de monitoramento de origem. Este filtro também é selecionado por vários anos. Por exemplo, **Monitor Service = "Application Insights"** significa que a regra de ação é aplicável para todos os alertas baseados em Insights de Aplicativos.
* **Tipo de recurso**: Um filtro baseado em um tipo de recurso específico. Este filtro também é selecionado por vários anos. Por exemplo, **Tipo de recurso = "Máquinas Virtuais"** significa que a regra de ação é aplicável para todas as máquinas virtuais.
* **ID de regra de**alerta : Uma opção para filtrar regras específicas de alerta usando o ID do Gerenciador de Recursos da regra de alerta.
* **Condição do monitor**: Um filtro para instâncias de alerta com **acionado** ou **resolvido** como condição do monitor.
* **Descrição**: Uma correspondência de regex (expressão regular) que define uma correspondência de seqüência de cordas com a descrição, definida como parte da regra de alerta. Por exemplo, **A descrição contém 'prod'** corresponderá a todos os alertas que contenham a seqüência "prod" em suas descrições.
* **Contexto de alerta (carga)**: Uma correspondência de regex que define uma correspondência de seqüência de cordas contra os campos de contexto de alerta da carga útil de um alerta. Por exemplo, **o contexto de alerta (carga útil) contém 'Computador-01'** corresponderá a todos os alertas cujas cargas contêm a string "Computer-01".

Estes filtros são aplicados em conjunto uns com os outros. Por exemplo, se você definir **tipo de recurso ' = Máquinas Virtuais** e **Gravidade' = Sev0**, então você filtrapara todos os alertas **Sev0** apenas em suas VMs. 

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração do grupo de supressão ou ação

Em seguida, configure a regra de ação para supressão de alerta ou suporte a grupo de ação. Você não pode escolher os dois. A configuração atua em todas as instâncias de alerta que correspondem ao escopo e filtros previamente definidos.

#### <a name="suppression"></a>Supressão

Se você selecionar **a supressão,** configure a duração para a supressão de ações e notificações. Escolha uma das seguintes opções:
* **A partir de agora (Sempre)**: Suprime todas as notificações indefinidamente.
* **Em um horário agendado:** Suprime as notificações dentro de uma duração limitada.
* **Com uma recorrência**: Suprime as notificações em um horário diário, semanal ou mensal recorrente.

![Supressão de regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se você selecionar **o grupo Ação** no alternador, adicione um grupo de ação existente ou crie um novo. 

> [!NOTE]
> Você pode associar apenas um grupo de ação a uma regra de ação.

![Adicionar ou criar nova regra de ação selecionando grupo de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por último, configure os seguintes detalhes para a regra de ação:
* Nome
* Grupo de recursos no qual ele é salvo
* Descrição 

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

Contoso quer suprimir notificações para todos os alertas Sev4 em todas as VMs dentro da assinatura **ContosoSub** todos os fins de semana.

**Solução:** Crie uma regra de ação com:
* Escopo = **ContosoSub**
* Filtros
    * Gravidade = **Sev4**
    * Tipo de recurso = **Máquinas Virtuais**
* Supressão com recorrência definida para semanal, e **sábado** e **domingo** verificado

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto de alerta (carga)

Contoso quer suprimir notificações para todos os alertas de log gerados para **o Computador-01** em **ContosoSub** indefinidamente, pois está passando por manutenção.

**Solução:** Crie uma regra de ação com:
* Escopo = **ContosoSub**
* Filtros
    * Serviço de monitor = **Análise de Log**
    * O contexto de alerta (carga útil) contém **o computador-01**
* Supressão definida para **A partir de agora (Sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ação definido em um grupo de recursos

Contoso definiu [um alerta métrico em nível de assinatura.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor) Mas quer definir as ações que disparam especificamente para os alertas gerados a partir do grupo de recursos **ContosoRG**.

**Solução:** Crie uma regra de ação com:
* Escopo = **ContosoRG**
* Sem filtros
* Grupo de ação definido para **ContosoActionGroup**

> [!NOTE]
> *Grupos de ação definidos dentro das regras de ação e regras de alerta operam de forma independente, sem deduplicação.* No cenário descrito anteriormente, se um grupo de ação é definido para a regra de alerta, ele é acionado em conjunto com o grupo de ação definido na regra de ação. 

## <a name="managing-your-action-rules"></a>Gerenciando suas regras de ação

Você pode visualizar e gerenciar suas regras de ação a partir da exibição da lista:

![Exibição da lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, você pode ativar, desativar ou excluir regras de ação em escala, selecionando a caixa de seleção ao lado delas. Quando você seleciona uma regra de ação, sua página de configuração é aberta. A página ajuda você a atualizar a definição da regra de ação e ahabilitá-la ou desativá-la.

## <a name="best-practices"></a>Práticas recomendadas

Os alertas de log que você cria com o [número de resultados](alerts-unified-log.md) geram uma única instância de alerta usando todo o resultado da pesquisa (que pode se estender por vários computadores). Neste cenário, se uma regra de ação usar o filtro **Alerta Contexto (carga),** ele agirá na instância de alerta enquanto houver uma correspondência. No Cenário 2, descrito anteriormente, se os resultados de pesquisa para o alerta de log gerado contiverem tanto **o Computador-01** quanto o **Computador-02,** toda a notificação será suprimida. Não há nenhuma notificação gerada para **o Computador-02.**

![Regras de ação e alertas de log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para melhor usar alertas de log com regras de ação, crie alertas de log com a opção [de medição métrica.](alerts-unified-log.md) As instâncias de alerta separadas são geradas por essa opção, com base em seu campo de grupo definido. Em seguida, no Cenário 2, são geradas instâncias de alerta separadas para **o Computador-01** e **o Computador-02**. Devido à regra de ação descrita no cenário, apenas a notificação para **o Computador-01** é suprimida. A notificação para **o Computador-02** continua a disparar normalmente.

![Regras de ação e alertas de log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Perguntas frequentes

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Enquanto estou configurando uma regra de ação, gostaria de ver todas as possíveis regras de ação sobrepostas, para que eu evite notificações duplicadas. É possível fazer isso?

Depois de definir um escopo ao configurar uma regra de ação, você pode ver uma lista de regras de ação que se sobrepõem no mesmo escopo (se houver). Essa sobreposição pode ser uma das seguintes opções:

* Uma correspondência exata: Por exemplo, a regra de ação que você está definindo e a regra de ação sobreposta estão na mesma assinatura.
* Um subconjunto: Por exemplo, a regra de ação que você está definindo está em uma assinatura, e a regra de ação sobreposta está em um grupo de recursos dentro da assinatura.
* Um superconjunto: Por exemplo, a regra de ação que você está definindo está em um grupo de recursos, e a regra de ação sobreposta está na assinatura que contém o grupo de recursos.
* Uma intersecção: Por exemplo, a regra de ação que você está definindo está no **VM1** e **vM2**, e a regra de ação sobreposta está em **VM2** e **VM3**.

![Regras de ação sobrepostas](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Enquanto estou configurando uma regra de alerta, é possível saber se já existem regras de ação definidas que podem agir na regra de alerta que estou definindo?

Depois de definir o recurso de destino para sua regra de alerta, você pode ver a lista de regras de ação que agem no mesmo escopo (se houver) selecionando **Exibir ações configuradas** na seção **Ações.** Esta lista é preenchida com base nos seguintes cenários para o escopo:

* Uma correspondência exata: Por exemplo, a regra de alerta que você está definindo e a regra de ação estão na mesma assinatura.
* Um subconjunto: Por exemplo, a regra de alerta que você está definindo está em uma assinatura, e a regra de ação está em um grupo de recursos dentro da assinatura.
* Um superconjunto: Por exemplo, a regra de alerta que você está definindo está em um grupo de recursos, e a regra de ação está na assinatura que contém o grupo de recursos.
* Um cruzamento: Por exemplo, a regra de alerta que você está definindo está no **VM1** e **vM2**, e a regra de ação está em **VM2** e **VM3**.
    
![Regras de ação sobrepostas](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Posso ver os alertas que foram suprimidos por uma regra de ação?

Na [página da lista de alertas,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)você pode escolher uma coluna adicional chamada **Status de Supressão**. Se a notificação de uma instância de alerta fosse suprimida, mostraria esse status na lista.

![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se há uma regra de ação com um grupo de ação e outra com supressão ativa no mesmo escopo, o que acontece?

A supressão sempre tem precedência no mesmo escopo.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso que é monitorado em duas regras de ação separadas? Recebo uma ou duas notificações? Por exemplo, **VM2** no seguinte cenário:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Para cada alerta no VM1 e VM3, o grupo de ação AG1 seria acionado uma vez. Para cada alerta no **VM2,** o grupo de ação AG1 seria acionado duas vezes, porque as regras de ação não deduplicam as ações. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso monitorado em duas regras de ação separadas e um pede ação enquanto outro para supressão? Por exemplo, **VM2** no seguinte cenário:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Para cada alerta no VM1, o grupo de ação AG1 seria acionado uma vez. Ações e notificações para cada alerta em VM2 e VM3 serão suprimidas. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>O que acontece se eu tiver uma regra de alerta e uma regra de ação definida para o mesmo recurso chamando diferentes grupos de ação? Por exemplo, **VM1** no seguinte cenário:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Para cada alerta no VM1, o grupo de ação AG1 seria acionado uma vez. Sempre que a regra de alerta "regra1" for acionada, ela também acionará o AG2 adicionalmente. Grupos de ação definidos dentro das regras de ação e regras de alerta operam de forma independente, sem deduplicação. 

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre alertas no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
