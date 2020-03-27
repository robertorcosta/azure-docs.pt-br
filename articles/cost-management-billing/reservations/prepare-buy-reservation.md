---
title: Preparar a compra de uma reserva do Azure
description: Saiba mais sobre pontos importantes antes de comprar uma reserva do Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235663"
---
# <a name="prepare-to-buy-a-reservation"></a>Preparar a compra de uma reserva

As reservas do Azure ajudam você a economizar dinheiro confirmando os planos de um ou de três anos para muitos recursos do Azure. Antes de inserir um compromisso para comprar uma reserva, é preciso que você examine as seções a seguir para se preparar para sua compra.

## <a name="who-can-buy-a-reservation"></a>Quem pode comprar uma reserva

Para comprar um plano, você deve ter uma função de proprietário de assinatura em uma assinatura Enterprise (MS-AZR-0017P ou MS-AZR-0148P) ou de Pagamento Conforme o Uso (MS-AZR-0003P ou MS-AZR-0023P) ou de Contrato de Cliente da Microsoft. Os provedores de solução na nuvem podem usar o portal do Azure ou o  [Partner Center](/partner-center/azure-reservations)  para comprar Reservas do Azure.

Clientes com EA (Contrato Enterprise) podem limitar as compras aos administradores de EA desabilitando a opção **Adicionar Instâncias Reservadas** no Portal do EA. Os administradores de EA precisam ser proprietários de pelo menos uma assinatura de EA para comprar uma reserva. A opção é útil para empresas que desejam que uma equipe centralizada compre reservas para diferentes centros de custo. Após a compra, as equipes centralizadas podem adicionar proprietários de centro de custo às reservas. Os proprietários podem, então, definir o escopo da reserva para suas assinaturas. A equipe central não precisa ter acesso de proprietário da assinatura no local em que a reserva é comprada.

Um desconto de reserva aplica-se somente aos recursos associados a assinaturas adquiridas por meio do Enterprise, do CSP (Provedor de Soluções de Nuvem), Contrato de Cliente da Microsoft e planos individuais com tarifas pagas conforme o uso.

## <a name="scope-reservations"></a>Escopo das reservas

Você pode definir o escopo de uma reserva como uma assinatura ou como grupos de recursos. Definir o escopo de uma reserva seleciona o local a que a economia da reserva se aplica. Quando você define o escopo da reserva como um grupo de recursos, os descontos da reserva aplicam-se somente ao grupo de recursos, não à assinatura inteira.

### <a name="reservation-scoping-options"></a>Opções de escopo de reserva

Você tem três opções para definir o escopo de uma reserva, dependendo de suas necessidades:

- **Escopo de grupo de recursos único**: aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
- **Escopo de assinatura única**: aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.
- **Escopo compartilhado**: aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Contrato Enterprise, o contexto de cobrança é o registro. Para clientes do Contrato de Cliente da Microsoft, o escopo do orçamento é o perfil de cobrança. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.

Ao aplicar descontos de reserva ao seu uso, o Azure processa a reserva na seguinte ordem:

1. Reservas que estão no escopo de um grupo de recursos
2. Reservas de escopo único
3. Reservas de escopo compartilhado

Um único grupo de recursos pode obter descontos de reserva provenientes de várias reservas, dependendo de como você define o escopo de suas reservas.

Você sempre pode atualizar o escopo depois de comprar uma reserva. Para fazer isso, vá para a reserva, clique em **Configuração** e redefina o escopo da reserva. A redefinição do escopo de uma reserva não é uma transação comercial. O termo da reserva não é alterado. Para saber mais sobre como atualizar o escopo, confira [Atualizar o escopo depois de comprar uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo mostrando uma alteração de escopo de reserva](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Comprar reservas

Você pode comprar reservas por meio do portal do Azure, das APIs, do PowerShell e da CLI. Leia os seguintes artigos, que se aplicam a você quando você está pronto para fazer uma compra de reserva:

- [Serviço de Aplicativo](prepay-app-service-isolated-stamp.md)
- [Cache Redis do Azure](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Armazenamento em Disco](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Host Dedicado](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Planos de software](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Banco de Dados SQL](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Máquinas virtuais](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar reservas para recursos do Azure](manage-reserved-vm-instance.md)
