---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123257"
---
A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente.  Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Utilizar um tamanho isolado garante que sua máquina virtual será apenas sendo executada na instância de servidor específico.  As ofertas atuais da máquina virtual isolada incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Você pode aprender mais sobre cada tamanho isolado disponível [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Aposentando-se D15_v2/DS15_v2 isolamento em 15 de maio de 2020
**Atualização em 10 de fevereiro de 2020: O cronograma de aposentadoria "isolamento" foi prorrogado para 15 de maio de 2020"**

O Azure Dedicated Host agora é o GA, que permite executar as máquinas virtuais Linux e Windows da sua organização em servidores físicos de um único inquilino. Planejamos substituir totalmente as VMs azure isoladas pelo Host Dedicado Azure. Após **15 de maio de 2020,** as VMs D15_v2/DS15_v2 Azure não serão mais isoladas de hardware.

## <a name="how-does-this-affect-me"></a>Como isso me afeta?
Após 15 de maio de 2020, não forneceremos mais uma garantia de isolamento para suas máquinas virtuais D15_v2/DS15_v2 Azure. 

## <a name="what-actions-should-i-take"></a>Que ações devo tomar?
Se o isolamento de hardware não for necessário para você, não há ação que você precise tomar. 

Se o isolamento for necessário para você, antes de 15 de maio de 2020, você precisará:

• [Migre](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) sua carga de trabalho para o Host Dedicado do Azure.

• [Solicite acesso](https://aka.ms/D15iRequestAccess) a uma D15i_v2 e DS15i_v2 Azure VM, para obter o mesmo desempenho de preço. Esta opção só está disponível para cenários de instâncias reservadas de um ano e pay-as-you-go.    

• [Migre](https://azure.microsoft.com/blog/resize-virtual-machines/) sua carga de trabalho para outra máquina virtual isolada do Azure. 

Para mais detalhes veja abaixo:

## <a name="timeline"></a>Linha do tempo
| Data | Ação | 
| --- | --- |
| 18 de novembro de 2019 | Disponibilidade de D/DS15i_v2 (PAYG, RI de 1 ano) |
| 14 de maio de 2020  | Último dia para comprar R/DS15i_v2 1 ano de RI | 
| 15 de maio de 2020   | Garantia de isolamento D/DS15_v2 removida | 
| 15 de maio de 2021  | Aposentar D/DS15i_v2 (todos os clientes exceto quem comprou 3 anos de RI de D/DS15_v2 antes de 18 de novembro de 2019)| 
| 17 de novembro de 2022  | Aposentar D/DS15i_v2 quando 3 anos de RIs feitos (para clientes que compraram 3 anos de RI de D/DS15_v2 antes de 18 de novembro de 2019) | 

## <a name="faq"></a>Perguntas frequentes
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>P: O tamanho D/DS15_v2 vai se aposentar?
**R:** Não, apenas o recurso de "isolamento" vai se aposentar. Se você não precisa de isolamento, você não precisa tomar nenhuma ação.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>P: O tamanho D/DS15i_v2 vai se aposentar?
**R:** Sim, o tamanho só está disponível até 15 de maio de 2021. Para clientes que compraram RIs de 3 anos em D/DS15_v2 até 18 de novembro de 2019 terão acesso a D/DS15i_v2 até 17 de novembro de 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>P: Por que não estou vendo os novos tamanhos de D/DS15i_v2 no portal?
**A:** Se você é um cliente D/DS15_v2 atual e deseja usar os novos tamanhos de D/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>P: Por que não estou vendo nenhuma cota para os novos tamanhos de D/DS15i_v2?
**A:** Se você é um cliente D/DS15_v2 atual e deseja usar os novos tamanhos de D/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Quando os outros tamanhos isolados vão se aposentar?
**R:** Forneceremos lembretes com 12 meses de antecedência do descomissionamento oficial dos tamanhos.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: Existe um tempo de inatividade quando meu vm pousa em um hardware não isolado?
**A:** Se você não precisa de isolamento, você não precisa tomar nenhuma ação e você não veria qualquer tempo de inatividade.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>P: Há alguma mudança de custo para a mudança para uma máquina virtual não isolada?
**A:** Não 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>P: Eu já comprei uma instância reservada de 1 ou 3 anos para D15_v2 ou Ds15_v2. Como o desconto será aplicado ao uso da minha VM?
**R:** Os RIs adquiridos antes de 18 de novembro de 2019 estenderão automaticamente a cobertura para a nova série isolada de VM. 

| RI |  Flexibilidade do tamanho da instância | Elegibilidade de benefícios |   
| --- | --- | --- |
|   D15_v2  |   Desativado     |   D15_v2 e D15i_v2 |    
|   D15_v2  |   Por  |   D15_v2 séries e D15i_v2 todos receberão o benefício de RI. |    
|   D14_v2  |   Por  |   D15_v2 séries e D15i_v2 todos receberão o benefício de RI. |    
 
Da mesma forma para a série Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>P: Eu quero comprar instâncias reservadas adicionais para Dv2. Qual devo escolher?
**R**: Todas as RIs compradas após 18 de novembro de 2019, têm o seguinte comportamento. 

| RI |  Flexibilidade do tamanho da instância | Elegibilidade de benefícios |   
| --- | --- | --- |
| D15_v2 |  Desativado |   D15_v2 apenas  
| D15_v2 |  Por |    D15_v2 série receberá o benefício do RI. O novo D15i_v2 não será elegível para o benefício de RI deste tipo de RI. | 
| D15i_v2 |     Desativado | D15i_v2 apenas |  
| D15i_v2 |     Por  | D15i_v2 apenas | 
 
A flexibilidade de tamanho de instância não pode ser usada para aplicar-se a outros tamanhos, como D2_v2, D4_v2 ou D15_v2. Da mesma forma, para a série Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>P: Posso comprar um novo RI de 3 anos para D15i_v2 e DS15i_v2?
**R:** Infelizmente não, apenas 1 ano de RI está disponível para nova compra.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>P: Posso mover minha instância de reserva de D15_v2/DS15_v2 existente para uma instância reservada de tamanho isolado?
**R:** Esta ação não é necessária, pois o benefício se aplicará a tamanhos isolados e não isolados. Mas o Azure suportará a alteração das instâncias reservadas de D15_v2/DS15_v2 existentes para D15i_v2/DS15i_v2. Para todas as outras Instâncias Reservadas Dv2/Dsv2, use a Instância Reservada existente ou compre novas Instâncias Reservadas para os tamanhos isolados.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: Eu sou um cliente de malha de serviço Azure que depende dos níveis de durabilidade de prata ou ouro. Essa mudança me impacta?
**A**: Não. As garantias [fornecidas](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) pelos Níveis de Durabilidade da Service Fabric continuarão a funcionar mesmo após essa alteração. Se você precisar de isolamento físico de hardware por outras razões, você ainda pode precisar tomar uma das ações descritas acima. 
