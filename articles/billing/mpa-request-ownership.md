---
title: Obter a propriedade de cobrança das assinaturas do Azure para o MPA (Contrato de Parceiro da Microsoft)
description: Saiba como solicitar propriedade de cobrança de assinaturas do Azure de outros usuários.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: 312808517c80041cb45e2e8ac46566c1f59884e1
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376783"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Obter a propriedade de cobrança das assinaturas do Azure para sua conta do MPA

Para fornecer uma única fatura consolidada para os serviços gerenciados e o consumo do Azure, o CSP (Provedor de Soluções na Nuvem) pode assumir a propriedade de cobrança das assinaturas do Azure dos clientes dele com o EA (Contrato Enterprise).

Esse recurso só está disponível para Parceiros de Cobrança Direta do CSP certificados como [MSP Especialista do Azure](https://partner.microsoft.com/membership/azure-expert-msp). Ele está sujeito à governança e às políticas da Microsoft e pode exigir análise e aprovação para determinados clientes.

Para solicitar a propriedade de cobrança, é necessário ter a função **Administrador Global** ou **Agentes Administrativos**. Para saber mais, confira [Partner Center – Atribuir funções e permissões de usuários](https://docs.microsoft.com/partner-center/permissions-overview).

Este artigo se aplica às contas de cobrança para Contratos de Parceiro da Microsoft. Essas contas são criadas para CSPs (Provedores de Soluções na Nuvem) para gerenciar a cobrança dos clientes na nova experiência de comércio. A nova experiência só está disponível para parceiros que têm, pelo menos, um cliente que tenha aceitado um MCA (Contrato de Cliente da Microsoft) e tenha um plano do Azure. [Verifique se você tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Pré-requisitos

1. Estabelecer um [relacionamento de revendedor](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) com um cliente. Verificar a [Visão Geral da Autorização Regional do CSP](https://docs.microsoft.com/partner-center/regional-authorization-overview) para garantir que o cliente e o locatário do Parceiro estejam dentro das mesmas regiões autorizadas.  

2. [Confirmar a aceitação pelo cliente do Contrato de Cliente da Microsoft](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Configurar o [plano do Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) para o cliente. Se o cliente estiver fazendo a compra por meio de vários revendedores, você precisará configurar o plano do Azure para cada combinação de cliente e revendedor.

## <a name="request-billing-ownership"></a>Solicitar propriedade de cobrança

1. Entre no [portal do Azure](https://portal.azure.com) usando as credenciais de Agente Administrativo do CSP.

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/mpa-request-ownership/search-cmb.png)

3. Selecione **Clientes** do lado esquerdo e, em seguida, selecione um cliente na lista.
   
   ![Captura de tela que mostra a seleção de clientes](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Selecione **Solicitações de transferência** no lado inferior esquerdo e, em seguida, selecione **Adicionar uma nova solicitação**.
 
   ![Captura de tela que mostra a seleção de solicitações de transferência](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Insira o endereço de email do usuário na organização do cliente que aceitará a solicitação de transferência. O usuário precisa ser um proprietário da conta em um Contrato Enterprise. Selecione **Enviar solicitação de transferência**.

   ![Captura de tela que mostra o envio de uma solicitação de transferência](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. O usuário recebe um email com instruções para revisar sua solicitação de transferência.

   ![Captura de tela que mostra o email de análise da solicitação de transferência](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções.

    ![Captura de tela que mostra o email de análise da solicitação de transferência](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verifique o status da solicitação de transferência

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Selecione **Clientes** no lado esquerdo.

   ![Captura de tela que mostra a seleção de clientes](./media/mpa-request-ownership/mpa-select-customers.png)

4. Selecione o cliente na lista para a qual você enviou a solicitação de transferência.

5. Selecione **Solicitações de transferência** no lado inferior esquerdo. A página Solicitações de transferência exibe as seguintes informações:

    ![Captura de tela que mostra uma lista de solicitações de transferência](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Coluna|Definição|
   |---------|---------|
   |Data da solicitação|A data em que a solicitação de transferência foi enviada|
   |Destinatário|O endereço de email do usuário para quem você enviou a solicitação de transferência da propriedade de cobrança|
   |Data de validade|A data em que a solicitação expira|
   |Status|O status da solicitação de transferência|

    A solicitação de transferência pode ter um dos seguinte status:

   |Status|Definição|
   |---------|---------|
   |Em andamento|O usuário não aceitou a solicitação de transferência|
   |Processando|O usuário aprovou a solicitação de transferência. A cobrança das assinaturas que o usuário selecionou está sendo transferida para a sua conta|
   |Concluído| A cobrança das assinaturas selecionadas pelo usuário é transferida para a sua conta|
   |Concluído com erros|A solicitação foi concluída, mas a cobrança de algumas assinaturas que o usuário selecionou não pôde ser transferida|
   |Expirado|O usuário não aceitou a solicitação a tempo e ela expirou|
   |Cancelado|Alguém com acesso à solicitação de transferência cancelou a solicitação|
   |Recusado|O usuário recusou a solicitação de transferência|

6. Selecione uma solicitação de transferência para exibir detalhes. A página de detalhes da transferência exibe as seguintes informações: ![Captura de tela que mostra a lista de assinaturas transferidas](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Coluna  |Definição|
   |---------|---------|
   |ID da solicitação de transferência|A ID exclusiva de sua solicitação de transferência. Se você enviar uma solicitação de suporte, compartilhe a ID com o Suporte do Azure para agilizá-la|
   |Transferência solicitada em|A data em que a solicitação de transferência foi enviada|
   |Transferência solicitada por|O endereço de email do usuário que enviou a solicitação de transferência|
   |A solicitação de transferência expira em| A data em que a solicitação de transferência expira|
   |Endereço de email do destinatário|O endereço de email do usuário para quem você enviou a solicitação de transferência da propriedade de cobrança|
   |Link de transferência enviado para o destinatário|A URL que foi enviada para o usuário para examinar a solicitação de transferência|

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

É possível solicitar a propriedade de cobrança dos tipos de assinatura listados abaixo.

- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)\* 
- [Contrato Enterprise da Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* A assinatura do Enterprise DevTest será cobrada com base na taxa pagas conforme o uso após a transferência e nenhum desconto oferecido por meio da oferta do Enterprise DevTest pelo EA do cliente estará disponível para o parceiro CSP.

## <a name="additional-information"></a>Informações adicionais

A seção a seguir fornece mais informações sobre como transferir assinaturas.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure na assinatura continuam sendo executados sem nenhuma interrupção. Apenas fazemos a transição do relacionamento de cobrança das assinaturas do Azure que o usuário selecione para transferir.

### <a name="disabled-subscriptions"></a>Assinaturas desabilitadas

As assinaturas desabilitadas não podem ser transferidas. As assinaturas devem estar no estado ativo para transferir a propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos de assinaturas como VMs, discos e sites são transferidos.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Os produtos do Azure Marketplace que estão disponíveis para assinaturas gerenciadas por CSPs (Provedores de Soluções na Nuvem) são transferidos juntamente com as respectivas assinaturas. As assinaturas que têm produtos do Azure Marketplace que não estão habilitados para CSPs não podem ser transferidas.

### <a name="azure-reservations-transfer"></a>Transferência de Reservas do Azure

As Reservas do Azure não são movidas automaticamente com as assinaturas. [Entre em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as Reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

O acesso de usuários, grupos ou entidades de serviço existentes que foi atribuído usando o [controle de acesso baseado em função (RBAC) do Azure](../role-based-access-control/overview.md) não é afetado durante a transição. O parceiro não obterá nenhum novo acesso RBAC às assinaturas.  

Os parceiros devem trabalhar com o cliente para obter acesso às assinaturas.  Os parceiros precisam obter tíquetes de suporte aberto de acesso [Administrador em Nome de – AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) ou [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider).

### <a name="azure-support-plan"></a>Plano de Suporte do Azure

O Suporte do Azure não é transferido com as assinaturas. Se o usuário transferir todas as assinaturas do Azure, peça que ele cancele o plano de suporte. Após a transferência, o parceiro CSP é responsável pelo suporte. O cliente deve trabalhar com o parceiro CSP em caso de qualquer solicitação de suporte.  

### <a name="charges-for-transferred-subscription"></a>Encargos para assinatura transferida

O proprietário de cobrança original das assinaturas é responsável por encargos que foram relatados até o momento em que a transferência foi concluída. Você é responsável pelos encargos relatados a partir do momento da transferência. Pode haver alguns encargos cobrados antes da transferência, mas que foram relatados posteriormente. Esses encargos são exibidos em sua fatura.

### <a name="cancel-a-transfer-request"></a>Cancelar uma solicitação de transferência

É possível cancelar a solicitação de transferência até que a solicitação seja aprovada ou recusada. Para cancelar a solicitação de transferência, acesse a [página de detalhes de transferência](#check-the-transfer-request-status) e selecione cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Transferência SaaS (software como serviço)

Os produtos SaaS não são transferidos com as assinaturas. Peça ao usuário para [Entrar em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade de cobrança de produtos SaaS. Junto com a propriedade de cobrança, o usuário também pode transferir a propriedade do recurso. A propriedade do recurso permite que você realize operações de gerenciamento, como excluir e exibir os detalhes do produto. O usuário deve ser um proprietário de recurso no produto SaaS para transferir a propriedade do recurso.

### <a name="additional-approval-for-certain-customers"></a>Aprovação adicional para determinados clientes

Algumas das solicitações de transição do cliente podem exigir um processo de análise adicional com a Microsoft devido à natureza da estrutura do Registro Enterprise atual do cliente. O parceiro será notificado desses requisitos ao tentar enviar um convite aos clientes. Os parceiros são solicitados a trabalhar com o Gerente de Desenvolvimento do Parceiro e a equipe de contas do cliente para concluir esse processo de análise.

### <a name="azure-subscription-directory"></a>Diretório da assinatura do Azure 
O diretório das assinaturas do Azure que são transferidas precisa corresponder ao diretório do cliente que foi selecionado ao estabelecer o relacionamento do CSP. 

Se esses dois diretórios não forem correspondentes, as assinaturas não poderão ser transferidas. Você precisa estabelecer um novo relacionamento de revendedor CSP com o cliente selecionando o diretório das assinaturas do Azure ou alterando o diretório das assinaturas do Azure para que correspondam ao diretório de relacionamento entre o CSP e o cliente. Para obter mais informações, confira [Associar uma assinatura existente ao Azure AD Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- A propriedade de cobrança das assinaturas do Azure é transferida para você. Controle os encargos dessas assinaturas no [portal do Azure](https://portal.azure.com).

- Trabalhe com o cliente para obter acesso às assinaturas transferidas do Azure. [Gerencie o acesso aos recursos do Azure usando o RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
