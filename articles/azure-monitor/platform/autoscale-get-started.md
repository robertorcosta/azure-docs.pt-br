---
title: Introdução ao dimensionamento automático no Azure
description: Saiba como dimensionar seu aplicativo Web de recurso, serviço de nuvem, máquina virtual ou conjunto de dimensionamento de máquinas virtuais no Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: d37b1bad397e6170e2a7992a0a9671d6ca9c25ef
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651722"
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

### <a name="health-check-path"></a>Caminho de verificação de integridade

O caminho deve responder dentro de dois minutos com um código de status entre 200 e 299 (inclusivo). Se o caminho não responder dentro de dois minutos ou retornar um código de status fora do intervalo, a instância será considerada "não íntegra". A verificação de integridade se integra aos recursos de autenticação e autorização do serviço de aplicativo, o sistema alcançará o ponto de extremidade mesmo se esses recursos do secuity estiverem habilitados. Se você estiver usando seu próprio sistema de autenticação, o caminho de verificação de integridade deverá permitir acesso anônimo. Se o site tiver HTTP**S** habilitado, o HealthCheck atingirá o ponto de extremidade http primeiro e, em seguida, honrará o redirecionamento http 307 para o ponto de extremidade HTTPS.

O caminho de verificação de integridade deve verificar os componentes críticos do seu aplicativo. Por exemplo, se seu aplicativo depende de um banco de dados e um sistema de mensagens, o ponto de extremidade de verificação de integridade deve se conectar a esses componentes. Se o aplicativo não puder se conectar a um componente crítico, o caminho deverá retornar um código de resposta de nível 500 para indicar que o aplicativo não está íntegro.

#### <a name="security"></a>Segurança 

As equipes de desenvolvimento em grandes empresas geralmente precisam aderir aos requisitos de segurança para suas APIs expostas. Para proteger o ponto de extremidade HealthCheck, você deve primeiro usar recursos como [restrições de IP](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules), certificados de [cliente](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)ou uma rede virtual para restringir o acesso ao aplicativo. Você pode proteger o ponto de extremidade HealthCheck em si, exigindo que o `User-Agent` da solicitação de entrada seja correspondente `ReadyForRequest/1.0` . O agente do usuário não pode ser falsificado, pois a solicitação já foi protegida pelos recursos de segurança anteriores.

### <a name="behavior"></a>Comportamento

Quando o caminho de verificação de integridade for fornecido, o serviço de aplicativo executará ping no caminho em todas as instâncias. Se um código de resposta bem-sucedido não for recebido após 5 pings, essa instância será considerada "não íntegra". Instâncias não íntegras serão excluídas da rotação do balanceador de carga. Além disso, quando você estiver aumentando ou reduzindo, o serviço de aplicativo executará ping no caminho de verificação de integridade para garantir que as novas instâncias estejam prontas para solicitações.

As instâncias íntegras restantes podem apresentar uma carga maior. Para evitar sobrecarregar as instâncias restantes, não mais do que metade das instâncias serão excluídas. Por exemplo, se um plano do serviço de aplicativo for escalado horizontalmente para 4 instâncias e três das quais não estiverem íntegras, no máximo 2 serão excluídas da rotação do balanceador. As outras 2 instâncias (1 íntegro e 1 não íntegro) continuarão a receber solicitações. No pior cenário em que todas as instâncias não estão íntegras, nenhuma será excluída.

Se uma instância permanecer não íntegra durante uma hora, ela será substituída por uma nova instância. No máximo uma instância será substituída por hora, com um máximo de três instâncias por dia por plano do serviço de aplicativo.

### <a name="monitoring"></a>Monitoramento

Depois de fornecer o caminho de verificação de integridade do aplicativo, você pode monitorar a integridade do seu site usando Azure Monitor. Na folha **verificação de integridade** no portal, clique nas **métricas** na barra de ferramentas superior. Isso abrirá uma nova folha em que você poderá ver o status de integridade histórico do site e criar uma nova regra de alerta. Para obter mais informações sobre como monitorar seus sites, [consulte o guia em Azure monitor](../../app-service/web-sites-monitor.md).

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
