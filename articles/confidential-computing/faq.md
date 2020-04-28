---
title: Perguntas frequentes sobre computação confidencial do Azure
description: Respostas para perguntas frequentes sobre a computação confidencial do Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189438"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Perguntas frequentes sobre a computação confidencial do Azure

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução [de cargas de trabalho de computação confidencial no Azure](overview.md).

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou postar [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Você também pode enviar uma solicitação de suporte do Azure. Para enviar uma solicitação de suporte, na [página de suporte do Azure](https://azure.microsoft.com/support/options/), selecione obter suporte.

## <a name="confidential-computing-virtual-machines"></a>Máquinas virtuais de computação confidencial<a id="vm-faq"></a>

1. **Como você pode começar a implantar VMs da série DCsv2?**

   Aqui estão algumas maneiras que você pode implantar uma VM DCsv2:
   - Usando um [modelo de Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - Da [portal do Azure](https://portal.azure.com/#create/hub)
   - No modelo de solução do Marketplace de [computação confidencial do Azure (máquina virtual)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) . O modelo de solução do Marketplace ajudará a restringir um cliente aos cenários com suporte (regiões, imagens, disponibilidade, criptografia de disco). 

1. **Todas as imagens do sistema operacional funcionarão com a computação confidencial do Azure?**

   Não. As máquinas virtuais só podem ser implantadas em máquinas virtuais de geração 2. Oferecemos suporte de geração 2 para o Ubuntu Server 18, 4, Ubuntu Server 16, 4 e Windows Server 2016 datacenter. Leia mais sobre as VMs da Gen 2 no [Linux](../virtual-machines/linux/generation-2.md) e no [Windows](../virtual-machines/windows/generation-2.md)

1. **As máquinas virtuais DCsv2 estão esmaecidas no portal e não consigo selecionar uma**

   Com base na bolha de informações ao lado da VM, há ações diferentes a serem tomadas:
    -   **UnsupportedGeneration**: altere a geração da imagem da máquina virtual para "Gen2".
    -   **NotAvailableForSubscription** : a região ainda não está disponível para sua assinatura. Selecione uma região disponível.
    -   **InsufficientQuota**: [crie uma solicitação de suporte para aumentar sua cota](../azure-portal/supportability/per-vm-quota-requests.md). As assinaturas de avaliação gratuita não têm cota para VMs de computação confidencial. 

1. **As máquinas virtuais DCsv2 não aparecem quando tento procurá-las no seletor de tamanho do portal**

   Verifique se você selecionou uma região disponível. Além disso, certifique-se de selecionar "limpar todos os filtros" no seletor de tamanho. 

1. **Qual é a diferença entre as VMs das séries DCsv2 e DC?**

   As VMs da série DC são executadas em processadores Intel de 6 núcleos mais antigos com o Intel SGX. Eles têm menos memória total, menos memória EPC (compartimento de página enclave) e estão disponíveis em menos regiões. Essas VMs só estão disponíveis no leste dos EUA e a Europa Ocidental estão disponíveis em dois tamanhos: Standard_DC2s e Standard_DC4s. Eles não vão para a GA e só podem ser implantados na instância do Marketplace da [VM da série DC de computação confidencial [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

1. **As máquinas virtuais DCsv2 estão disponíveis globalmente?**

   Não, essas máquinas virtuais só estão disponíveis em regiões selecionadas. Verifique a [página produtos por regiões](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter as regiões disponíveis mais recentes. 

1. **Como fazer instalar o SDK do Open enclave?**
   
   Para obter instruções sobre como instalar o SDK do OE em um computador, seja no Azure ou no local, siga as instruções no [GitHub do SDK do Open enclave](https://github.com/openenclave/openenclave).
     
   Você também pode acessar o GitHub do SDK do enclave aberto para obter instruções de instalação específicas do sistema operacional:
     - [Instalar o SDK do OE no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Instalar o SDK do OE no Ubuntu 18, 4](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Instalar o SDK do OE no Ubuntu 16, 4](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
