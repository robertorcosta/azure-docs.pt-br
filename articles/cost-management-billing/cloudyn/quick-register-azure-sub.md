---
title: Registrar a assinatura do Azure no Cloudyn
description: Saiba como registrar a sua assinatura do Azure com o Cloudyn e entre no portal do Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: bb7add199186ab54eb28131070e717e4298bedf1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985554"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrar uma assinatura individual do Azure e exibir dados de custo

Você pode usar sua assinatura do Azure para registrar com Cloudyn. O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn. Também mostra como começar a exibir dados de custo imediatamente.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Registrar com Cloudyn

1. No portal do Azure, clique em **Gerenciamento de Custo + Cobrança** na lista de serviços.
2. Em **Visão geral**, clique em **Cloudyn**  
    ![Página do Cloudyn mostrada no portal do Azure](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Sobre o **gerenciamento de custos** , clique em **ir para o Cloudyn** para abrir a página de registro do Cloudyn em uma nova janela.
4. Na página de registro de avaliação do portal do Cloudyn, digite o nome de sua empresa e, em seguida, selecione **Proprietário da Assinatura Individual do Azure** e, em seguida, clique em **Avançar**. O nome da conta e a ID de Locatário são automaticamente adicionados ao formulário.  
    ![Página de registro de avaliação em que você insere suas informações de registro](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Selecione a **ID da Oferta – Nome** associada à sua assinatura. Caso não tenha certeza de qual é sua ID de Taxa da assinatura, exiba sua fatura do Azure e procure **ID da Oferta**.
6. Concorde com os Termos de Uso e valide as informações e, em seguida, clique em **Avançar**.
7. Na página **Coletar dados adicionais**, clique em **Avançar** para autorizar o Cloudyn a coletar dados do recurso do Azure. Os dados coletados incluem dados de uso, desempenho, cobrança e marcação de suas assinaturas.  
    ![Coletar a página de dados adicionais em que você autoriza o Cloudyn](./media/quick-register-azure-sub/gather-additional.png)
8. O navegador levará você à página de entrada do Cloudyn. Entre com suas credenciais de assinatura do Azure.
9. Clique em **Ir para o Cloudyn** para abrir o portal de Cloudyn e, em seguida, na página **Gerenciamento de Contas**, você deverá ver as informações de sua conta de assinatura do Azure.  
    ![Página Gerenciamento de Contas mostrando informações de assinatura do Azure](./media/quick-register-azure-sub/accounts-mgt.png)

Para assistir a um vídeo de tutorial sobre como registrar sua assinatura do Azure, consulte [localizar o GUID do diretório e a ID de taxa para uso no Cloudyn](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou suas informações de inscrição do Azure para registrar-se na Cloudyn. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre a Cloudyn, continue o tutorial para o Cloudyn.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](tutorial-review-usage.md)
