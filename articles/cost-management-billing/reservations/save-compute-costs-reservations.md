---
title: O que são Reservas do Azure?
description: Saiba mais sobre as Reservas do Azure e sobre os preços para economizar nos custos de máquinas virtuais, de bancos de dados SQL, do Azure Cosmos DB e de outros recursos.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235643"
---
# <a name="what-are-azure-reservations"></a>O que são Reservas do Azure?

As reservas do Azure ajudam você a economizar dinheiro confirmando os planos de um ou de três anos para vários produtos. O compromisso permite que você obtenha um desconto nos recursos que usar. As reservas podem reduzir significativamente os custos de recursos em até 72% nos preços pagos conforme o uso. As reservas fornecem um desconto de cobrança e não afetam o estado de runtime dos recursos. Após você comprar uma reserva, o desconto se aplica automaticamente aos recursos correspondentes.

Você pode pagar por uma reserva de forma antecipada ou mensal. O custo total das reservas antecipadas e mensais é o mesmo e você não paga nenhuma taxa adicional quando opta por pagar mensalmente. O pagamento mensal está disponível para reservas do Azure, e não para produtos de terceiros.

Você pode comprar uma instância reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Por que comprar uma reserva?

Se você tiver um uso de recursos consistente que dá suporte a reservas, a compra de uma reserva lhe oferecerá a opção de reduzir os custos. Por exemplo, quando executa continuamente instâncias de um serviço sem a reserva, você é cobrado com base em taxas pagas conforme o uso. Quando compra uma reserva, você obtém imediatamente o desconto de reserva. Os recursos não serão mais cobrados com base nas taxas pagas conforme o uso.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Após a compra, o desconto de reserva se aplica automaticamente para o uso de recursos que correspondem aos atributos que você seleciona ao comprar a reserva. Os atributos incluem o SKU, as regiões (quando aplicável) e o escopo. O escopo de uma reserva seleciona o local a que a economia da reserva se aplica.

Para obter mais informações sobre como o desconto é aplicado, confira [Aplicação do desconto de instância reservada](reservation-discount-application.md).

Para obter mais informações sobre como funciona o escopo de reserva, confira [Reservas de escopo](prepare-buy-reservation.md#scope-reservations).


## <a name="flexibility-with-azure-reservations"></a>Flexibilidade com reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a atender às suas necessidades em constante evolução. Você pode trocar uma reserva por outra do mesmo tipo. Você também poderá pedir reembolso de uma reserva de até US$ 50.000 em uma janela de 12 meses se não precisar mais dela. O limite máximo do reembolso se aplica a todas as reservas no escopo do seu contrato com a Microsoft.

Para obter mais informações, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>Preços cobertos pela reserva

- **Instância de Máquina Virtual Reservada** – uma reserva apenas abrange os custos de computação de máquina virtual. Não cobre encargos adicionais de software, Windows, rede nem armazenamento.
- **Capacidade reservada de Armazenamento do Azure**: uma reserva abrange a capacidade de contas de armazenamento padrão para armazenamento de blobs ou do Azure Data Lake Storage Gen2. A reserva não abrange a largura de banda nem as taxas de transação.
- **Capacidade reservada do Azure Cosmos DB** – cobre a taxa de transferência provisionada para seus recursos. Ela não cobre encargos de armazenamento e rede.
- **vCore reservado do Banco de Dados SQL** – apenas os custos de computação são incluídos com uma reserva. A licença do SQL é cobrada separadamente.
- **SQL Data Warehouse** – uma reserva cobre o uso de cDWU. Ela não cobre encargos de armazenamento ou rede associados ao uso do SQL Data Warehouse.
- **Azure Databricks** – uma reserva abrange apenas o uso de DBU. Outros encargos, como computação, armazenamento e rede, são aplicados separadamente.
- **Imposto de selo do Serviço de Aplicativo** – uma reserva cobre o uso do imposto de selo. Ela não se aplica a funções de trabalho, de forma que todos os outros recursos associados ao selo são cobrados separadamente.
- **Banco de Dados do Azure para MySQL** – apenas os custos de computação são incluídos com uma reserva. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do servidor de Banco de Dados MySQL.
- **Banco de Dados do Azure para PostgreSQL** – apenas os custos de computação são incluídos com uma reserva. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância dos servidores de Banco de Dados PostgreSQL.
- **Banco de Dados do Azure para MariaDB** – apenas os custos de computação são incluídos com uma reserva. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do servidor de banco de dados MariaDB.
- **Azure Data Explorer** – uma reserva abrange os encargos de marcação. Uma reserva não abrange os encargos de computação, rede ou armazenamento associados aos clusters.
- **Cache do Azure para Redis**: apenas os custos de computação são incluídos com uma reserva. Uma reserva não abrange os preços de rede ou armazenamento associados às instâncias de Cache Redis.
- **Host Dedicado do Azure** – Somente os custos de computação estão incluídos no Host Dedicado.
- **Reservas do Armazenamento em Disco do Azure** – uma reserva cobre apenas SSDs Premium de tamanho P30 ou superior. Ela não cobre nenhum outro tipo de disco ou tamanhos menores que P30.

Planos de software:

- **SUSE Linux** – uma reserva abrange os custos do plano de software. Os descontos aplicam-se apenas aos medidores do SUSE e não ao uso da máquina virtual.
- **Planos do Red Hat** – uma reserva abrange os custos do plano de software. Os descontos aplicam-se apenas aos medidores Red Hat e não ao uso da máquina virtual.
- **Solução VMware da CloudSimple no Azure** – uma reserva abrange os nós VMWare da CloudSimple. Os custos de software adicionais ainda se aplicam.
- **Red Hat OpenShift no Azure** – uma reserva se aplica aos custos do OpenShift, mas não aos custos de infraestrutura do Azure.

Para máquinas virtuais do Windows e o Banco de Dados SQL, o desconto de reserva não se aplica aos custos de software. Você pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as Reservas do Azure com os seguintes artigos:
    - [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
    - [Noções básicas sobre o uso de reserva para sua assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)

- Saiba mais sobre reservas para planos de serviço:
    - [Máquinas virtuais com Instâncias de VM Reservadas do Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Recursos de computação de Banco de Dados SQL do Azure com capacidade reservada](../../sql-database/sql-database-reserved-capacity.md) Saiba mais sobre reservas para planos de software:
    - [Planos de software Red Hat das Reservas do Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Planos de software SUSE das Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
