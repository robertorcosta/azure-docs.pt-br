---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128997"
---
## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em hosts dedicados.

## <a name="benefits"></a>Benefícios 

Reservar todo o host oferece os seguintes benefícios:

-   Isolamento de hardware no nível do servidor físico. Nenhuma outra VM será colocada em seus hosts. Os hosts dedicados são implantados nos mesmos data centers e compartilham a mesma infra-estrutura de armazenamento subjacente e de rede que outros hosts não isolados.
-   Controle sobre eventos de manutenção iniciados pela plataforma Azure. Embora a maioria dos eventos de manutenção tenha pouco ou nenhum impacto em suas máquinas virtuais, existem algumas cargas de trabalho sensíveis onde cada segundo de pausa pode ter um impacto. Com hosts dedicados, você pode optar por uma janela de manutenção para reduzir o impacto ao seu serviço.
-   Com o benefício híbrido do Azure, você pode trazer suas próprias licenças para Windows e SQL para o Azure. Usar os benefícios híbridos proporciona benefícios adicionais. Para obter mais informações, consulte [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupos, anfitriões e VMs  

![Veja os novos recursos para hosts dedicados.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Um **grupo anfitrião** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de host em uma região e uma região de disponibilidade e adiciona hosts a ele.

Um **host** é um recurso, mapeado para um servidor físico em um data center do Azure. O servidor físico é alocado quando o host é criado. Um host é criado dentro de um grupo de host. Um host tem um SKU descrevendo quais tamanhos de VM podem ser criados. Cada host pode hospedar várias VMs, de tamanhos diferentes, desde que sejam da mesma série de tamanho.

Ao criar uma VM no Azure, você pode selecionar qual host dedicado usar para sua VM. Você tem controle total sobre quais VMs são colocadas em seus hosts.


## <a name="high-availability-considerations"></a>Considerações de alta disponibilidade 

Para alta disponibilidade, você deve implantar várias VMs, espalhadas por vários hosts (mínimo de 2). Com hosts dedicados ao Azure, você tem várias opções para provisionar sua infra-estrutura para moldar seus limites de isolamento de falhas.

### <a name="use-availability-zones-for-fault-isolation"></a>Use zonas de disponibilidade para isolamento de falhas

As zonas de disponibilidade são locais físicos exclusivos dentro de uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Um grupo de host é criado em uma única zona de disponibilidade. Uma vez criados, todos os hosts serão colocados dentro dessa zona. Para obter alta disponibilidade entre as regiões, você precisa criar vários grupos de host (um por região) e espalhar seus hosts de acordo.

Se você atribuir um grupo de host a uma zona de disponibilidade, todas as VMs criadas nesse host devem ser criadas na mesma região.

### <a name="use-fault-domains-for-fault-isolation"></a>Use domínios de falha para isolamento de falhas

Um host pode ser criado em um domínio de falha específico. Assim como a VM em um conjunto de escalas ou conjunto de disponibilidade, hosts em diferentes domínios de falha serão colocados em diferentes racks físicos no data center. Quando você cria um grupo de host, você é obrigado a especificar a contagem de domínios de falha. Ao criar hosts dentro do grupo de host, você atribui o domínio de falha para cada host. As VMs não exigem nenhuma atribuição de domínio de falha.

Os domínios de falha não são os mesmos que a localização de col. Ter o mesmo domínio de falha para dois hosts não significa que eles estão próximos um do outro.

Os domínios de falha são escopo do grupo de host. Você não deve fazer qualquer suposição sobre anti-afinidade entre dois grupos de host (a menos que eles estejam em zonas de disponibilidade diferentes).

As VMs implantadas em hosts com diferentes domínios de falha terão seus serviços de discos gerenciados subjacentes em vários selos de armazenamento, para aumentar a proteção de isolamento de falhas.

### <a name="using-availability-zones-and-fault-domains"></a>Usando zonas de disponibilidade e domínios de falhas

Você pode usar ambos os recursos juntos para obter ainda mais isolamento de falhas. Neste caso, você especificará a contagem de domínio de disponibilidade e de falha para cada grupo de host, atribuirá um domínio de falha a cada um de seus hosts no grupo e atribuirá uma zona de disponibilidade a cada um de seus VMs

O modelo de amostra do Gerenciador de recursos encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) usa regiões e domínios de falha para espalhar hosts para máxima resiliência em uma região.

## <a name="maintenance-control"></a>Controle de manutenção

A infra-estrutura que suporta suas máquinas virtuais pode ocasionalmente ser atualizada para melhorar a confiabilidade, o desempenho, a segurança e para lançar novos recursos. A plataforma Azure tenta minimizar o impacto da manutenção da plataforma sempre que possível, mas os clientes com cargas de *trabalho sensíveis* à manutenção não podem tolerar nem mesmo alguns segundos que a VM precisa ser congelada ou desconectada para manutenção.

**O Controle de Manutenção** oferece aos clientes a opção de pular as atualizações regulares da plataforma agendadas em seus hosts dedicados e, em seguida, aplicá-la no momento de sua escolha dentro de uma janela de rolamento de 35 dias.

> [!NOTE]
>  O controle de manutenção está atualmente em visualização pública. Para obter mais informações, consulte **Atualizações de controle com controle de manutenção usando [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) ou [PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)**.

## <a name="capacity-considerations"></a>Considerações sobre a capacidade

Uma vez que um host dedicado é provisionado, o Azure atribui-o ao servidor físico. Isso garante a disponibilidade da capacidade quando você precisa provisionar sua VM. O Azure usa toda a capacidade da região (ou zona) para escolher um servidor físico para o seu host. Isso também significa que os clientes podem esperar ser capazes de aumentar sua pegada de host dedicada sem a preocupação de ficar sem espaço no cluster.

## <a name="quotas"></a>Cotas

Há um limite de cota padrão de 3000 vCPUs para hosts dedicados, por região. Mas, o número de hosts que você pode implantar também é limitado pela cota para a família de tamanho VM usada para o host. Por exemplo, uma assinatura **pay-as-you-go** pode ter apenas uma cota de 10 vCPUs disponíveis para a série tamanho Dsv3, na região leste dos EUA. Neste caso, você precisa solicitar um aumento de cota para pelo menos 64 vCPUs antes de poder implantar um host dedicado. Selecione o botão **Solicitar aumento** no canto superior direito para arquivar uma solicitação, se necessário.

![Captura de tela da página de uso e cotas no portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Para obter mais informações, consulte [cotas de vCPU da máquina virtual](/azure/virtual-machines/windows/quotas).

A avaliação gratuita e as assinaturas do MSDN não têm cota para hosts dedicados ao Azure.

## <a name="pricing"></a>Preços

Os usuários são cobrados por host dedicado, independentemente de quantas VMs sejam implantadas. Em sua declaração mensal você verá um novo tipo de recurso faturado de hosts. As VMs em um host dedicado ainda serão mostradas em sua declaração, mas carregarão um preço de 0.

O preço do host é definido com base na família VM, tipo (tamanho do hardware) e região. O preço do host é relativo ao maior tamanho de VM suportado no host.

O licenciamento de software, o armazenamento e o uso da rede são cobrados separadamente do host e das VMs. Não há nenhuma mudança nesses itens faturados.

Para obter mais informações, consulte [os preços do Host dedicado do Azure](https://aka.ms/ADHPricing).

Você também pode economizar custos com uma [instância reservada de hosts dedicados do Azure](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Tamanhos e gerações de hardware

Um SKU é definido para um host e representa a série e o tipo de tamanho vm. Você pode misturar várias VMs de tamanhos diferentes dentro de um único host, desde que sejam da mesma série de tamanho. 

O *tipo* é a geração de hardware. Diferentes tipos de hardware para a mesma série De VM serão de diferentes fornecedores de CPU e terão diferentes gerações de CPU e número de núcleos. 

Os tamanhos e tipos de hardware variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.


## <a name="host-life-cycle"></a>Ciclo de vida do hospedeiro


O Azure monitora e gerencia o estado de saúde de seus anfitriões. Os seguintes estados serão retornados quando você consultar seu host:

| Estado de Integridade   | Descrição       |
|----------|----------------|
| Host Disponível     | Não há problemas conhecidos com seu anfitrião.   |
| Hospedeiro investigação  | Estamos tendo alguns problemas com o anfitrião que estamos investigando. Este é um estado de transição necessário para o Azure tentar identificar o escopo e a causa raiz do problema identificado. As máquinas virtuais que estão funcionando no host podem ser impactadas. |
| Host Pendente Deallocate   | O Azure não pode restaurar o host de volta a um estado saudável e pedir que você reimplante suas máquinas virtuais fora deste host. Se `autoReplaceOnFailure` estiver habilitado, suas máquinas virtuais *serão atendidas por* hardware saudável. Caso contrário, sua máquina virtual pode estar sendo executado em um host que está prestes a falhar.|
| Host desalocado  | Todas as máquinas virtuais foram removidas do host. Você não está mais sendo cobrado por este host desde que o hardware foi retirado de rotação.   |

