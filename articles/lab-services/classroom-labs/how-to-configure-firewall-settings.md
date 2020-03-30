---
title: Configurações de firewall para o Azure Lab Services
description: Saiba como determinar o endereço IP público e a gama de números de porta de máquinas virtuais em laboratório para que as informações possam ser adicionadas às regras de firewall.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443441"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Configurações de firewall para o Azure Lab Services

Cada organização ou escola criará sua própria rede de uma forma que melhor se adapte às suas necessidades.  Às vezes, isso inclui a definição de regras de firewall que bloqueiam conexões de protocolo de desktop remoto (rdp) ou Secure Shell (ssh) a máquinas fora de sua própria rede.  Como o Azure Lab Services é executado na nuvem pública, alguma configuração extra talvez seja necessária para permitir que os alunos acessem sua VM ao se conectarem da rede do campus.

Cada laboratório usa endereço IP público único e várias portas.  Todas as VMs, tanto o modelo VM quanto as VMs estudantis, usarão este endereço IP público.  O endereço IP público não mudará para a vida do laboratório.  No entanto, cada VM terá um número de porta diferente.  Os números da porta variam de 49152 a 65535.  A combinação de endereço IP público e número de porta é usada para conectar instrutor e alunos à VM correta.  Este artigo abordará como encontrar o endereço IP público específico usado por um laboratório.  Essas informações podem ser usadas para atualizar as regras de firewall de entrada e saída para que os alunos possam acessar suas VMs.

>[!IMPORTANT]
>Cada laboratório terá um endereço IP público diferente.

## <a name="find-public-ip-for-a-lab"></a>Encontre IP público para um laboratório

Os endereços IP públicos de cada laboratório estão listados na página **all labs** da conta do laboratório lab Services.  Para saber como encontrar a página **All labs,** veja [como gerenciar laboratórios em uma conta de laboratório](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![Todas as páginas de laboratórios](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Você não verá o endereço IP público se a máquina de modelo do seu laboratório ainda não for publicada.

## <a name="conclusion"></a>Conclusão

Agora sabemos o endereço IP público do laboratório.  Regras de entrada e saída podem ser criadas para o firewall da organização para o endereço ip público e a faixa de porta 49152-65535.  Uma vez atualizadas as regras, os alunos podem acessar suas VMs sem que o firewall da rede bloqueie o acesso.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Conecte a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um usuário como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras configurações para um laboratório](how-to-configure-lab-accounts.md)
