---
title: Configurações de firewall para o Azure Lab Services
description: Saiba como determinar o endereço IP público e o intervalo de números de porta de máquinas virtuais em um laboratório para que as informações possam ser adicionadas às regras de firewall.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443441"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Configurações de firewall para o Azure Lab Services

Cada organização ou escola configurará sua própria rede de uma maneira que melhor atenda às suas necessidades.  Às vezes, isso inclui a definição de regras de firewall que bloqueiam conexões protocolo RDP (RDP) ou Secure Shell (SSH) para computadores fora de sua própria rede.  Como Azure Lab Services é executado na nuvem pública, algumas configurações extras talvez sejam necessárias para permitir que os alunos acessem sua VM ao se conectarem da rede do campus.

Cada laboratório usa um único endereço IP público e várias portas.  Todas as VMs, tanto a VM de modelo quanto as VMs de aluno usarão esse endereço IP público.  O endereço IP público não será alterado durante a vida útil do laboratório.  No entanto, cada VM terá um número de porta diferente.  Os números de porta variam de 49152 a 65535.  A combinação de endereço IP público e número da porta é usada para conectar o instrutor e os alunos à VM correta.  Este artigo explicará como encontrar o endereço IP público específico usado por um laboratório.  Essas informações podem ser usadas para atualizar as regras de firewall de entrada e saída para que os alunos possam acessar suas VMs.

>[!IMPORTANT]
>Cada laboratório terá um endereço IP público diferente.

## <a name="find-public-ip-for-a-lab"></a>Localizar IP público para um laboratório

Os endereços IP públicos de cada laboratório são listados na página **todos os laboratórios** da conta do laboratório dos serviços de laboratório.  Para obter instruções sobre como encontrar a página **todos os laboratórios** , consulte [como gerenciar laboratórios em uma conta de laboratório](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> página ![todos os laboratórios](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Você não verá o endereço IP público se o computador de modelo do seu laboratório ainda não estiver publicado.

## <a name="conclusion"></a>Conclusão

Agora sabemos o endereço IP público do laboratório.  As regras de entrada e saída podem ser criadas para o firewall da organização para o endereço IP público e o intervalo de portas 49152-65535.  Depois que as regras são atualizadas, os alunos podem acessar suas VMs sem o firewall de rede bloqueando o acesso.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Permitir que o criador de laboratório escolha o local do laboratório](allow-lab-creator-pick-lab-location.md)
- [Conectar a rede do seu laboratório a uma rede virtual de mesmo nível](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Definir outras configurações para um laboratório](how-to-configure-lab-accounts.md)
