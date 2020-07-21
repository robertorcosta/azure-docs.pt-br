---
title: Obtenha ajuda com a implantação de solução do Azure VMware ou falhas de provisionamento
description: Como obter as informações de que você precisa de sua nuvem privada da AVS (solução VMware) do Azure para arquivar uma solicitação de serviço para implantação de AVS ou falhas de provisionamento.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 05c9dee088b37c37cdcdee7e745cdcd2222b63e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514570"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Obtenha ajuda com a implantação de solução do Azure VMware ou falhas de provisionamento

Neste artigo, você aprenderá a obter ajuda com a implantação da AVS (solução do Azure VMware) ou falhas de provisionamento em sua nuvem privada, abrindo uma solicitação de serviço (SR) no portal do Azure. No entanto, primeiro você precisa coletar algumas informações importantes na portal do Azure. Na maioria dos casos, você precisa do:

- ID de correlação (da implantação com falha)
- ID de circuito do ExpressRoute (ao tentar dimensionar ou emparelhar uma nuvem privada existente com o circuito de ExpressRoute da nuvem privada e falha)

## <a name="collect-the-correlation-id"></a>Coletar a ID de correlação
 
Vamos examinar a ID de correlação primeiro. Quando você cria uma nuvem privada (ou qualquer recurso no Azure), uma ID de correlação associada é gerada. Cada implantação de Azure Resource Manager também gera uma ID de correlação exclusiva. Essa ID permite a criação e resolução de SR mais rápidas. 
 
Aqui está um exemplo da saída de uma implantação de nuvem privada com falha, com a ID de correlação realçada.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Falha na implantação de nuvem privada com ID de correlação.":::

Copie e salve essa ID de correlação para incluir na solicitação de serviço. Para obter detalhes, consulte [criar sua solicitação de suporte](#create-your-support-request) no final deste artigo.

Se a falha ocorrer nos estágios de validação prévia, antes da implantação de uma nuvem privada, nenhuma ID de correlação será gerada. Nesse caso, você pode simplesmente fornecer as informações que usou ao criar a nuvem privada da AVS, incluindo:

- Localização
- Grupo de recursos
- Nome do recurso
 
### <a name="collect-a-summary-of-errors"></a>Coletar um resumo de erros

Os detalhes de qualquer erro também podem ser úteis para resolver o problema. Na tela anterior, selecione **clique aqui para obter detalhes** (realçado) e um resumo de erros será aberto, conforme mostrado na captura de tela a seguir.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Resumo de erros.":::

Novamente, copie e salve esse Resumo para incluir no SR.
 
### <a name="retrieve-past-deployments"></a>Recuperar implantações anteriores

Você pode recuperar as implantações anteriores, incluindo as que falharam, pesquisando no log de atividades de implantação acessado selecionando o ícone de notificações.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Abra notificações.":::

Em notificações, selecione **mais eventos no log de atividades**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Link: mais eventos no log de atividades.":::

Em seguida, pesquise o nome do recurso ou em outra informação exclusiva usada na criação do recurso, para localizar a implantação com falha e sua ID de correlação. O exemplo a seguir mostra os resultados da pesquisa em um recurso de nuvem privada (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Encontrar implantações de AVS com falha anteriores.":::
 
Selecionar o nome da operação da implantação com falha abre uma janela com detalhes. Selecione a guia JSON e procure CorrelationId. Copie e inclua no SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Coletar a ID do ExpressRoute (URI)
 
Talvez você já tenha uma nuvem privada e ocorra uma falha quando estiver tentando dimensioná-la ou emparelhar com o circuito de ExpressRoute de nuvem privada. Nesse caso, a ID do ExpressRoute da nuvem privada pode ser usada para identificá-la quando você cria um SR.

Ao exibir uma nuvem privada no portal, selecione **conectividade > ExpressRoute** e copie a **ID do expressroute** para a área de transferência.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Colete uma ID do ExpressRoute."::: 
 
Cole a ID do ExpressRoute no campo apropriado na nova solicitação de suporte. Para obter mais informações, consulte a seção a seguir, [criar sua solicitação de suporte](#create-your-support-request).
 
> [!NOTE]
> Ocasionalmente, as verificações de validação podem falhar antes de uma implantação e as únicas informações disponíveis serão as mensagens de erro e/ou falha. Isso pode ser útil em várias falhas, por exemplo, problemas relacionados à cota, e é importante incluir essas mensagens na solicitação de suporte. Para coletar esses, consulte a seção anterior, [coletar um resumo de erros](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Criar sua solicitação de suporte

Para obter diretrizes gerais sobre como criar sua solicitação de suporte, consulte [como criar uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Aqui estão as diretrizes adicionais específicas para a criação de uma implantação do SR for AVS ou falhas de provisionamento.

1. Selecione o ícone de **ajuda** e **+ nova solicitação de suporte**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Colete uma ID do ExpressRoute.":::

2. Preencha todos os campos obrigatórios e na guia **noções básicas** :

    - Para **tipo de problema**, selecione **problemas de configuração e**configuração.

    - Para **subtipo de problema**, selecione **provisionar uma nuvem privada**.

3. Na guia **detalhes** :

    - Preencha todos os campos obrigatórios.

    - Cole sua ID de correlação ou a ID do ExpressRoute nos campos específicos fornecidos. Se você não vir um campo específico para eles, poderá colá-los na caixa de texto em **fornecer detalhes sobre o problema.**

    - Cole os detalhes do erro, incluindo o resumo dos erros que você copiou, na caixa de texto em **fornecer detalhes sobre o problema.**

4. Examine e selecione **criar** para criar seu Sr.
