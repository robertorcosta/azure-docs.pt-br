---
title: Manipulação feedback de revisão para Azure Apps oferta no Mercado Comercial
description: Como lidar com o feedback de revisão para o Azure Apps oferece para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) usando o portal Commercial Marketplace no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279786"
---
# <a name="handling-review-feedback"></a>Tratar comentários de revisão

Este artigo explica como acessar o ambiente do Azure DevOps utilizado pela equipe de revisão do Microsoft Azure Marketplace. Se problemas críticos forem localizados na oferta de aplicativo Azure durante a etapa de **revisão da Microsoft**, você poderá entrar nesse sistema para exibir informações detalhadas sobre esses problemas (revisar comentários). Após corrigir todos esses problemas, será necessário reenviar a oferta para continuar a publicá-la no Azure Marketplace. O diagrama a seguir ilustra como esse processo de comentários se relaciona com o processo de publicação.

![Revisar o processo de feedback](./media/review-feedback-process.png)

Normalmente, problemas de revisão são referenciados como uma PR (solicitação de pull). Cada PR é vinculada a um item online do [Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente nomeado VSTS (Visual Studio Team Services)), que contém detalhes sobre o problema. A imagem a seguir exibe um exemplo da experiência do Partner Center se os problemas forem encontrados durante as revisões. 

![Status de publicação](./media/publishing-status.png)

O RP que contenha detalhes específicos sobre o envio será mencionado no link "Exibir Relatório de Certificação". Para situações complexas, as equipes de suporte e revisão também poderão enviar-lhe um email.

## <a name="azure-devops-access"></a>Acesso a Azure DevOps

Todos os usuários com acesso à função "desenvolvedor" no Partner Center terão acesso para visualizar os itens de RP referenciados no feedback de revisão.

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

1. Na seção de revisão da **Microsoft** do formulário de etapas de publicação, clique em um link de RP para iniciar seu navegador e navegue até a página **Visão Geral** (inicial) para este RP. A imagem a seguir mostra um exemplo da página inicial do problema crítico para a oferta do aplicativo de exemplo Contoso. Essa página contém informações de resumo úteis sobre os problemas de revisão encontrados no aplicativo do Azure.

    [![Página inicial do pedido de puxar](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*

1. (Opcional) No lado direito da janela, na seção **Políticas,** clique na mensagem de problema (neste exemplo: Falha na **validação de políticas)** para investigar os detalhes de baixo nível do problema, incluindo os arquivos de log associados. Os erros normalmente são exibidos na parte inferior dos arquivos de log.
1. No menu esquerdo da home page, selecione **Arquivos** para exibir os arquivos de lista que compõem os ativos técnicos dessa oferta. Os revisores da Microsoft deverão ter adicionado comentários, descrevendo os problemas críticos descobertos. No exemplo a seguir, dois problemas foram descobertos.

    [![Página inicial do pedido de puxar](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

1. Clique em cada nó de comentário na árvore esquerda para navegar até o comentário no contexto do código ao redor. Corrija seu código-fonte no projeto da equipe para corrigir o problema descrito pelo comentário.

>[!Note]
>Não é possível editar os ativos técnicos da oferta no ambiente do Azure DevOps da equipe de revisão. Para publicadores, esse é um ambiente somente leitura para o código-fonte contido. No entanto, você poderá deixar suas respostas aos comentários para o benefício da equipe de revisão da Microsoft.

   No exemplo a seguir, o publicador revisou, corrigiu e respondeu ao primeiro problema.

   ![Primeiro corrigir e comentar resposta](./media/first-comment-reply.png)

## <a name="next-steps"></a>Próximas etapas

Após corrigir os problemas críticos documentados no(s) relatório(s) de revisão, será necessário [republicar a oferta de aplicativo Azure](./create-new-azure-apps-offer.md#publish).
