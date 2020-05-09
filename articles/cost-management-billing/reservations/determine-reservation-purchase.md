---
title: Determinar qual reserva do Azure você deve comprar
description: Este artigo ajuda você a determinar qual reserva você deve comprar.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: feee7475dcadc6d06693d9e60020097f8dc9149c
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628598"
---
# <a name="determine-what-reservation-to-purchase"></a>Determinar qual reserva comprar

Todas as reservas, exceto as do Azure Databricks, são aplicadas a cada hora. Você deve comprar reservas segundo o uso de base consistente. Há várias maneiras de determinar o que comprar e este artigo ajuda a determinar qual reserva você deve comprar.

Comprar mais capacidade do que o seu uso histórico resulta em uma reserva subutilizada. Você deve evitar a subutilização sempre que possível. A capacidade reservada não utilizada não é transferida de uma hora para outra. O uso que excede a quantidade reservada é cobrado usando taxas pagas conforme o uso mais caras.

## <a name="analyze-usage-data"></a>Analisar os dados de uso

Use as seções a seguir para ajudar a analisar seus dados de uso diário para determinar o uso da linha de base e qual reserva comprar.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analisar o uso para uma compra de instância reservada de VM

Identifique o tamanho de VM certo para a compra. Por exemplo, uma reserva adquirida para VMs da série ES não se aplica às VMs da série E e vice-versa.

As VMs da série promocional não obtêm um desconto de reserva; portanto, remova-as da análise.

Para restringir o escopo para o uso de VM qualificado, aplique os seguintes filtros nos dados de uso:

- Filtre **MeterCategory** para **Máquinas Virtuais**.
- Obtenha informações de **ServiceType** de **AdditionalInfo**. As informações sugerem o tamanho correto da VM. Por exemplo, E32 Standard.
- Use o campo **ResourceLocation** para determinar o data center de uso.

Ignore os recursos que têm menos de 24 horas de uso em um dia.

Se você quiser analisar no nível da família de tamanho da instância, poderá obter os valores de flexibilidade de tamanho da instância de [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Combine os valores com os dados para fazer a análise. Para obter mais informações sobre a flexibilidade de tamanho da instância, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Analisar o uso de uma compra de instância reservada do Azure Synapse Analytics

A capacidade reservada aplica-se ao preço do DWU do Azure Synapse Analytics. Ela não se aplica ao custo de licença do Azure Synapse Analytics nem a qualquer custo que não seja computação.

Para restringir o uso qualificado, aplique os seguintes filtros nos dados de uso:


- Filtre **MeterCategory** para **Banco de Dados SQL**.
- Filtre **MeterName** para **vCore**.
- Filtre **MeterSubCategory** para todos os registros de uso que têm _Computação_ no nome.

Em **AdditionalInfo**, obtenha o valor **vCores**. Ele informa quantos vCores foram usados. A quantidade é igual ao número de **vCores** multiplicado pelo número de horas pelo qual o banco de dados foi usado.

Os dados informam você sobre o uso consistente para:

- Combinação de tipo de banco de dados. Por exemplo, instância gerenciada ou pool elástico por banco de dados individual.
- Camada de serviço. Por exemplo, uso geral ou crítico para os negócios.
- Geração. Por exemplo, Gen 5.
- Local do recurso

### <a name="analysis-for-azure-synapse-analytics"></a>Análise para o Azure Synapse Analytics

A capacidade reservada aplica-se ao uso do DWU do Azure Synapse Analytics e é adquirida em incrementos em 100 DWU. Para restringir o uso qualificado, aplique os seguintes filtros nos dados de uso:

- Filtre **MeterName** para **100 DWUs**.
- Filtre **Subcategoria do medidor** para **Computação otimizada Gen2**.

Use o campo **Localização do Recurso** para determinar o uso do Azure Synapse Analytics em uma região.

O uso do Azure Synapse Analytics pode aumentar ou diminuir ao longo do dia. Converse com a equipe que gerenciou a instância do Azure Synapse Analytics para saber mais sobre o uso de base.

Acesse Reservas no portal do Azure e compre capacidade reservada do Azure Synapse Analytics em múltiplos de 100 DWUs.

## <a name="reservation-purchase-recommendations"></a>Recomendações de compra de reserva

As recomendações de compra de reserva são calculadas analisando seus dados de uso por hora nos últimos 7, 30 e 60 dias. O Azure calcula quais teriam sido os seus custos se você tivesse uma reserva e comparam-nos com seus custos reais de pagamento conforme o uso incorridos durante o tempo de duração. O cálculo é realizado para cada quantidade que você usou durante o período. A quantidade que maximiza a sua economia é recomendada.

Por exemplo, você pode usar 500 VMs na maior parte do tempo, mas às vezes o uso chega a um pico de 700 VMs. Neste exemplo, o Azure calcula sua economia para as quantidades de 500 e 700 VMs. Como o uso de 700 VMs é esporádico, o cálculo de recomendação determina que a economia é maximizada para uma compra de reserva de 500 VMs e a recomendação é fornecida para a quantidade 500.

Observe os seguintes pontos:

- As recomendações de reserva são calculadas usando as tarifas de uso sob demanda que se aplicam a você.
- As recomendações são calculadas para tamanhos individuais, não para a família de tamanho de instância.
- A quantidade recomendada para um escopo é reduzida no mesmo dia em que você adquire reservas para o escopo.
    - No entanto, uma atualização para a recomendação de quantidade de reserva entre escopos pode levar até 25 dias. Por exemplo, se você comprar com base em recomendações de escopo compartilhado, as recomendações de escopo de assinatura única poderão levar até 25 dias para serem ajustadas.

## <a name="recommendations-in-the-azure-portal"></a>Recomendações no portal do Azure

As compras de reserva calculadas pelo mecanismo de recomendações são mostradas na guia **Recomendado** no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Aqui está uma imagem de exemplo.

![Imagem mostrando recomendações](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Recomendações no aplicativo Power BI de Gerenciamento de Custos

Clientes do Contrato Enterprise e do Contrato de Cliente da Microsoft podem usar os relatórios de cobertura de RI da VM para VMs e recomendações de compra. Os relatórios de cobertura mostram o uso total e o uso que é coberto por instâncias reservadas.

1. Obtenha o [aplicativo de Gerenciamento de Custos](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Vá para o relatório de cobertura de RI da VM – Escopo único ou compartilhado, dependendo de qual escopo você deseja comprar.
3. Selecione a região, a família de tamanho da instância para ver o uso, a cobertura de RI e a recomendação de compra para o filtro selecionado.

## <a name="recommendations-in-azure-advisor"></a>Recomendações no Assistente do Azure

As recomendações de compra de reserva estão disponíveis no [Assistente do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- O Assistente tem apenas recomendações de escopo de assinatura única.
- As recomendações do Assistente são calculadas usando o período dos 30 dias anteriores. As economias projetadas são para um termo de reserva de três anos.
- Se você comprar uma reserva com escopo compartilhado, as recomendações de compra de reserva do Assistente poderão levar 30 dias para desaparecer.

## <a name="recommendations-using-apis"></a>Recomendações usando APIs

Use a API REST de [Recomendações de Reserva](/rest/api/consumption/reservationrecommendations/list) para exibir recomendações de maneira programática.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
- [Noções básicas sobre o uso de reserva para sua assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)