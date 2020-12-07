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
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: bbc36dbb2a17d379d31a9a235898500aea36247d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533903"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar sua conformidade regulatória

A Central de Segurança do Azure ajuda a simplificar o processo para atender aos requisitos de conformidade regulatória, usando o **painel de conformidade regulatória**. No painel, a Central de Segurança fornece insights sobre sua postura de conformidade, com base em avaliações contínuas de seu ambiente do Azure. A Central de Segurança analisa fatores de risco em seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Essas avaliações são mapeadas para controles de conformidade de um conjunto compatível de padrões. No painel de conformidade regulatória, você pode ver o status de todas as avaliações em seu ambiente no contexto de um padrão ou regulamento específico. Conforme você tomar decisões com base nas recomendações e reduzir os fatores de risco em seu ambiente, sua postura de conformidade melhorará.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Avaliar sua conformidade regulatória usando o painel de conformidade regulatória
> * Melhorar sua postura de conformidade tomando decisões com base nas recomendações

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar os recursos abordados neste tutorial:

- O [Azure Defender](azure-defender.md) precisa estar habilitado. Você pode experimentar o Azure Defender gratuitamente por 30 dias.
- Você precisa estar conectado a uma conta que tenha acesso de leitor aos dados de conformidade da política (**Leitor de Segurança** é insuficiente). A função de **Leitor global** da assinatura pode ser usada. No mínimo, você precisará ter as funções **Colaborador de Política de Recursos** e **Administrador de Segurança** atribuídas.

##  <a name="assess-your-regulatory-compliance"></a>Avaliar sua conformidade regulatória

A Central de Segurança avalia a configuração de seus recursos continuamente para identificar problemas de segurança e vulnerabilidades. Essas avaliações são apresentadas como recomendações, que se concentram na melhoria da higiene de segurança. No painel de conformidade regulatória, você pode ver um conjunto de padrões de conformidade com todos os requisitos, em que os requisitos compatíveis são mapeados para as avaliações de segurança aplicáveis. Isso permite que você exiba sua postura de conformidade em relação ao padrão, com base no status dessas avaliações.

A exibição do painel Conformidade regulatória pode ajudar você a concentrar sua atenção nas lacunas de conformidade com um padrão ou um regulamento importante para você. Essa exibição concentrada também possibilita que você monitore continuamente sua pontuação de conformidade ao longo do tempo em ambientes dinâmicos híbridos e de nuvem.

>[!NOTE]
> Por padrão, a Central de Segurança dá suporte aos seguintes padrões regulatórios: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> O recurso de [pacotes de conformidade dinâmica (versão prévia)](update-regulatory-compliance-packages.md) permite que você atualize os padrões mostrados em seu painel de conformidade regulatória para os novos pacotes *dinâmicos*. Você também pode usar a mesma versão prévia do recurso para adicionar novos pacotes de conformidade e monitorar a conformidade com padrões adicionais. 

1. No menu da Central de Segurança, selecione **Conformidade regulatória**. <br>
Na parte superior da tela, você verá um painel com uma visão geral de seu status de conformidade com o conjunto de regulamentos de conformidade compatíveis. Você poderá ver sua pontuação geral de conformidade e o número de avaliações aprovadas vs. reprovadas associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulatória":::

1. Selecione uma guia para um padrão de conformidade relevante para você (1). Você verá a quais assinaturas o padrão é aplicado (2) e a lista de todos os controles para esse padrão (3). Para os controles aplicáveis, você poderá ver os detalhes das avaliações aprovadas e reprovadas associadas a esse controle (4), bem como os números dos recursos afetados (5). Alguns controles estão esmaecidos. Esses controles não têm nenhuma avaliação da Central de Segurança associada a eles. Verifique os requisitos deles e avalie-os em seu ambiente por conta própria. Alguns deles podem ser relacionados ao processo e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes da conformidade com um padrão específico":::

1. Para gerar e fazer o download de um relatório em PDF que resuma seu status de conformidade atual para um padrão específico, clique em **Baixar o relatório**.

    O relatório fornece um resumo de alto nível do status de conformidade para o padrão escolhido tendo como base os dados das avaliações da Central de Segurança e é organizado de acordo com os controles desse padrão específico. O relatório pode ser compartilhado com stakeholders relevantes e pode servir para fornecer evidências aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Baixar relatório de conformidade":::

## <a name="improve-your-compliance-posture"></a>Melhorar sua postura de conformidade

Considerando as informações do painel de conformidade regulatória, você poderá aprimorar sua postura de conformidade resolvendo as recomendações diretamente no painel.

1.  Clique em uma das avaliações reprovadas mostradas no painel para exibir os detalhes dessa recomendação. Cada recomendação inclui um conjunto de etapas de correção que devem ser seguidas para resolver o problema.

1.  Selecione um recurso específico para exibir mais detalhes e resolva a recomendação para esse recurso. <br>Por exemplo, no **Azure CIS 1.1.0 (Novo) Standard**, você pode selecionar a recomendação **Criptografia de disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A seleção de uma recomendação de um padrão leva diretamente para a página de detalhes da recomendação":::

1. Neste exemplo, ao selecionar **Executar ação** na página de detalhes da recomendação, você chegará nas páginas da Máquina Virtual do Azure do portal do Azure, em que é possível abrir a guia **Segurança** e habilitar a criptografia:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="O botão Executar ação na página de detalhes da recomendação leva às opções de correção":::

    Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

1.  Depois de tomar medidas para resolver as recomendações, você verá o impacto no relatório do painel de conformidade, devido à melhoria de sua pontuação de conformidade.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas e, portanto, você verá o impacto sobre seus dados de conformidade somente após a próxima execução da avaliação relevante.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o painel de conformidade regulatória da Central de Segurança para:

-   Exibir e monitorar sua postura de conformidade com relação aos padrões e aos regulamentos importantes para você.
-   Melhorar seu status de conformidade resolvendo recomendações relevantes e observando a melhoria da pontuação de conformidade.

O painel de conformidade regulatória pode simplificar muito o processo de conformidade e reduzir significativamente o tempo necessário para a coleta de evidências de conformidade para o Azure e seu ambiente híbrido.

Para saber mais, confira estes artigos relacionados:

-   [Atualizar para pacotes de conformidade dinâmica em seu painel de conformidade regulatória (Versão prévia)](update-regulatory-compliance-packages.md) – saiba mais sobre esta versão prévia do recurso que permite atualizar os padrões mostrados em seu painel de conformidade regulatória para os novos pacotes *dinâmicos*. Você também pode usar a mesma versão prévia do recurso para adicionar novos pacotes de conformidade e monitorar a conformidade com padrões adicionais. 
-   [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
-   [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como usar as recomendações da Central de Segurança do Azure para ajudar a proteger seus recursos do Azure.
-   [Aprimorar sua classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md) – saiba como priorizar as vulnerabilidades e as recomendações de segurança para aprimorar ao máximo sua postura de segurança.
