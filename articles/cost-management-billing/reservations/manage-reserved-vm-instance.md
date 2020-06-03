---
title: Gerenciar reservas do Azure
description: Saiba como é possível gerenciar as Reservas do Azure.
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: banders
ms.openlocfilehash: 1084e8687cc709b5e31a9ef2c903638dd3d8f62c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84019989"
---
# <a name="manage-reservations-for-azure-resources"></a>Gerenciar Reservas para recursos do Azure

Após comprar uma reserva do Azure, talvez seja necessário aplicar a reserva a uma assinatura diferente, alterar quem pode gerenciar a reserva ou alterar o escopo da reserva. Também é possível dividir uma reserva em duas reservas para aplicar algumas das instâncias que você comprou para outra assinatura.

Se você tiver comprado Instâncias de Máquinas Virtuais Reservadas do Azure, poderá alterar a configuração de otimização da reserva. O desconto de reserva pode ser aplicado a VMs na mesma série ou você pode reservar a capacidade do data center para um tamanho de VM específico. Além disso, você deve tentar otimizar reservas para que elas sejam totalmente usadas.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Pedido de Reserva e Reserva

Quando você compra uma reserva, dois objetos são criados: **Pedido de Reserva** e **Reserva**.

No momento da compra, um Pedido de Reserva tinha uma Reserva nele. Ações como dividir, mesclar, reembolsar parcialmente ou trocar criam novas reservas no **Pedido de Reserva**.

Para exibir um Pedido de Reserva, acesse **Reservas** > selecione a reserva e, em seguida, clique na **ID do pedido de reserva**.

![Exemplo dos detalhes do pedido de reserva mostrando a ID do pedido de reserva ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Uma reserva herda permissões de seu pedido de reserva.

## <a name="change-the-reservation-scope"></a>Alterar o escopo da reserva

 Seu desconto de reserva se aplica a máquinas virtuais, bancos de dados SQL, Azure Cosmos DB ou outros recursos que correspondem à sua reserva e são executados no escopo da reserva. O contexto de cobrança depende da assinatura usada para comprar a reserva.

Para atualizar o escopo de uma reserva:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo.

Se você alterar de escopo compartilhado para único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente as assinaturas no mesmo contexto de cobrança que a reserva podem ser selecionadas.

O escopo somente se aplica a assinaturas individuais com taxas pagas conforme o uso (ofertas MS-AZR-0003P ou MS-AZR-0023P), à oferta Enterprise MS-AZR-0017P ou MS-AZR-0148P ou a tipos de assinatura CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os usuários que podem gerenciar uma reserva

Você pode delegar o gerenciamento de uma reserva adicionando pessoas às funções na reserva. Por padrão, a pessoa que fez a reserva e o administrador da conta têm a função de Proprietário de reserva.

É possível gerenciar o acesso a pedidos de reservas e reservas independentemente das assinaturas que obtêm o desconto de reserva. Ao conceder a alguém permissões para gerenciar um pedido de reserva ou a reserva, a pessoa não recebe permissão para gerenciar a assinatura. De maneira semelhante, se você conceder a alguém permissões para gerenciar uma assinatura no escopo da reserva, a pessoa não receberá direitos de gerenciar o pedido da reserva ou a reserva.

Para realizar uma troca ou reembolso, o usuário deve ter acesso ao pedido de reserva. Ao conceder permissões a alguém, é melhor conceder permissões ao pedido de reserva, não à reserva.


Para delegar o gerenciamento de acesso de uma reserva:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
3. Selecione a reserva que deseja delegar acesso a outros usuários.
4. Selecione **IAM (Controle de acesso)** .
5. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Ou, se você quiser conceder acesso limitado, selecione uma função diferente.
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário.
7. Selecione o usuário e, em seguida, selecione **Salvar**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma única reserva em duas reservas

 Depois de comprar mais de uma instância de recurso em uma reserva, é recomendável atribuir instâncias nessa reserva a assinaturas diferentes. Por padrão, todas as instâncias têm um escopo - assinatura única ou compartilhada. Por exemplo, você comprou 10 instâncias de reserva e especificou o escopo para ser a assinatura A. Agora convém alterar o escopo de sete reservas para a assinatura A e as três restantes para a assinatura B. A divisão de uma reserva permite distribuir instâncias para gerenciamento de escopo granular. Você pode simplificar a alocação para as assinaturas escolhendo o escopo compartilhado. Contudo, para fins de gerenciamento ou de orçamento, você pode alocar quantidades a assinaturas específicas.

 Você pode dividir uma reserva em duas reservas por meio do PowerShell, CLI ou da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva usando o PowerShell

1. Obtenha a ID do pedido de reserva executando o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Obtenha os detalhes de uma reserva:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divida a reserva em duas e distribua as instâncias:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Alterar a configuração de otimização para Instâncias de VM Reservadas

 Ao comprar uma Instância de VM Reservada, você escolhe a flexibilidade do tamanho da instância ou a prioridade da capacidade. A flexibilidade do tamanho da instância aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](https://aka.ms/RIVMGroups). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. Essa opção oferece mais confiança na capacidade de iniciar as instâncias de VM quando forem necessárias.

Por padrão, quando o escopo da reserva é compartilhado, a flexibilidade do tamanho da instância é ativada. A capacidade do data center não é priorizada para implantações de VM.

Para reservas em que o escopo é único, você pode otimizar a reserva para prioridade de capacidade em vez de flexibilidade de tamanho de instância de VM.

Para atualizar a configuração de otimização da reserva:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.  
  ![Exemplo mostrando o item de Configuração](./media/manage-reserved-vm-instance/add-product03.png)
5. Altere a configuração **Otimizar para**.  
  ![Exemplo mostrando a configuração Otimizar para](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Otimizar o uso da reserva

A economia da reserva do Azure são provenientes do uso do recurso sustentado. Ao realizar uma compra de reserva, você paga por um uso de recurso 100% possível durante um prazo de um ou três anos. Tente maximizar sua reserva para ter o máximo de uso e de economia possível. As seguintes seções explicam como monitorar uma reserva e otimizar seu uso.

### <a name="view-reservation-use-in-the-azure-portal"></a>Exibir uso de reserva no portal do Azure

Uma maneira de exibir o uso de reserva é no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > [**Reservas**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e observe a **Utilização (%)** de uma reserva.  
  ![Imagem mostrando a lista de reservas](./media/manage-reserved-vm-instance/reservation-list.png)
3. Selecione uma reserva.
4. Examine a tendência de uso da reserva com o tempo.  
  ![Imagem mostrando o uso da reserva ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Exibir uso da reserva com API

Se você for um cliente EA (Contrato Enterprise), poderá exibir programaticamente como as reservas em sua organização estão sendo usadas. Você obtém a reserva não utilizada por meio de dados de uso. Quando você examina os encargos da reserva, tenha em mente que os dados são divididos entre custo real e custos amortizados. O custo real fornece dados para reconciliar sua fatura mensal. Ele também tem o custo de compra da reserva e os detalhes do aplicativo de reserva. O custo amortizado é como o custo real, exceto que o preço efetivo do uso da reserva é proporcional. As horas de reserva não utilizadas são mostradas em dados de custo amortizado. Para obter mais informações sobre dados de uso para clientes EA, confira [Obter uso e custos de reserva do Contrato Enterprise](understand-reserved-instance-usage-ea.md).

Para outros tipos de assinatura, use a API [Resumos de Reservas – Listar por Pedido de Reserva e Reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Otimize sua reserva

Se você achar que as reservas da sua organização estão sendo subutilizadas:

- Verifique se as máquinas virtuais que sua organização cria correspondem ao tamanho da VM que está na reserva.
- Verifique se a flexibilidade de tamanho da instância está ligada. Para obter mais informações, confira [Gerenciar reservas – alterar configuração de otimização para Instâncias de VM Reservadas](#change-optimize-setting-for-reserved-vm-instances).
- Altere o escopo da reserva para _compartilhado_ de modo que ele se aplique mais amplamente. Para obter mais informações, confira [Alterar o escopo de uma reserva](#change-the-reservation-scope).
- Considere trocar a quantidade não utilizada. Para obter mais informações, confira [Cancelamentos e trocas](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](save-compute-costs-reservations.md)

Comprar um plano de serviço:
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Comprar um plano de software:
- [Pagar antecipadamente por planos de software Red Hat das reservas do Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pré-pagamento para planos de software SUSE das reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Noções básicas sobre desconto e uso:
- [Entender como o desconto de reserva de VM é aplicado](../manage/understand-vm-reservation-charges.md)
- [Noções básicas sobre como o desconto do plano de software do Red Hat Enterprise Linux é aplicado](understand-rhel-reservation-charges.md)
- [Entender como o desconto do plano de software do SUSE Linux Enterprise é aplicado](understand-suse-reservation-charges.md)
- [Entender como outros descontos de reserva são aplicados](understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
