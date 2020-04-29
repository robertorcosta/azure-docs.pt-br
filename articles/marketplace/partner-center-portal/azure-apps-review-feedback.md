---
title: Lidando com comentários de revisão para a oferta de aplicativos do Azure no Marketplace comercial
description: Como lidar com comentários de revisão para a oferta de aplicativos do Azure para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279786"
---
# <a name="handling-review-feedback"></a>Tratar comentários de revisão

Este artigo explica como acessar o ambiente do Azure DevOps utilizado pela equipe de revisão do Microsoft Azure Marketplace. Se problemas críticos forem localizados na oferta de aplicativo Azure durante a etapa de **revisão da Microsoft**, você poderá entrar nesse sistema para exibir informações detalhadas sobre esses problemas (revisar comentários). Após corrigir todos esses problemas, será necessário reenviar a oferta para continuar a publicá-la no Azure Marketplace. O diagrama a seguir ilustra como esse processo de comentários se relaciona com o processo de publicação.

![Examinar o processo de comentários](./media/review-feedback-process.png)

Normalmente, problemas de revisão são referenciados como uma PR (solicitação de pull). Cada PR é vinculada a um item online do [Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente nomeado VSTS (Visual Studio Team Services)), que contém detalhes sobre o problema. A imagem a seguir exibe um exemplo da experiência do Partner Center se forem encontrados problemas durante as revisões. 

![Status de publicação](./media/publishing-status.png)

A PR que contém detalhes específicos sobre o envio será mencionada no link "Exibir relatório de certificação". Para situações complexas, as equipes de suporte e revisão também poderão enviar-lhe um email.

## <a name="azure-devops-access"></a>Acesso DevOps do Azure

Todos os usuários com acesso à função de "desenvolvedor" no Partner Center terão acesso para exibir os itens de PR referenciados em comentários de revisão.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Revisar a solicitação de pull

Use o procedimento a seguir para revisar os problemas documentados na solicitação de pull.

1. Nas seções de **revisão da Microsoft** do formulário etapas de publicação, clique em um link de PR para iniciar o navegador e navegue até a página **visão geral** (página inicial) desta PR. A imagem a seguir mostra um exemplo do problema crítico home page para a oferta de aplicativo de exemplo da contoso. Essa página contém informações de resumo úteis sobre os problemas de revisão encontrados no aplicativo do Azure.

    [![home page de solicitação pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*

1. Adicional No lado direito da janela, na seção **políticas**, clique na mensagem de problema (neste exemplo: falha na validação da **política**) para investigar os detalhes de baixo nível do problema, incluindo os arquivos de log associados. Os erros normalmente são exibidos na parte inferior dos arquivos de log.
1. No menu esquerdo da home page, selecione **Arquivos** para exibir os arquivos de lista que compõem os ativos técnicos dessa oferta. Os revisores da Microsoft deverão ter adicionado comentários, descrevendo os problemas críticos descobertos. No exemplo a seguir, dois problemas foram descobertos.

    [![home page de solicitação pull](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

1. Clique em cada nó de comentário na árvore esquerda para navegar até o comentário no contexto do código ao redor. Corrija seu código-fonte no projeto da equipe para corrigir o problema descrito pelo comentário.

>[!Note]
>Não é possível editar os ativos técnicos da oferta no ambiente do Azure DevOps da equipe de revisão. Para publicadores, esse é um ambiente somente leitura para o código-fonte contido. No entanto, você poderá deixar suas respostas aos comentários para o benefício da equipe de revisão da Microsoft.

   No exemplo a seguir, o publicador revisou, corrigiu e respondeu ao primeiro problema.

   ![Primeiro corrigir e comentar resposta](./media/first-comment-reply.png)

## <a name="next-steps"></a>Próximas etapas

Após corrigir os problemas críticos documentados no(s) relatório(s) de revisão, será necessário [republicar a oferta de aplicativo Azure](./create-new-azure-apps-offer.md#publish).
