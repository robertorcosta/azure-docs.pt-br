---
title: Crie uma oferta de máquina virtual no Azure Marketplace.
description: Saiba como criar uma oferta de máquina virtual no Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 5964292d7037465456efe22283a5552a2bfc7ad8
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331253"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Como criar uma oferta de máquina virtual no Azure Marketplace

Este artigo descreve como criar uma oferta de máquina virtual do Azure para o [Azure Marketplace](https://azuremarketplace.microsoft.com/). Ele aborda máquinas virtuais que contêm um sistema operacional e um VHD (disco rígido virtual), tanto baseadas em Windows quanto em Linux, além de até 16 discos de dados.

Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](partner-center-portal/create-account.md). Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver feito isso, examine [planejar uma oferta de máquina virtual](marketplace-virtual-machines.md). Ele explicará os requisitos técnicos para sua máquina virtual e listará as informações e os ativos que você precisará ao criar sua oferta. 

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página **Visão geral**, selecione **Nova oferta** > **Máquina Virtual do Azure**.

    ![Captura de tela mostrando as opções de menu do painel esquerdo e o botão "Nova oferta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Depois que sua oferta for publicada, as edições feitas nela no Partner Center serão exibidas no Azure Marketplace somente depois que você republicar a oferta. Sempre republique uma oferta depois de fazer alterações nela.

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço web para a oferta do Azure Marketplace e no Azure PowerShell e na CLI do Azure, se aplicável.
- Use apenas letras minúsculas e números. A ID pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. O alias da oferta é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Azure Marketplace. Ele é diferente do nome da oferta e de outros valores que são mostrados aos clientes.

## <a name="enable-a-test-drive-optional"></a>Habilitar um test drive (opcional)

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, concedendo a eles acesso a um ambiente pré-configurado por um número fixo de horas. A oferta de um test drive resulta em uma taxa de conversão maior e gera clientes potenciais altamente qualificados. Para saber mais sobre as unidades de teste, consulte [o que é um test drive?](partner-center-portal/test-drive.md).

> [!TIP]
> Uma test drive é diferente de uma avaliação gratuita. Você pode oferecer um test drive, uma avaliação gratuita ou ambos. Ambos fornecem aos clientes sua solução por um período de tempo fixo. No entanto, um test drive também inclui um tour interativo prático dos principais recursos do produto e dos benefícios demonstrados em um cenário de implementação do mundo real.

**Para habilitar um test drive**
1.  Em **testar unidade**, marque a caixa de seleção **habilitar um test drive** .
1.  Selecione o tipo de test drive na lista que aparece.

## <a name="configure-lead-management"></a>Configurar o gerenciamento de clientes potenciais

Quando você estiver publicando sua oferta no marketplace comercial com o Partner Center, conecte-o ao seu sistema de CRM (gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse em seu produto ou usá-lo. Se você quiser habilitar uma test drive (consulte a seção anterior), será necessário conectar-se a um CRM. Caso contrário, a conexão a um CRM será opcional.

1. em **clientes potenciais**, selecione o link **conectar** .
1. Na caixa de diálogo **detalhes da conexão** , selecione um destino de Lead na lista.
1. Preencha os campos que aparecem. Para obter etapas detalhadas, consulte os seguintes artigos:

   - [Configurar a oferta para enviar clientes potenciais à tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configure sua oferta para enviar clientes potenciais ao engajamento do cliente do dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
   - [Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configuração da oferta para enviar clientes potenciais para o Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurar oferta para enviar clientes potenciais para o Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração que você forneceu, selecione o link **validar** .
1. Para fechar a caixa de diálogo, selecione **OK**.

## <a name="resell-through-csps"></a>Revenda por meio de CSPs

Expanda o alcance de sua oferta, disponibilizando-a para parceiros no programa de [CSP (provedor de soluções na nuvem)](https://azure.microsoft.com/offers/ms-azr-0145p/). Todos os planos BYOL (traga sua própria licença) são automaticamente aceitos para o programa. Você também pode optar por aceitar seus planos não BYOL.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="next-steps"></a>Próximas etapas

- [Configurar propriedades de oferta de máquina virtual](azure-vm-create-properties.md)
- [Práticas recomendadas de listagem de ofertas](gtm-offer-listing-best-practices.md)