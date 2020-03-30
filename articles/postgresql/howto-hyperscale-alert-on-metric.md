---
title: Configurar alertas - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Este artigo descreve como configurar e acessar alertas métricos para o Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063139"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Use o portal Azure para configurar alertas sobre métricas para O Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)

Este artigo mostra como configurar alertas do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure. Você pode receber um alerta com base em [métricas de monitoramento](concepts-hyperscale-monitoring.md) para seus serviços do Azure.

Vamos configurar um alerta para disparar quando o valor de uma métrica especificada cruzar um limite. O alerta dispara quando a condição é atendida pela primeira vez, e continua a disparar depois.

Você pode configurar um alerta para fazer as seguintes ações quando ele disparar:
* Envie notificações de e-mail para o administrador do serviço e co-administradores.
* Enviar um email para outros emails que você especificar.
* Chamar um webhook.

Você pode configurar e obter informações sobre as regras de alerta usando:
* [Portal Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica no Portal do Azure
1. No [Portal do Azure](https://portal.azure.com/), selecione o servidor do Banco de Dados do Azure para PostgreSQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Alertas** como mostrado abaixo:

   ![Selecionar Regras de Alerta](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selecione **Nova regra de alerta** (+ ícone).

4. A página **Criar regra** é aberta, conforme mostrado abaixo. Preencha as informações obrigatórias:

   ![Formulário Adicionar alerta de métrica](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Na seção **Condição,** selecione **Adicionar**.

6. Selecione uma métrica da lista de sinais sobre a qual deseja ser alertado. Neste exemplo, selecione "Porcentagem de armazenamento".
   
   ![Selecionar métrica](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta:

    * **Operador** (ex. "Maior que")
    * **Valor limiar** (ex. 85%)
    * Tempo de **granularidade de agregação** a regra métrica deve ser satisfeita antes que o alerta seja acionado (ex. "Nos últimos 30 minutos")
    * e **Frequência de avaliação** (ex. "1 minuto")
   
   Selecione **Concluído** ao concluir.

   ![Selecionar métrica](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Dentro da seção **Grupos de Ações**, selecione **Criar Novo** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário "Adicionar grupo de ações" com um nome, o nome curto, a assinatura e o grupo de recursos.

    ![Grupo de ações](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Configure o tipo de ação **Email/SMS/Push/Voz**.
    
    Escolha "Papel de gerenciador de recursos do Mail Azure" para enviar notificações aos proprietários de assinaturas, colaboradores e leitores.
   
    Selecione **OK** ao concluir.

    ![Grupo de ações](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Especifique um Nome da regra de alerta, uma Descrição e uma Gravidade.

    ![Grupo de ações](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selecione **Criar regra de alerta** para criar o alerta.

    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

### <a name="managing-alerts"></a>Gerenciando alertas

Depois de criar um alerta, você pode selecioná-lo e fazer as seguintes ações:

* Exibir um grafo mostrando o limite de métrica e os valores reais do dia anterior relevante para este alerta.
* **Editar** ou **Excluir** a regra de alerta.
* **Desabilitar** ou **Habilitar** o alerta, se desejar interromper temporariamente ou retomar o recebimento de notificações.

## <a name="suggested-alerts"></a>Alertas sugeridos

### <a name="disk-space"></a>Espaço em disco

O monitoramento e o alerta são importantes para cada grupo de servidores de Hyperscale (Citus) de produção. O banco de dados PostgreSQL subjacente requer espaço livre em disco para operar corretamente. Se o disco ficar cheio, o nó do servidor de banco de dados ficará offline e se recusará a iniciar até que o espaço esteja disponível. Nesse ponto, ele requer uma solicitação de suporte da Microsoft para corrigir a situação.

Recomendamos a configuração de alertas de espaço em disco em cada nó em cada grupo de servidores, mesmo para uso não-produzido. Os alertas de uso do espaço em disco fornecem o aviso prévio necessário para intervir e manter os nódulos saudáveis. Para obter melhores resultados, tente uma série de alertas com 75%, 85% e 95% de uso. Os percentuais para escolher dependem da velocidade de ingestão de dados, uma vez que a ingestão rápida de dados preenche o disco mais rapidamente.

À medida que o disco se aproxima do seu limite de espaço, experimente essas técnicas para obter mais espaço livre:

* Revise a política de retenção de dados. Mova dados mais antigos para armazenamento a frio, se possível.
* Considere [adicionar nomes](howto-hyperscale-scaling.md#add-worker-nodes) ao grupo de servidores e reequilibrar fragmentos. O reequilíbrio distribui os dados em mais computadores.
* Considere [aumentar a capacidade](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) dos nódulos operários. Cada trabalhador pode ter até 2 TiB de armazenamento. No entanto, a adição de nós deve ser tentada antes de redimensionar nós porque a adição de nós completa mais rápido.

### <a name="cpu-usage"></a>Uso da CPU

Monitorar o uso da CPU é útil para estabelecer uma linha de base para o desempenho. Por exemplo, você pode notar que o uso da CPU é geralmente em torno de 40-60%. Se o uso da CPU de repente começar a pairar em torno de 95%, você pode reconhecer uma anomalia. O uso da CPU pode refletir o crescimento orgânico, mas também pode revelar uma consulta perdida. Ao criar um alerta de CPU, defina uma granularidade de agregação longa para capturar aumentos prolongados e ignorar picos momentâneos.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Tenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
