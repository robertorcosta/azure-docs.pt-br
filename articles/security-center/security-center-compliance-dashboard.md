---
title: Verificações de conformidade regulatória – Central de Segurança do Azure
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c448697339948b30d73e88c183211e7a1154fe77
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686430"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar sua conformidade regulatória
---

A Central de Segurança do Azure ajuda a simplificar o processo para atender aos requisitos de conformidade regulatória, usando o **painel de conformidade regulatória**. No painel, a Central de Segurança fornece insights sobre sua postura de conformidade, com base em avaliações contínuas de seu ambiente do Azure. A Central de Segurança analisa fatores de risco em seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Essas avaliações são mapeadas para controles de conformidade de um conjunto compatível de padrões. No Painel de conformidade regulatória, você pode ver o status de todas as avaliações em seu ambiente no contexto de um padrão ou regulamento específico. Conforme você tomar decisões com base nas recomendações e reduzir os fatores de risco em seu ambiente, sua postura de conformidade melhorará.

Neste tutorial, você aprenderá a:

-   Avaliar sua conformidade regulatória usando o painel Conformidade regulatória

-   Melhorar sua postura de conformidade tomando decisões com base nas recomendações

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer os recursos abordados neste tutorial, você precisa ter um tipo de preço Standard da Central de Segurança. Você pode experimentar a Central de Segurança Standard sem nenhum custo.
Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Integração da sua assinatura do Azure à Central de Segurança Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) orienta você sobre como fazer upgrade para Standard.

##  <a name="assess-your-regulatory-compliance"></a>Avaliar sua conformidade regulatória

A Central de Segurança avalia a configuração de seus recursos continuamente para identificar problemas de segurança e vulnerabilidades. Essas avaliações são apresentadas como recomendações, que se concentram na melhoria da higiene de segurança. No painel Conformidade regulatória, você pode exibir um conjunto de padrões de conformidade com todos os requisitos, em que os requisitos compatíveis são mapeados para as avaliações de segurança aplicáveis. Isso permite que você exiba sua postura de conformidade em relação ao padrão, com base no status dessas avaliações.

A exibição do painel Conformidade regulatória pode ajudar você a concentrar sua atenção nas lacunas de conformidade com um padrão ou um regulamento importante para você. Essa exibição concentrada também possibilita que você monitore continuamente sua pontuação de conformidade ao longo do tempo em ambientes dinâmicos híbridos e de nuvem.

>[!NOTE]
> Por padrão, a Central de Segurança dá suporte aos seguintes padrões regulatórios: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> O recurso de [pacotes de conformidade dinâmica (versão prévia)](update-regulatory-compliance-packages.md) permite que você atualize os padrões mostrados em seu painel de conformidade regulatória para os novos pacotes *dinâmicos*. Você também pode usar a mesma versão prévia do recurso para adicionar novos pacotes de conformidade e monitorar a conformidade com padrões adicionais. 

1.  No menu principal da Central de Segurança, em **POLÍTICA E CONFORMIDADE**, escolha **Conformidade regulatória**. <br>
Na parte superior da tela, você verá um painel com uma visão geral de seu status de conformidade com o conjunto de regulamentos de conformidade compatíveis. Você poderá ver sua pontuação geral de conformidade e o número de avaliações aprovadas vs. reprovadas associadas a cada padrão.

    ![alta confiança da descrição do computador](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Selecione uma guia para um padrão de conformidade relevante para você. Você verá a lista de todos os controles para esse padrão. Para os controles aplicáveis, você poderá exibir os detalhes das avaliações aprovadas e reprovadas associadas a esse controle. Alguns controles estão esmaecidos. Esses controles não têm nenhuma avaliação da Central de Segurança associada a eles. Verifique os requisitos deles e avalie-os em seu ambiente por conta própria. Alguns deles podem ser relacionados ao processo e não técnicos.

    ![guia Conformidade](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Para gerar e fazer o download de um relatório em PDF que resuma seu status de conformidade atual para um padrão específico, clique em **Baixar o relatório**.

    O relatório fornece um resumo de alto nível do status de conformidade para o padrão escolhido tendo como base os dados das avaliações da Central de Segurança e é organizado de acordo com os controles desse padrão específico. O relatório pode ser compartilhado com stakeholders relevantes e pode servir para fornecer evidências aos auditores internos e externos.

    ![baixar](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Melhorar sua postura de conformidade

Considerando as informações do painel Conformidade regulatória, você poderá melhorar sua postura de conformidade resolvendo as recomendações diretamente no painel.

1.  Clique em uma das avaliações reprovadas mostradas no painel para exibir os detalhes dessa recomendação. Cada recomendação inclui um conjunto de etapas de correção que devem ser seguidas para resolver o problema.

1.  Selecione um recurso específico para exibir mais detalhes e resolva a recomendação para esse recurso. <br>Por exemplo, na guia **Padrão do Azure CIS**, você poderá clicar na recomendação **Exigir transferência segura para a conta de armazenamento**.

    ![recomendação sobre conformidade](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Quando você clicar nas informações de recomendação e selecionar um recurso não íntegro, isso levará você diretamente para a experiência de habilitação da **transferência de armazenamento segura** no portal do Azure.

    Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

    ![recomendação sobre conformidade](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Depois de tomar medidas para resolver as recomendações, você verá o impacto no relatório do painel de conformidade, devido à melhoria de sua pontuação de conformidade.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas e, portanto, você verá o impacto sobre seus dados de conformidade somente após a execução das avaliações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o painel Conformidade regulatória da Central de Segurança para:

-   Exibir e monitorar sua postura de conformidade com relação aos padrões e aos regulamentos importantes para você.

-   Melhorar seu status de conformidade resolvendo recomendações relevantes e observando a melhoria da pontuação de conformidade.

O painel Conformidade regulatória pode simplificar muito o processo de conformidade e reduzir significativamente o tempo necessário para a coleta de evidências de conformidade para o Azure e o ambiente híbrido.

Para saber mais, confira:

-   [Atualizar para pacotes de conformidade dinâmica em seu Painel de conformidade regulatória (Versão prévia)](update-regulatory-compliance-packages.md) – saiba mais sobre esta versão prévia do recurso que permite atualizar os padrões mostrados em seu painel de conformidade regulatória para os novos pacotes *dinâmicos*. Você também pode usar a mesma versão prévia do recurso para adicionar novos pacotes de conformidade e monitorar a conformidade com padrões adicionais. 

-   [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.

-   [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como usar as recomendações da Central de Segurança do Azure para ajudar a proteger seus recursos do Azure.

-   [Melhorar sua classificação de segurança na Central de Segurança do Azure](security-center-secure-score.md) – saiba como priorizar as vulnerabilidades e as recomendações de segurança para oferecer melhoria máxima para sua postura de segurança.

-   [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
