---
title: Tutorial de resposta a incidentes – Central de Segurança do Azure
description: Neste tutorial, você aprenderá a fazer a triagem de alertas de segurança, determinar a causa raiz e o escopo de um incidente e pesquisar dados de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 49e2af8fd2d71e1e5103f5337d5c7104c2904322
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559195"
---
# <a name="tutorial-respond-to-security-incidents"></a>Tutorial: Responder a incidentes de segurança
A Central de Segurança analisa continuamente as cargas de trabalho de nuvem híbrida usando a análise avançada e a inteligência contra ameaças para lhe avisar a existência de atividades mal-intencionadas. Além disso, você pode integrar à Central de Segurança alertas de outros produtos e serviços de segurança, bem como criar alertas personalizados com base em indicadores próprios ou fontes de inteligência. Após a geração de um alerta, é necessária uma ação rápida para investigação e correção da questão. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Classificação de alertas de segurança
> * Investigue mais para determinar a causa raiz e o escopo de um incidente de segurança
> * Pesquisar os dados de segurança para ajudar na investigação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites
Para percorrer os recursos abordados neste tutorial, você deve estar em um tipo de preço da Central de Segurança Padrão. Você pode experimentar a Central de Segurança Standard sem nenhum custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Integração da sua assinatura do Azure à Central de Segurança Standard](security-center-get-started.md) orienta você sobre como fazer upgrade para Standard.

## <a name="scenario"></a>Cenário
A Contoso migrou recentemente alguns dos seus recursos locais para o Azure, incluindo uma linha de cargas de trabalho de negócios e bancos de dados SQL baseada em máquinas virtuais. No momento, a principal Equipe de Resposta a Incidentes de Segurança de Computação (CSIRT) da Contoso tem um problema para investigar os problemas de segurança devido à inteligência de segurança não estar integrada em suas ferramentas atuais de resposta a incidentes. Essa falta de integração apresenta um problema durante a detecção (muitos falsos positivos) e durante os estágios de avaliação e diagnóstico. Como parte da migração, eles decidiram optar pela Central de Segurança para ajudar a resolver o problema.

A primeira fase da migração terminou após a integração de todos os recursos e do endereçamento de todas as recomendações de segurança da Central de Segurança. A CSIRT da Contoso é o ponto focal para lidar com os incidentes de segurança do computador. A equipe consiste em um grupo de pessoas com responsabilidades para lidar com qualquer incidente de segurança. Os membros da equipe definiram claramente as tarefas para garantir que nenhuma área de resposta fique descoberta.

Para esse cenário, focaremos nas funções do personas a seguir que fazem parte da CSIRT da Contoso:

![Ciclo de vida da resposta a incidentes](./media/tutorial-security-incident/security-center-incident-response.png)

A Laura está no setor de operações de segurança. Suas responsabilidades incluem:

* Monitorar e responder às ameaças de segurança 24 horas por dia.
* Expandir o proprietário das cargas de trabalho de nuvem ou o analista de segurança quando necessário.

Sam é analista de segurança e suas responsabilidades incluem:

* Investigar os ataques.
* Corrigir os alertas.
* Trabalhar com os proprietários da carga de trabalho para determinar e aplicar atenuações.

Como você pode ver, Laura e Sam têm responsabilidades diferentes e devem trabalhar juntos para compartilhar as informações da Central de Segurança.

## <a name="triage-security-alerts"></a>Classificação de alertas de segurança
A Central de Segurança fornece uma visualização unificada de todos os alertas de segurança. Os alertas de segurança são classificados com base na gravidade e quando possíveis alertas relacionados são combinados em um incidente de segurança. Ao fazer a triagem dos incidentes e alertas, você deve:

- Ignorar alertas para os quais não é necessária nenhuma ação adicional, por exemplo, se o alerta for um falso positivo
- Agir para corrigir os ataques conhecidos, por exemplo, bloquear o tráfego de rede de um endereço IP mal-intencionado
- Determinar os alertas que exigem mais investigação


1. No menu principal da Central de Segurança, em **DETECÇÃO**, selecione **Alertas de segurança**:

   ![Alertas de segurança](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. Na lista de alertas, clique em um incidente de segurança, que é uma coleção de alertas, para saber mais sobre o incidente. A tela **Incidente de segurança detectado** abre.

   ![Incidente de segurança](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Nessa tela, você tem a descrição do incidente de segurança na parte superior e a lista de alertas que fazem parte do incidente. Clique no alerta que você deseja continuar investigando para obter mais informações.

   ![Incidente de segurança](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   O tipo de alerta pode variar, leia [Noções básicas sobre os alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) para obter mais detalhes sobre o tipo de alerta e as etapas de correção possíveis. Para os alertas que podem ser ignorados com segurança, clique com o botão direito no alerta e selecione a opção **Ignorar**:

   ![Alerta](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Se a causa raiz e o escopo da atividade mal-intencionada forem desconhecidos, vá para a próxima etapa para continuar investigando.

## <a name="investigate-an-alert-or-incident"></a>Investigar um alerta ou incidente
1. Na página **Alerta de segurança**, clique no botão **Iniciar a investigação** (se você já tiver iniciado, o nome será alterado para **Continuar investigação**).

   ![Investigação](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   O mapa de investigação é uma representação gráfica das entidades conectadas a esse alerta de segurança ou um incidente. Ao clicar em uma entidade no mapa, as informações sobre a entidade mostrarão novas entidades, e o mapa expandirá. As propriedades da entidade selecionada no mapa são realçadas no painel à direita da página. As informações disponíveis em cada guia variam de acordo com a entidade selecionada. Durante o processo de investigação, examine todas as informações relevantes para entender melhor o movimento do invasor.

2. Se você precisar de mais evidências ou tiver de investigar mais as entidades encontradas durante a investigação, vá para a próxima etapa.

## <a name="search-data-for-investigation"></a>Dados de pesquisa para investigação

Você pode usar os recursos de pesquisa na Central de Segurança para encontrar mais evidências de sistemas comprometidos e obter mais detalhes sobre as entidades que fazem parte da investigação.

Para executar uma pesquisa, abra o painel da **Central de Segurança**, clique em **Pesquisa** no painel de navegação esquerdo, selecione o workspace que contém as entidades a serem pesquisadas, digite a consulta de pesquisa e clique no botão de pesquisa.

## <a name="clean-up-resources"></a>Limpar recursos
Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e os guias de início rápido subsequentes, continue executando a camada Standard e mantenha o provisionamento automático habilitado. Se você não planejar continuar ou quiser retornar para a camada Gratuita:

1. Retorne ao menu principal da Central de Segurança e selecione a **Política de segurança**.
2. Selecione a assinatura ou a política que você deseja retornar para Gratuita. A **Política de segurança** abre.
3. Em **COMPONENTES DE POLÍTICA**, selecione **Tipo de preços**.
4. Selecione **Gratuita** para alterar a assinatura da camada Standard para a camada Gratuita.
5. Clique em **Salvar**.

Se quiser desabilitar o provisionamento automático:

1. Retorne ao menu principal da Central de Segurança e selecione **Política de segurança**.
2. Selecione a assinatura em que você deseja desabilitar o provisionamento automático.
3. Em **Política de segurança – Coleta de dados**, selecione **Desativar** em **Integração** para desabilitar o provisionamento automático.
4. Clique em **Salvar**.

>[!NOTE]
> Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure em que o agente tenha sido provisionado. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu sobre os recursos da Central de Segurança a serem usados quando se responde a um incidente de segurança, por exemplo:

> [!div class="checklist"]
> * Incidente de segurança, que é uma agregação de alertas relacionados de um recurso
> * Mapa de investigação, que é uma representação gráfica das entidades conectadas a um alerta ou incidente de segurança
> * Recursos de pesquisa para localizar mais evidências dos sistemas comprometidos

Para saber mais sobre o recurso de investigação da Central de Segurança, veja:

> [!div class="nextstepaction"]
> [Investigar os incidentes e os alertas](security-center-investigation.md)
