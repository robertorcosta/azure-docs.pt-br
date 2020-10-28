---
title: Obtenha ajuda com a implantação de solução do Azure VMware ou falhas de provisionamento
description: Como obter as informações de que você precisa de sua nuvem privada da solução Azure VMware para arquivar uma solicitação de serviço para implantação de solução do Azure VMware ou falhas de provisionamento.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779485"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Obtenha ajuda com a implantação de solução do Azure VMware ou falhas de provisionamento

Este artigo ajuda você com as falhas de implantação e provisionamento da solução do Azure VMware. Quando houver falhas em sua nuvem privada, você precisará abrir uma solicitação de [suporte](https://rc.portal.azure.com/#create/Microsoft.Support) (Sr) no portal do Azure. 

No entanto, primeiro você precisará coletar algumas informações importantes no portal do Azure:

- ID de Correlação
- ID do circuito do ExpressRoute

## <a name="collect-the-correlation-id"></a>Coletar a ID de correlação
 
Uma ID de correlação é gerada quando você cria uma nuvem privada ou qualquer recurso no Azure. Cada implantação de Azure Resource Manager também gera uma ID de correlação. Essa ID permite a criação e resolução de SR mais rápidas. 
 
Aqui está um exemplo da saída de uma implantação de nuvem privada com falha, com a ID de correlação realçada.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::

Copie e salve essa ID de correlação para incluir na solicitação de serviço. Para obter detalhes, consulte [criar sua solicitação de suporte](#create-your-support-request) no final deste artigo.

Se a falha ocorrer nos estágios de validação prévia, nenhuma ID de correlação será gerada. Nesse caso, você pode fornecer as informações que usou ao criar a nuvem privada da solução Azure VMware, incluindo:

- Location
- Resource group
- Nome do recurso
 
### <a name="collect-a-summary-of-errors"></a>Coletar um resumo de erros

Os detalhes de quaisquer erros também podem ajudar a resolver o problema. Na tela anterior, selecione a mensagem de aviso para ver um resumo dos erros.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::

Novamente, copie e salve esse Resumo para incluir no SR.
 
### <a name="retrieve-past-deployments"></a>Recuperar implantações anteriores

Você pode recuperar as implantações anteriores, incluindo as que falharam, pesquisando no log de atividades de implantação acessado selecionando o ícone de notificações.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::

Em notificações, selecione **mais eventos no log de atividades** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::

Em seguida, procure o nome do recurso ou outras informações usadas para criar o recurso para localizar a implantação com falha e sua ID de correlação. O exemplo a seguir mostra os resultados da pesquisa em um recurso de nuvem privada (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::
 
Selecionar o nome da operação da implantação com falha abre uma janela com detalhes. Selecione a guia JSON e procure CorrelationId. Copie e inclua no SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Coletar a ID do ExpressRoute (URI)
 
Talvez você esteja tentando dimensionar ou emparelhar uma nuvem privada existente com o circuito de ExpressRoute de nuvem privada e falhar. Nesse caso, você precisará da ID do ExpressRoute. 

No portal do Azure, selecione **conectividade > ExpressRoute** e copie a **ID do ExpressRoute** para a área de transferência.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Falha na implantação de nuvem privada com ID de correlação."::: 
 
> [!NOTE]
> Ocasionalmente, as verificações de validação podem falhar antes da implantação, e as únicas informações disponíveis serão as mensagens de erro e falha. Eles podem ser úteis em muitas falhas, por exemplo, problemas relacionados a cotas, e é importante incluir essas mensagens na solicitação de suporte. Para coletar esses, consulte a seção anterior, [coletar um resumo de erros](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Criar sua solicitação de suporte

Para obter diretrizes gerais sobre como criar sua solicitação de suporte, consulte [como criar uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Aqui está uma orientação específica para a criação de uma implantação da solução do SR para Azure ou falhas de provisionamento.

1. Selecione o ícone de **ajuda** e **+ nova solicitação de suporte** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::

2. Preencha todos os campos obrigatórios e na guia **noções básicas** :

    - Para **tipo de problema** , selecione **problemas de configuração e** configuração.

    - Para **subtipo de problema** , selecione **provisionar uma nuvem privada** .

3. Na guia **detalhes** :

    - Preencha todos os campos obrigatórios.

    - Cole sua ID de correlação ou a ID do ExpressRoute nos campos específicos fornecidos. Se você não vir um campo específico, poderá colá-los na caixa de texto em **fornecer detalhes sobre o problema.**

    - Cole os detalhes do erro, incluindo o resumo dos erros que você copiou, na caixa de texto em **fornecer detalhes sobre o problema.**

4. Examine e selecione **criar** para criar seu Sr.
