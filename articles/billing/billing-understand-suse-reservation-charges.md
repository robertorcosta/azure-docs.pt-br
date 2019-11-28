---
title: Desconto do plano de software – Azure | Microsoft Docs
description: Saiba como os descontos de plano de software são aplicados ao software em máquinas virtuais.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 76fc2d782571818d14a3008c10a0c3fae07a2b7d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225735"
---
# <a name="azure-software-plan-discount"></a>Desconto do plano de software do Azure

Os planos de software do Azure para SUSE e Red Hat são reservas que se aplicam às VMs implantadas. O desconto do plano de software é aplicado ao uso de software das VMs implantadas que correspondem à reserva.

Quando você desliga uma VM, o desconto é aplicado automaticamente a outra VM correspondente, se disponível. Um plano de software cobre o custo da execução do software em uma VM. Outros encargos, como computação, armazenamento e rede, são cobrados separadamente.

Para comprar o plano certo, você precisa entender o uso da VM e o número de vCPUs nessas VMs. Use as seções a seguir para ajudar a identificar o plano a ser comprado, com base nos dados de uso.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Examinar o uso da VM do Red Hat antes da compra

Obtenha o nome do produto de seus dados de uso e compre o plano do Red Hat com o mesmo tipo e tamanho.

Por exemplo, se o seu uso tiver o produto **Red Hat Enterprise Linux – Licença de VM com 1 a 4 vCPU**, você deverá comprar o **Red Hat Enterprise Linux** para a **VM com 1 a 4 vCPU**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Examinar o uso da VM do SUSE antes da compra

Obtenha o nome do produto de seus dados de uso e compre o plano do SUSE com o mesmo tipo e tamanho.

Por exemplo, se o seu uso for para o produto **SUSE Linux Enterprise Server Priority – Suporte de VM com 2 a 4 vCPU**, você deverá comprar o **SUSE Linux Enterprise Server Priority** para **2 a 4 vCPU**.

![Exemplo de seleção do produto a ser comprado](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>O desconto se aplica a diferentes tamanhos de VM para planos do SUSE

Como as instâncias de VMs reservadas, as compras do plano do SUSE oferecem flexibilidade no tamanho da instância. Isso significa que seu desconto se aplica mesmo quando você implanta uma VM com uma contagem diferente de vCPU. O desconto se aplica a diferentes tamanhos de VM dentro do plano de software.

O desconto se aplica a diferentes tamanhos de VM dentro do plano de software. A proporção compara a área de cobertura relativa de cada medidor nesse grupo. A proporção depende das vCPUs da VM. Use o valor da razão para calcular quantas instâncias de VM obtêm o desconto do plano do SUSE Linux.

Por exemplo, se você comprar um plano para o SUSE Linux Enterprise Server para HPC Priority para uma VM com 3 ou 4 vCPUs, a proporção dessa reserva será 2. O desconto cobre o custo do software SUSE para:

- 2 VMs implantadas com 1 ou 2 vCPUs,
- 1 VM implantada com 3 ou 4 vCPUs,
- ou 0,77 ou cerca de 77% de uma VM com 5 ou mais vCPUs.

A taxa de 5 ou mais vCPUs é 2.6. Portanto, uma reserva para o SUSE com uma VM com 5 ou mais vCPUs cobre apenas uma parte do custo do software, que é de cerca de 77%.

As tabelas a seguir mostram os planos de software para os quais você pode comprar uma reserva, os medidores de uso associados e as taxas de cada uma.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server para HPC Priority

Nome do marketplace do portal do Azure:

- SLES 12 SP3 para HPC (prioridade)

|VM SUSE | MeterId| Proporção| Tamanho de VM de exemplo|
| -------| ------------------------| --- |--- |
|SLES para HPC 1 ou 2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES para HPC 3-4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES para HPC 5+ vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Nome do marketplace do portal do Azure:

- SLES 12 SP3 para HPC

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- | --- | ------------------------| --- |
|SLES para HPC 1 ou 2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES para HPC 3-4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES para HPC 5+ vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Nomes do marketplace no portal do Azure:

- SLES para SAP 15 (prioridade)
- SLES para SAP 12 SP3 (prioridade)
- SLES para SAP SP2 12 (prioridade)

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- |------------------------| --- | --- |
|SLES para SAP Priority 1 ou 2 vCPUs|497fe0b6-Fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES para SAP prioridade 3 e 4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES para SAP de prioridade 5 + vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>Prioridade do SUSE Linux Enterprise Server

Nomes do marketplace no portal do Azure:

- SLES 15 (PRIORIDADE)
- SLES 12 SP3 (prioridade)
- SLES 11 SP4 (prioridade)

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 a 4 vCPUs |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 a 4 vCPUs |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vCPUs |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPUs |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 vCPUs de núcleo |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vCPUs |bb21066f-fe46-46D3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vCPUs |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|SLES 24 núcleos vCPUs |-005 d-4075 ac11 822ccde9e8f6|3.2| ND24s|
|SLES 32 vCPUs |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 núcleos vCPUs |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vCPUs |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 núcleos vCPUs |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 núcleos vCPUs |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 núcleos vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Nomes do marketplace no portal do Azure:

- SLES 15
- SLES 15 (padrão)
- SLES 12 SP3 (padrão)

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- |------------------------| --- |--- |
|SLES 1-2 núcleos vCPUs |4b2fecfc-B110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3 a 4 núcleos vCPUs |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pagar antecipadamente por planos de software do SUSE com reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
