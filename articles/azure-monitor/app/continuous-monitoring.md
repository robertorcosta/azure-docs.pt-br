---
title: Monitoramento contínuo do pipeline de liberação de DevOps com a zure Pipelines e insights de aplicativos Do Zure | Microsoft Docs
description: Fornece instruções para configurar rapidamente o monitoramento contínuo com o Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655388"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicionar monitoramento contínuo ao seu pipeline de versão

O Azure Pipelines integra-se ao Azure Application Insights para permitir o monitoramento contínuo do pipeline de liberação de DevOps durante todo o ciclo de vida do desenvolvimento de software. 

Com monitoramento contínuo, os pipelines de liberação podem incorporar dados de monitoramento do Application Insights e outros recursos do Azure. Quando o pipeline de liberação detectar um alerta de Application Insights, o pipeline pode porto ou reverter a implantação até que o alerta seja resolvido. Se todas as verificações passarem, as implantações podem proceder automaticamente do teste até a produção, sem a necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configurar o monitoramento contínuo

1. No [Azure DevOps,](https://dev.azure.com)selecione uma organização e um projeto.
   
1. No menu à esquerda da página do projeto, selecione **Lançamentos** > de**pipelines**. 
   
1. Baixe a seta ao lado de **Novo** e selecione **Novo pipeline de versão**. Ou, se você ainda não tiver um pipeline, selecione **Novo pipeline** na página que aparece.
   
1. No **Painel Selecionar um painel de modelo,** procure e selecione **a implantação do Azure App Service com monitoramento contínuo**e selecione **Aplicar**. 

   ![Novo gasoduto de lançamento da Azure Pipelines](media/continuous-monitoring/001.png)

1. Na caixa **Estágio 1,** selecione o hiperlink para **exibir tarefas de estágio.**

   ![Ver tarefas de estágio](media/continuous-monitoring/002.png)

1. No painel de configuração **estágio 1,** complete os seguintes campos: 

    | Parâmetro        | Valor |
   | ------------- |:-----|
   | **Nome do estágio**      | Forneça um nome artístico, ou deixe-o no **Estágio 1**. |
   | **Assinatura do Azure** | Baixe e selecione a assinatura vinculada do Azure que deseja usar.|
   | **Tipo de aplicativo** | Baixe e selecione seu tipo de aplicativo. |
   | **Nome do Serviço de Aplicativo** | Digite o nome do seu Serviço de Aplicativo Azure. |
   | **Nome do Grupo de Recursos para Insights de Aplicativos**    | Baixe e selecione o grupo de recursos que deseja usar. |
   | **Nome do recurso do Application Insights** | Baixe e selecione o recurso Application Insights para o grupo de recursos selecionado.

1. Para salvar o pipeline com configurações de regra de alerta padrão, **selecione Salvar** no canto superior direito na janela Azure DevOps. Digite um comentário descritivo e selecione **OK**.

## <a name="modify-alert-rules"></a>Modificar as regras de alerta

Fora da caixa, a implantação do **Azure App Service com** modelo de monitoramento contínuo tem quatro regras de alerta: **disponibilidade,** **solicitações com falha,** **tempo de resposta do servidor**e **exceções do servidor.** Você pode adicionar mais regras ou alterar as configurações de regras para atender às suas necessidades de nível de serviço. 

Para modificar as configurações de regra de alerta:

1. No painel esquerdo da página de pipeline de versão, selecione **Configure Application Insights Alerts**.

1. No **painel Alertas do Monitor do Azure,** selecione a elipse **...** ao lado das regras **de alerta**.
   
1. Na caixa de diálogo **'Alerta' regras,** selecione o símbolo de baixa ao lado de uma regra de alerta, como **Disponibilidade**. 
   
1. Modifique o **Limiar** e outras configurações para atender aos seus requisitos.
   
   ![Modificar alerta](media/continuous-monitoring/003.png)
   
1. Selecione **OK**e selecione **Salvar** no canto superior direito na janela Azure DevOps. Digite um comentário descritivo e selecione **OK**.

## <a name="add-deployment-conditions"></a>Adicionar condições de implantação

Quando você adiciona portões de implantação ao seu pipeline de liberação, um alerta que excede os limites definidos impede a promoção de lançamento indesejada. Uma vez que você resolve o alerta, a implantação pode prosseguir automaticamente.

Para adicionar portões de implantação:

1. Na página principal do pipeline, em **Etapas,** selecione as **condições de pré-implantação** ou o símbolo **de condições de pós-implantação,** dependendo de qual estágio precisa de um portão de monitoramento contínuo.
   
   ![Condições de pré-implantação](media/continuous-monitoring/004.png)
   
1. No painel de configuração **de condições de pré-implantação,** defina **Gates** **como Ativado**.
   
1. Ao lado **dos portões de implantação,** **selecione Adicionar**.
   
1. Selecione **Alertas do Monitor do Azure** do menu suspenso. Esta opção permite que você acesse os alertas do Azure Monitor e do Application Insights.
   
   ![Consulta alertas do Monitor do Azure](media/continuous-monitoring/005.png)
   
1. Em **Opções de Avaliação,** insira os valores desejados para configurações como **O tempo entre a reavaliação dos portões** e o tempo após o qual os **portões falham**. 

## <a name="view-release-logs"></a>Exibir registros de liberação

Você pode ver o comportamento do portão de implantação e outras etapas de liberação nos registros de liberação. Para abrir os registros:

1. Selecione **Lançamentos** no menu esquerdo da página do pipeline. 
   
1. Selecione qualquer versão. 
   
1. Em **Estágios,** selecione qualquer estágio para exibir um resumo de liberação. 
   
1. Para exibir logs, **selecione Exibir logs** no resumo da versão, selecione o hiperlink **''Com sucesso** ou **falha',** em qualquer etapa', ou hover sobre qualquer etapa e selecione **Logs**. 
   
   ![Exibir registros de liberação](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a Azure Pipelines, consulte a documentação do [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
