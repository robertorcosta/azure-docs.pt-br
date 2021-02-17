---
title: 'Tutorial: Verificações de conformidade regulatória – Central de Segurança do Azure'
description: 'Tutorial: Saiba como melhorar a conformidade regulatória usando a Central de Segurança do Azure.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550889"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar sua conformidade regulatória

A Central de Segurança do Azure ajuda a simplificar o processo para atender aos requisitos de conformidade regulatória, usando o **painel de conformidade regulatória**. 

A Central de Segurança avalia continuamente o seu ambiente de nuvem híbrida para analisar os fatores de risco de acordo com os controles e as práticas recomendadas nos padrões aplicados às suas assinaturas. O painel reflete o status de sua conformidade com esses padrões. 

Quando você habilita a Central de Segurança em uma assinatura do Azure, o [Azure Security Benchmark](../security/benchmarks/introduction.md) é automaticamente atribuído a ela. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

O painel de conformidade regulatória mostra o status de todas as avaliações no seu ambiente para os seus padrões e regulamentos escolhidos. Conforme você tomar decisões com base nas recomendações e reduzir os fatores de risco em seu ambiente, sua postura de conformidade melhorará.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Avaliar sua conformidade regulatória usando o painel de conformidade regulatória
> * Melhorar sua postura de conformidade tomando decisões com base nas recomendações
> * Configure alertas nas alterações da sua postura de conformidade
> * Exporte os seus dados de conformidade como um fluxo contínuo e como instantâneos semanais

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar os recursos abordados neste tutorial:

- O [Azure Defender](azure-defender.md) precisa estar habilitado. Você pode experimentar o Azure Defender gratuitamente por 30 dias.
- Você precisa estar conectado a uma conta que tenha acesso de leitor aos dados de conformidade da política (**Leitor de Segurança** é insuficiente). A função de **Leitor global** da assinatura pode ser usada. No mínimo, você precisará ter as funções **Colaborador de Política de Recursos** e **Administrador de Segurança** atribuídas.

##  <a name="assess-your-regulatory-compliance"></a>Avaliar sua conformidade regulatória

O painel de conformidade regulatória mostra os padrões de conformidade que você selecionou com todos os respectivos requisitos, em que os requisitos compatíveis são mapeados para as avaliações de segurança aplicáveis. O status dessas avaliações reflete sua conformidade com o padrão.

Use o painel de conformidade regulatória como auxílio para concentrar a sua atenção nas lacunas de conformidade com os padrões e regulamentos escolhidos. Essa exibição concentrada também possibilita que você monitore continuamente sua conformidade ao longo do tempo em ambientes dinâmicos híbridos e de nuvem.

1. No menu da Central de Segurança, selecione **Conformidade regulatória**.

    Na parte superior da tela, você verá um painel com uma visão geral de seu status de conformidade com o conjunto de regulamentos de conformidade compatíveis. Você verá a sua pontuação geral de conformidade e o número de avaliações aprovadas vs. reprovadas associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulatória" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecione uma guia para um padrão de conformidade relevante para você (1). Você verá a quais assinaturas o padrão é aplicado (2) e a lista de todos os controles para esse padrão (3). Para os controles aplicáveis, você poderá ver os detalhes das avaliações aprovadas e reprovadas associadas a esse controle (4), bem como os números dos recursos afetados (5). Alguns controles estão esmaecidos. Esses controles não têm nenhuma avaliação da Central de Segurança associada a eles. Verifique os requisitos deles e avalie-os em seu ambiente por conta própria. Alguns deles podem ser relacionados ao processo e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes da conformidade com um padrão específico":::

1. Para gerar um relatório em PDF com um resumo do seu status de conformidade atual para um padrão específico, selecione **Baixar o relatório**.

    O relatório fornece um resumo de alto nível do status de conformidade para o padrão escolhido tendo como base os dados das avaliações da Central de Segurança e é organizado de acordo com os controles desse padrão específico. O relatório pode ser compartilhado com stakeholders relevantes e pode servir para fornecer evidências aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Baixar relatório de conformidade":::

## <a name="improve-your-compliance-posture"></a>Melhorar sua postura de conformidade

Usando as informações do painel de conformidade regulatória, aprimore a sua postura de conformidade resolvendo as recomendações diretamente no painel.

1.  Clique em uma das avaliações reprovadas mostradas no painel para exibir os detalhes dessa recomendação. Cada recomendação inclui um conjunto de etapas de correção que devem ser seguidas para resolver o problema.

1.  Selecione um recurso específico para ver mais detalhes e resolva a recomendação desse recurso. <br>Por exemplo, no padrão **Azure CIS 1.1.0**, selecione a recomendação **A criptografia de disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A seleção de uma recomendação de um padrão leva diretamente para a página de detalhes da recomendação":::

1. Neste exemplo, ao selecionar **Executar ação** na página de detalhes da recomendação, você chegará nas páginas da Máquina Virtual do Azure do portal do Azure, em que é possível habilitar a criptografia na guia **Segurança**:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="O botão Executar ação na página de detalhes da recomendação leva às opções de correção":::

    Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

1.  Depois de tomar medidas para resolver as recomendações, você verá o impacto no relatório do painel de conformidade, devido à melhoria da sua pontuação de conformidade.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas e, portanto, você verá o impacto sobre seus dados de conformidade somente após a próxima execução da avaliação relevante.


## <a name="export-your-compliance-status-data"></a>Exportar os seus dados de status de conformidade

Se você quiser acompanhar o seu status de conformidade com outras ferramentas de monitoramento no seu ambiente, a Central de Segurança incluirá um mecanismo de exportação para fazer isso de modo simples. Configure a **exportação contínua** para enviar os dados selecionados para um Hub de Eventos do Azure ou um workspace do Log Analytics.

Use os dados de exportação contínua para um Hub de Eventos do Azure ou um workspace do Log Analytics:

- Exporte todos os dados de conformidade regulatória em um **fluxo contínuo**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exportar continuamente um fluxo de dados de conformidade regulatória" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportar **instantâneos semanais** dos seus dados de conformidade regulatória:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exportar continuamente um instantâneo semanal dos dados de conformidade regulatória" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Você também pode exportar um **relatório PDF/CSV** dos seus dados de conformidade diretamente do painel de conformidade regulatória:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportar os seus dados de conformidade regulatória como um relatório PDF ou CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Saiba mais em [Exportar continuamente dados da Central de Segurança](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Executar automações de fluxo de trabalho quando houver alterações na conformidade

O recurso de automação do fluxo de trabalho da Central de Segurança pode disparar Aplicativos Lógicos sempre que uma das suas avaliações de conformidade regulatória mudar de estado.

Por exemplo, talvez você queira que a Central de Segurança envie por email um usuário específico quando uma avaliação de conformidade falhar. Você precisará criar o aplicativo lógico (usando [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md)) primeiro e, depois, configurar o gatilho em uma nova automação de fluxo de trabalho, conforme explicado em [Automatizar as respostas para os gatilhos da Central de Segurança](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Como usar as avaliações de conformidade regulatória para disparar uma automação de fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o painel de conformidade regulatória da Central de Segurança para:

- Exiba e monitore a sua postura de conformidade com relação aos padrões e aos regulamentos importantes para você.
- Melhorar seu status de conformidade resolvendo recomendações relevantes e observando a melhoria da pontuação de conformidade.

O painel de conformidade regulatória pode simplificar muito o processo de conformidade e reduzir significativamente o tempo necessário para a coleta de evidências de conformidade para o ambiente híbrido, do Azure e de várias nuvens.

Para saber mais, confira estas páginas relacionadas:

- [Personalize o conjunto de padrões no seu painel de conformidade regulatória](update-regulatory-compliance-packages.md) – Saiba como selecionar quais padrões aparecem no seu painel de conformidade regulatória. 
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como usar as recomendações da Central de Segurança do Azure para ajudar a proteger seus recursos do Azure.