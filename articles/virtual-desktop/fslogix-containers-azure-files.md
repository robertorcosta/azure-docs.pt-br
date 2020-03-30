---
title: Arquivos de contêineres de perfil do Windows Virtual Desktop FSLogix - Azure
description: Este artigo descreve os contêineres de perfil FSLogix dentro dos arquivos Windows Virtual Desktop e Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127888"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contêineres de perfil FSLogix e arquivos do Azure

O serviço de desktop virtual do Windows recomenda os contêineres de perfil FSLogix como uma solução de perfil de usuário. O FSLogix foi projetado para vagar perfis em ambientes de computação remota, como o Windows Virtual Desktop. Ele armazena um perfil de usuário completo em um único contêiner. No login, este contêiner é conectado dinamicamente ao ambiente de computação usando disco rígido virtual (VHDX) e disco rígido virtual hyper-v (VHDX). O perfil do usuário está disponível imediatamente e aparece no sistema exatamente como um perfil de usuário nativo. Este artigo descreve como os contêineres de perfil FSLogix usados com o Azure Files funcionam no Windows Virtual Desktop.

>[!NOTE]
>Se você estiver procurando material de comparação sobre as diferentes opções de armazenamento de contêiner de perfil FSLogix no Azure, consulte [opções de armazenamento para contêineres de perfil FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Perfis do usuário

Um perfil de usuário contém elementos de dados sobre um indivíduo, incluindo informações de configuração, como configurações de desktop, conexões de rede persistentes e configurações de aplicativos. Por padrão, o Windows cria um perfil de usuário local que é fortemente integrado ao sistema operacional.

Um perfil de usuário remoto fornece uma partição entre os dados do usuário e o sistema operacional. Permite que o sistema operacional seja substituído ou alterado sem afetar os dados do usuário. Em RDSH (Remote Desktop Session Session Host) e Virtual Desktop Infrastructures (VDI), o sistema operacional pode ser substituído pelas seguintes razões:

- Uma atualização do sistema operacional
- Uma substituição de uma Máquina Virtual (VM) existente
- Um usuário que faz parte de um ambiente RDSH ou VDI agrupado (não persistente)

Os produtos Microsoft operam com várias tecnologias para perfis de usuários remotos, incluindo essas tecnologias:
- Perfis de usuários roaming (RUP)
- Discos de perfil do usuário (UPD)
- Roaming de estado corporativo (ESR)

UPD e RUP são as tecnologias mais utilizadas para perfis de usuários em ambientes RDSH (Remote Desktop Session Host) e Virtual Hard Disk (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Desafios com tecnologias de perfil de usuário anteriores

As soluções microsoft existentes e herdadas para perfis de usuários vieram com vários desafios. Nenhuma solução anterior lidou com todas as necessidades de perfil do usuário que vêm com um ambiente RDSH ou VDI. Por exemplo, o UPD não pode lidar com arquivos OST grandes e o RUP não persiste em configurações modernas.

#### <a name="functionality"></a>Funcionalidade

A tabela a seguir mostra benefícios e limitações das tecnologias anteriores do perfil do usuário.

| Tecnologia | Configurações modernas | Configurações do Win32 | Configurações do Sistema Operacional | Dados do usuário | Suportado no SKU do servidor | Armazenamento back-end no Azure | Armazenamento back-end no local | Suporte à versão | Signo subseqüente no tempo |Observações|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Discos de perfil do usuário (UPD)** | Sim | Sim | Sim | Sim | Sim | Não | Sim | Ganhe 7+ | Sim | |
| **Perfil do Usuário Itinerante (RUP), modo de manutenção** | Não | Sim | Sim | Sim | Sim| Não | Sim | Ganhe 7+ | Não | |
| **Roaming do Estado Empresarial (ESR)** | Sim | Não | Sim | Não | Consulte as observações | Sim | Não | Win 10 | Não | Funções no SKU do servidor, mas sem suporte à interface do usuário |
| **User Experience Virtualization (UE-V)** | Sim | Sim | Sim | Não | Sim | Não | Sim | Ganhe 7+ | Não |  |
| **Arquivos de nuvem oneDrive** | Não | Não | Não | Sim | Consulte as observações | Consulte as observações  | Ver Notas | Ganhe 10 RS3 | Não | Não testado no SKU do servidor. O armazenamento back-end no Azure depende do cliente sync. O back-end storage on-prem precisa de um cliente de sincronização. |

#### <a name="performance"></a>Desempenho

O UPD requer o [S2D (Storage Spaces Direct)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) para atender aos requisitos de desempenho. O UPD usa o protocolo SMB (Server Message Block, bloco de mensagens de servidor). Ele copia o perfil para a VM em que o usuário está sendo registrado. UPD com S2D é a solução que recomendamos para o Windows Virtual Desktop.  

#### <a name="cost"></a>Custo

Enquanto os clusters S2D alcançam o desempenho necessário, o custo é caro para os clientes corporativos, mas especialmente caro para clientes de pequenas e médias empresas (SMB). Para essa solução, as empresas pagam por discos de armazenamento, juntamente com o custo das VMs que usam os discos para compartilhar.

#### <a name="administrative-overhead"></a>Sobrecarga administrativa

Os clusters S2D requerem um sistema operacional corrigido, atualizado e mantido em um estado seguro. Esses processos e a complexidade da configuração da recuperação de desastres S2D tornam o S2D viável apenas para empresas com uma equipe de TI dedicada.

## <a name="fslogix-profile-containers"></a>Recipientes de perfil FSLogix

Em 19 de novembro de 2018, [a Microsoft adquiriu o FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). O FSLogix aborda muitos desafios de contêiner de perfil. Entre eles estão:

- **Desempenho:** Os [contêineres de perfil FSLogix](/fslogix/configure-profile-container-tutorial/) são problemas de desempenho de alto desempenho e resolvem que historicamente bloquearam o modo de troca em cache.
- **OneDrive:** Sem os contêineres de perfil FSLogix, o OneDrive for Business não é suportado em ambientes RDSH ou VDI não persistentes. [As práticas recomendadas do OneDrive for Business e fSLogix](/fslogix/overview/) descrevem como interagem. Para obter mais informações, consulte [Use o cliente sync em desktops virtuais](/deployoffice/rds-onedrive-business-vdi/).
- **Pastas adicionais:** O FSLogix oferece a capacidade de estender os perfis de usuário para incluir pastas adicionais.

Desde a aquisição, a Microsoft começou a substituir as soluções de perfil de usuário existentes, como o UPD, por contêineres de perfil FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integração de arquivos do Azure com o serviço de domínio do diretório ativo do Azure

O desempenho e os recursos dos contêineres de perfil FSLogix aproveitam a nuvem. Em 7 de agosto de 2019, o Microsoft Azure Files anunciou a disponibilidade geral da autenticação do [Azure Files com o Azure Active Directory Domain Service (AD DS)](../storage/files/storage-files-active-directory-overview.md). Ao abordar custos e despesas gerais administrativas, o Azure Files com a azure AD DS Authentication é uma solução premium para perfis de usuários no serviço Windows Virtual Desktop.

## <a name="best-practices-for-windows-virtual-desktop"></a>Práticas recomendadas para Windows Virtual Desktop

O Windows Virtual Desktop oferece controle total sobre tamanho, tipo e contagem de VMs que estão sendo usadas pelos clientes. Para obter mais informações, consulte [O que é o Windows Virtual Desktop?](overview.md).

Para garantir que seu ambiente de área de trabalho virtual do Windows siga as melhores práticas:

- A conta de armazenamento de arquivos do Azure deve estar na mesma região que as VMs do host de sessão.
- As permissões do Azure Files devem corresponder às permissões descritas em [Requisitos - Recipientes de perfil](/fslogix/fslogix-storage-config-ht).
- Cada pool de host deve ser construído do mesmo tipo e tamanho VM com base na mesma imagem mestre.
- Cada VM do pool de host deve estar no mesmo grupo de recursos para ajudar o gerenciamento, o dimensionamento e a atualização.
- Para um desempenho ideal, a solução de armazenamento e o contêiner de perfil FSLogix devem estar no mesmo local do data center.
- A conta de armazenamento contendo a imagem principal deve estar na mesma região e assinatura onde as VMs estão sendo provisionadas.

## <a name="next-steps"></a>Próximas etapas

Use os guias a seguir para configurar um ambiente de desktop virtual do Windows.

- Para começar a criar sua solução de virtualização de desktop, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- Para criar um pool de host situado no seu inquilino do Windows Virtual Desktop, consulte [Criar um pool de hosts com o Azure Marketplace](create-host-pools-azure-marketplace.md).
- Para configurar compartilhamentos de arquivos totalmente gerenciados na nuvem, consulte [Configurar o compartilhamento de arquivos do Azure](/azure/storage/files/storage-files-active-directory-enable/).
- Para configurar os recipientes de perfil FSLogix, consulte [Criar um recipiente de perfil para um pool de host usando um compartilhamento de arquivos](create-host-pools-user-profile.md).
- Para atribuir usuários a um pool de host, consulte [Gerenciar grupos de aplicativos para o Windows Virtual Desktop](manage-app-groups.md).
- Para acessar os recursos do Windows Virtual Desktop a partir de um navegador da Web, consulte [Conecte-se ao Windows Virtual Desktop](connect-web.md).
