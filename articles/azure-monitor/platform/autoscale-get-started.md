---
title: Introdução ao dimensionamento automático no Azure
description: Saiba como dimensionar seu aplicativo Web de recurso, serviço de nuvem, máquina virtual ou conjunto de dimensionamento de máquinas virtuais no Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 5af60befeda9f0ed4ed76f7ab8449e94950352fb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186568"
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como configurar o dimensionamento automático para seu recurso no Portal do Microsoft Azure.

O dimensionamento automático do Azure Monitor aplica-se somente aos [Conjuntos de Dimensionamento de Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), aos [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/), ao [Serviço de Aplicativo – Aplicativos Web](https://azure.microsoft.com/services/app-service/web/) e aos [Serviços de Gerenciamento de API](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Descobrir as configurações de dimensionamento automático na sua assinatura

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Você pode descobrir todos os recursos a que o dimensionamento automático se aplica no Azure Monitor. Use as etapas a seguir para obter uma explicação passo a passo:

1. Abra o [portal do Azure.][1]
1. Clique no ícone do Azure Monitor no painel esquerdo.
  ![Abra o Azure Monitor][2]
1. Clique em **Dimensionamento automático** para exibir todos os recursos para os quais o dimensionamento automático é aplicável, juntamente com o status atual.
  ![Descubra o dimensionamento automático no Azure Monitor][3]

Você pode usar o painel de filtro na parte superior para reduzir o escopo da lista e selecionar recursos em um grupo de recursos específico, os tipos de recursos específicos ou um recurso específico.

Para cada recurso, você encontrará a contagem de instâncias atual e o status de dimensionamento automático. O status de dimensionamento automático pode ser:

- **Não configurado**: você ainda não habilitou o dimensionamento automático para este recurso.
- **Habilitado**: você habilitou o dimensionamento automático para este recurso.
- **Desabilitado**: você desabilitou o dimensionamento automático para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Crie sua primeira configuração de dimensionamento automático

Agora, vamos percorrer um passo a passo simples para criar sua primeira configuração de dimensionamento automático.

1. Abra a folha **Dimensionamento Automático** no Azure Monitor e selecione um recurso que deseja dimensionar. (As etapas a seguir usam um plano do serviço de aplicativo associado a um aplicativo Web. Você pode [criar seu primeiro aplicativo Web ASP.NET no Azure em 5 minutos.][4])
1. Observe que a contagem da instância atual é 1. Clique em **Habilitar dimensionamento automático**.
  ![Configuração de dimensionamento para um novo aplicativo Web][5]
1. Forneça um nome para a configuração de dimensionamento e clique em **Adicionar uma regra**. Observe as opções de regra de dimensionamento que são abertas como um painel de contexto no lado direito. Por padrão, ele define a opção de dimensionar sua contagem de instâncias em 1 se o percentual de CPU do recurso ultrapassar 70%. Deixe-o com seus valores padrão e clique em **Adicionar**.
  ![Criar configuração dimensionamento para um aplicativo Web][6]
1. Agora, você criou sua primeira regra de dimensionamento. Observe que o UX indica as práticas recomendadas e afirma que "É recomendável ter pelo menos uma escala na regra". Para fazer isso:

    a. Clique em **Adicionar uma Regra**.

    b. Defina **Operador** como **Menor que**.

    c. Defina o **Limite** como **20**.

    d. Defina **Operação** como **Diminuir contagem por**.

   Agora, você deve ter uma configuração de dimensionamento que expande/reduz com base no uso da CPU.
   ![Dimensionamento com base na CPU][8]
1. Clique em **Salvar**.

Parabéns! Você criou com êxito sua primeira configuração de dimensionamento para fazer o dimensionamento automático de seu aplicativo Web com base no uso da CPU.

> [!NOTE]
> As mesmas etapas são aplicáveis para começar a usar uma função de serviço de nuvem ou conjunto de dimensionamento de máquinas virtuais.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Dimensionamento com base em um planejamento
Além de dimensionar com base na CPU, você também pode definir seu dimensionamento de forma diferente em dias específicos da semana.

1. Clique em **Adicionar uma condição de dimensionamento**.
1. A configuração do modo e das regras de dimensionamento é a mesma que a da condição padrão.
1. Selecione **Repetir dias específicos** para o agendamento.
1. Selecione os dias e a hora de início/término em que a condição de dimensionamento deve ser aplicada.

![Condição de dimensionamento com base no agendamento][9]
### <a name="scale-differently-on-specific-dates"></a>Dimensionar de forma diferente em datas específicas
Além de dimensionar com base na CPU, você também pode definir seu dimensionamento de forma diferente em datas específicas.

1. Clique em **Adicionar uma condição de dimensionamento**.
1. A configuração do modo e das regras de dimensionamento é a mesma que a da condição padrão.
1. Selecione **Especificar datas de início/término** para o agendamento.
1. Selecione as datas e a hora de início/término em que a condição de dimensionamento deve ser aplicada.

![Condição de dimensionamento com base em datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Exibir o histórico de dimensionamento de seu recurso
Sempre que o recurso é expandido/reduzido, um evento é registrado no log de atividades. Você pode exibir o histórico de dimensionamento do seu recurso nas últimas 24 horas acessando a guia **Histórico de execução**.

![Histórico da execução][11]

Se quiser exibir o histórico completo de dimensionamento (de até 90 dias), selecione **Clique aqui para ver mais detalhes**. O log de atividade é aberto, com Dimensionamento Automático pré-selecionado para seu recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Exibir a definição de escala do seu recurso
Dimensionamento automático é um recurso do Gerenciador de Recursos do Azure. Você pode exibir a definição de escala no JSON acessando a guia **JSON**.

![Definição de escala][12]

Você pode fazer alterações no JSON diretamente, se necessário. Essas alterações serão refletidas depois que você as salvar.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desabilitar a escala automática e dimensionar suas instâncias manualmente
Pode haver momentos em que você queira desabilitar sua configuração de dimensionamento atual e dimensionar manualmente seu recurso.

Clique no botão **Desabilitar dimensionamento automático** na parte superior.
![Desabilitar dimensionamento automático][13]

> [!NOTE]
> Esta opção desabilita a sua configuração. No entanto, você pode voltar a ela depois que habilitar o Dimensionamento Automático novamente.

Agora, você pode definir o número de instâncias para o qual deseja dimensionar manualmente.

![Definir dimensionamento manual][14]

Você sempre pode retornar para o dimensionamento automático clicando em **Habilitar dimensionamento automático** e **Salvar**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Rotear o tráfego para instâncias íntegras (serviço de aplicativo)

Quando você é escalado horizontalmente para várias instâncias, o serviço de aplicativo pode executar verificações de integridade em suas instâncias para rotear o tráfego somente para as instâncias íntegras. Para fazer isso, abra o portal para o serviço de aplicativo e selecione **verificação de integridade** em **monitoramento**. Selecione **habilitar** e forneça um caminho de URL válido em seu aplicativo, como `/health` ou `/api/health` . Clique em **Salvar**.

Para habilitar o recurso com modelos ARM, defina a `healthcheckpath` Propriedade do `Microsoft.Web/sites` recurso para o caminho de verificação de integridade em seu site, por exemplo: `"/api/health/"` . Para desabilitar o recurso, defina a propriedade de volta para a cadeia de caracteres vazia, `""` .

### <a name="health-check-path"></a>Caminho de verificação de integridade

O caminho deve responder dentro de um minuto com um código de status entre 200 e 299 (inclusivo). Se o caminho não responder dentro de um minuto ou retornar um código de status fora do intervalo, a instância será considerada "não íntegra". O serviço de aplicativo não segue os redirecionamentos de 302 no caminho de verificação de integridade. A verificação de integridade se integra aos recursos de autenticação e autorização do serviço de aplicativo, o sistema alcançará o ponto de extremidade mesmo se esses recursos do secuity estiverem habilitados. Se você estiver usando seu próprio sistema de autenticação, o caminho de verificação de integridade deverá permitir acesso anônimo. Se o site tiver somente HTTP **s** habilitado, a solicitação HealthCheck será enviada via http **s**.

O caminho de verificação de integridade deve verificar os componentes críticos do seu aplicativo. Por exemplo, se seu aplicativo depende de um banco de dados e um sistema de mensagens, o ponto de extremidade de verificação de integridade deve se conectar a esses componentes. Se o aplicativo não puder se conectar a um componente crítico, o caminho deverá retornar um código de resposta de nível 500 para indicar que o aplicativo não está íntegro.

#### <a name="security"></a>Segurança 

As equipes de desenvolvimento em grandes empresas geralmente precisam aderir aos requisitos de segurança para suas APIs expostas. Para proteger o ponto de extremidade HealthCheck, você deve primeiro usar recursos como [restrições de IP](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule), certificados de [cliente](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule)ou uma rede virtual para restringir o acesso ao aplicativo. Você pode proteger o ponto de extremidade HealthCheck em si, exigindo que o `User-Agent` da solicitação de entrada seja correspondente `ReadyForRequest/1.0` . O User-Agent não pode ser falsificado, pois a solicitação já foi protegida pelos recursos de segurança anteriores.

### <a name="behavior"></a>Comportamento

Quando o caminho de verificação de integridade for fornecido, o serviço de aplicativo executará ping no caminho em todas as instâncias. Se um código de resposta bem-sucedido não for recebido após 5 pings, essa instância será considerada "não íntegra". Instâncias não íntegras serão excluídas da rotação do balanceador de carga. Você pode configurar o número necessário de pings com falha com a `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` configuração do aplicativo. Essa configuração de aplicativo pode ser definida como qualquer inteiro entre 2 e 10. Por exemplo, se estiver definido como `2` , suas instâncias serão removidas do balanceador de carga após dois pings com falha. Além disso, quando você estiver aumentando ou reduzindo, o serviço de aplicativo executará ping no caminho de verificação de integridade para garantir que as novas instâncias estejam prontas para solicitações antes de serem adicionadas ao balanceador de carga.

> [!NOTE]
> Lembre-se de que o plano do serviço de aplicativo deve ser dimensionado para duas ou mais instâncias para que a exclusão do balanceador de carga ocorra. Se você tiver apenas 1 instância, ela não será removida do balanceador de carga, mesmo que não esteja íntegra. 

As instâncias íntegras restantes podem apresentar uma carga maior. Para evitar sobrecarregar as instâncias restantes, não mais do que metade das instâncias serão excluídas. Por exemplo, se um plano do serviço de aplicativo for escalado horizontalmente para 4 instâncias e três das quais não estiverem íntegras, no máximo 2 serão excluídas da rotação do balanceador. As outras 2 instâncias (1 íntegro e 1 não íntegro) continuarão a receber solicitações. No pior cenário em que todas as instâncias não estão íntegras, nenhuma será excluída. Se você quiser substituir esse comportamento, poderá definir a `WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` configuração do aplicativo como um valor entre `0` e `100` . Definir isso para um valor mais alto significa que mais instâncias não íntegras serão removidas (o valor padrão é 50).

Se uma instância permanecer não íntegra durante uma hora, ela será substituída por uma nova instância. No máximo uma instância será substituída por hora, com um máximo de três instâncias por dia por plano do serviço de aplicativo.

### <a name="monitoring"></a>Monitoramento

Depois de fornecer o caminho de verificação de integridade do aplicativo, você pode monitorar a integridade do seu site usando Azure Monitor. Na folha **verificação de integridade** no portal, clique nas **métricas** na barra de ferramentas superior. Isso abrirá uma nova folha em que você poderá ver o status de integridade histórico do site e criar uma nova regra de alerta. Para obter mais informações sobre como monitorar seus sites, [consulte o guia em Azure monitor](../../app-service/web-sites-monitor.md).

## <a name="moving-autoscale-to-a-different-region"></a>Movendo o dimensionamento automático para uma região diferente
Esta seção descreve como mover a autoescala do Azure para outra região sob a mesma assinatura e grupo de recursos. Você pode usar a API REST para mover as configurações de dimensionamento automático.
### <a name="prerequisite"></a>Pré-requisito
1. Verifique se a assinatura e o grupo de recursos estão disponíveis e se os detalhes nas regiões de origem e de destino são idênticos.
1. Verifique se o dimensionamento automático do Azure está disponível na [região do Azure para a qual você deseja mover](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Mover
Use a [API REST](/rest/api/monitor/autoscalesettings/createorupdate) para criar uma configuração de dimensionamento automático no novo ambiente. A configuração de dimensionamento automático criada na região de destino será uma cópia da configuração de dimensionamento automático na região de origem.

[As configurações de diagnóstico](./diagnostic-settings.md) que foram criadas em associação com a configuração de dimensionamento automático na região de origem não podem ser movidas. Você precisará recriar as configurações de diagnóstico na região de destino, depois que a criação de configurações de autovenda for concluída. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Saiba mais sobre como mover recursos entre regiões do Azure
Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Próximas etapas
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png