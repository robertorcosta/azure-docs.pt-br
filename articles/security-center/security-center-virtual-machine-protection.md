---
title: Proteja seus computadores e aplicativos
description: Este documento aborda as recomendações da Central de Segurança que ajudam a proteger suas máquinas virtuais, seus computadores e os ambientes de aplicativos Web e do Serviço de Aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: b7e5b0286cdd15834b84e4fd3e619c6555054823
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552994"
---
# <a name="protect-your-machines-and-applications"></a>Proteja seus computadores e aplicativos
Quando a central de segurança do Azure identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a página **computação e aplicativos** da seção segurança de recursos da central de segurança.

Para obter uma lista completa das recomendações que podem ser exibidas nesta página, consulte [recomendações de armazenamento e dados](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Exibir a segurança de seus recursos de computação e aplicativos

[painel da central de segurança ![](./media/security-center-virtual-machine-recommendations/overview.png)](./media/security-center-virtual-machine-recommendations/overview.png#lightbox)

Para exibir o status dos seus recursos de computação e aplicativos, selecione **computação & aplicativos** em **recursos** na barra lateral da central de segurança. As seguintes guias estão disponíveis:

* **Visão geral**: lista as recomendações para todos os recursos de computação e aplicativos, bem como seu status de segurança atual 

* [**VMs e computadores**](#vms-and-computers): lista as recomendações para suas VMs, computadores e o estado de segurança atual de cada

* [**Conjuntos de dimensionamento de VM**](#vmscale-sets): lista as recomendações para seus conjuntos de dimensionamento, 

* [**Serviços de nuvem**](#cloud-services): lista as recomendações para suas funções Web e de trabalho monitoradas pela central de segurança

* [**Serviços de aplicativos**](#app-services): lista as recomendações para seus ambientes de serviço de aplicativo e o estado de segurança atual de cada um

* **Contêineres**: lista as recomendações para seus contêineres e avaliação de segurança de suas configurações

* **Recursos de computação**: lista as recomendações para seus recursos de computação, como clusters de Service Fabric e hubs de eventos

### <a name="whats-in-each-tab"></a>O que há em cada guia?

Cada guia tem várias seções e, em cada seção, você pode fazer uma busca detalhada para ver detalhes adicionais sobre o item mostrado.

Em cada guia, você também verá as recomendações para os recursos relevantes em seu ambiente monitorado. A primeira coluna lista a recomendação, a segunda mostra o número total de recursos afetados e a terceira mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que você poderá executar depois de selecioná-la. Por exemplo, se você selecionar **Atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a gravidade da atualização ausente serão exibidos.

> [!NOTE]
> As recomendações de segurança são as mesmas da página **recomendações** , mas aqui são filtradas para o tipo de recurso específico que você selecionou. Para obter mais informações sobre como resolver recomendações, consulte [implementando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>VMs e computadores
A seção VMs e computadores fornece uma visão geral de todas as recomendações de segurança para suas VMs e computadores. Quatro tipos de computadores estão incluídos:

![Computador não Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM do Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM do Azure Resource Manager.

![VM Clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM Clássica do Azure.

![VMs identificadas no workspace](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs identificadas somente pelo workspace que faz parte da assinatura exibida. Isso inclui VMs de outras assinaturas que são relatadas para o espaço de trabalho nessa assinatura e VMs que foram instaladas com o Operations Manager agente direto e não têm nenhuma ID de recurso.

O ícone que aparece em cada recomendação ajuda a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Você também pode usar os filtros para pesquisar a lista por **Tipo de recurso** e por **Gravidade**.

Para detalhar as recomendações de segurança para cada VM, clique na VM.
Veja aqui os detalhes de segurança da VM ou do computador. Na parte inferior, você pode ver a ação recomendada e a gravidade de cada problema.

[serviços de nuvem ![](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)

### <a name="cloud-services"></a>Serviços de nuvem
Para serviços de nuvem, uma recomendação será criada quando a versão do sistema operacional estiver desatualizada.

![Serviços de nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Em um cenário em que você tem uma recomendação, siga as etapas na recomendação para atualizar o sistema operacional. Quando uma atualização estiver disponível, você terá um alerta (vermelho ou laranja, dependendo da gravidade do problema). Para obter uma explicação completa dessa recomendação, clique em **Atualizar versão do sistema operacional** na coluna **Descrição** .

### <a name="app-services"></a>Serviços de aplicativos
Para exibir as informações do serviço de aplicativo, você deve estar no tipo de preço Standard da central de segurança e habilitar o serviço de aplicativo em sua assinatura. Para obter instruções sobre como habilitar esse recurso, consulte [proteger o serviço de aplicativo com a central de segurança do Azure](security-center-app-services.md).


Em **Serviços de Aplicativos**, você encontra uma lista de seus ambientes do Serviço de Aplicativo e o resumo de integridade com base na avaliação executada pela Central de Segurança.

![Serviços de aplicativos](./media/security-center-virtual-machine-recommendations/app-services.png)

Há três tipos de serviços de aplicativo mostrados:

![Ambiente de Serviços de Aplicativos](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de Serviços de Aplicativos

![Aplicativo Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicativo Web

![Aplicativo de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicativo de função

Se você selecionar um aplicativo Web, uma exibição de resumo será aberta com três guias:

   - **Recomendações**: com base em avaliações executadas pela central de segurança que falharam.
   - **Avaliações aprovadas**: lista de avaliações realizadas pela Central de Segurança que foram aprovadas.
   - **Avaliações não disponíveis**: lista de avaliações que falharam devido a um erro ou a recomendação não é relevante para o Serviço de Aplicativo específico

   Em **Recomendações** há uma lista das recomendações para o aplicativo Web selecionado e a gravidade de cada recomendação.

   ![Recomendações dos Serviços de Aplicativos](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não íntegros, recursos íntegros e recursos não examinados.

   - A coluna **avaliações passadas** mostra uma lista de avaliações aprovadas. Gravidade dessas avaliações sempre é verde.

   - Selecione uma avaliação passada na lista para obter uma descrição da avaliação, uma lista de recursos íntegros e não íntegros e uma lista de recursos não examinados. Há uma guia para os recursos não íntegros, mas essa lista está sempre vazia, pois a avaliação foi aprovada.

### <a name="vmscale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
A central de segurança descobre automaticamente se você tem conjuntos de dimensionamento e recomenda que você instale o Microsoft Monitoring Agent neles.

Para instalar o Microsoft Monitoring Agent: 

1. Selecione a recomendação **Instalar o agente de monitoramento no conjunto de dimensionamento de máquinas virtuais**. Uma lista de conjuntos de dimensionamento não monitorados é exibida.

1. Selecione um conjunto de dimensionamento não íntegro. Siga as instruções para instalar o agente de monitoramento usando um workspace preenchido existente ou crie um novo. Certifique-se de definir o [tipo de preço](security-center-pricing.md) do workspace, se ainda não estiver definido.

   ![Instalar o MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de dimensionamento para instalar automaticamente o Microsoft Monitoring Agent:
1. Vá para Azure Policy e clique em **Definições**.

1. Procure a política **implantar log Analytics agente para conjuntos de dimensionamento de máquinas virtuais do Windows** e clique nele.

1. Clique em **Atribuir**.

1. Defina o **Escopo** e o **espaço de trabalho do Log Analytics** e clique em **Atribuir**.

Se você quiser definir todos os conjuntos de dimensionamento existentes para instalar o Microsoft Monitoring Agent, no Azure Policy, vá para **Correção** e aplique a política existente aos conjuntos de dimensionamento existentes.


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes artigos:

* [Lista de referências completa das recomendações de segurança da central de segurança do Azure](recommendations-reference.md)
* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)