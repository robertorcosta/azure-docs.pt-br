---
title: Configurar a recuperação de desastre da VM do Azure com o Azure Site Recovery
description: Saiba como configurar a recuperação de desastre para VMs do Azure em uma região do Azure diferente usando o serviço Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292477"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configurar a recuperação de desastre para VMs do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre para o VMs do Azure ao replicá-lo de uma região do Azure para outra. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação
> * Verificar as configurações de recursos de destino
> * Configurar a conectividade de rede de saída para VMs
> * Habilitar a replicação para uma VM

> [!NOTE]
> Este artigo fornece instruções para implantar a recuperação de desastre com as configurações mais simples. Caso queira saber mais sobre as configurações personalizadas, examine os artigos na [seção Instruções](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Examine os [componentes e a arquitetura do cenário](concepts-azure-to-azure-architecture.md).
- Analise os [requisitos de suporte](site-recovery-support-matrix-azure-to-azure.md) antes de começar.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Crie o cofre em qualquer região, exceto a região de origem.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. Em seguida, selecione **Ferramentas de TI e Gerenciamento** > **Backup e Site Recovery**.
1. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
1. Crie um grupo de recursos ou selecione um existente. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Para acessar o cofre no painel, selecione **Fixar no painel** e, em seguida, **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resource-settings"></a>Verificar as configurações de recursos de destino

Verifique a sua assinatura do Azure para obter a região de destino.

- Verifique se a sua assinatura do Azure permite criar VMs na região de destino. Contate o suporte para habilitar a cota necessária.
- Verifique se a sua assinatura tem recursos suficientes para dar suporte a tamanhos de VMs que correspondem às VMs de origem. O Site Recovery escolhe o mesmo tamanho, ou o tamanho mais próximo possível, para a VM de destino.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configurar a conectividade de rede de saída para VMs

Para o Site Recovery funcionar conforme o esperado, você precisa modificar a conectividade de rede de saída das VMs que deseja replicar.

> [!NOTE]
> O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas URLs:

| **URL** | **Detalhes** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| `login.microsoftonline.com` | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Permite que a VM se comunique com o serviço Site Recovery. |
| `*.servicebus.windows.net` | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se você estiver usando um NSG (grupo de segurança de rede), crie regras NSG baseadas em marca de serviço para acesso ao Armazenamento do Azure, ao Azure Active Directory, ao serviço Site Recovery e ao monitoramento do Site Recovery. [Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Verifique os certificados de VM do Azure

Verifique se as VMs que você deseja replicar contam com os certificados raiz mais recentes. Caso contrário, a VM não poderá ser registrada no Site Recovery devido a restrições de segurança.

- Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
- Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções internas para controlar as operações de gerenciamento do Site Recovery.

- **Colaborador do Site Recovery** -essa função tem todas as permissões necessárias para gerenciar operações do Azure Site Recovery em um cofre dos Serviços de Recuperação. No entanto, um usuário com essa função não pode criar ou excluir um cofre dos Serviços de Recuperação ou atribuir direitos de acesso a outros usuários. Essa função é ideal para os administradores de recuperação de desastre que podem habilitar e gerenciar a recuperação de desastre para aplicativos ou organizações inteiras.

- **Operador do Site Recovery** - Essa função tem permissões para executar e gerenciar operações de Failover e Failback. Um usuário com essa função não pode habilitar ou desabilitar a replicação, criar ou excluir cofres, registrar nova infraestrutura ou atribuir direitos de acesso a outros usuários. Essa função é ideal para um operador de recuperação de desastre que pode executar failover em máquinas virtuais ou aplicativos quando instruído por administradores de TI ou proprietários do aplicativo em uma situação de desastre real ou simulada. Após a resolução do desastre, o operador de recuperação de desastre pode proteger novamente e fazer failback das máquinas virtuais.

- **Leitor do Site Recovery**: essa função tem permissões para exibir todas as operações de gerenciamento do Site Recovery. Essa função é ideal para um executivo de monitoramento de TI que possa monitorar o estado atual de proteção e gerar tíquetes de suporte.

Saiba mais sobre as [funções internas do RBAC do Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Habilitar a replicação para uma VM

As seções a seguir descrevem como habilitar a replicação.

### <a name="select-the-source"></a>Selecione a origem

Para iniciar a configuração de replicação, escolha a origem em que as VMs do Azure estão em execução.

1. Acesse **Cofres dos Serviços de Recuperação**, selecione o nome do cofre e, em seguida, selecione **+Replicar**.
1. Em **Origem**, selecione **Azure**.
1. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
1. Selecione a **Assinatura de origem** na qual as máquinas virtuais estão em execução. Pode ser qualquer assinatura dentro do mesmo locatário do Azure Active Directory na qual existe o cofre dos serviços de recuperação.
1. Selecione o **Grupo de recursos de origem** e **OK** para salvar as configurações.

   ![Configurar origem](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Selecione as VMs

O Site Recovery recupera uma lista das VMs associadas ao serviço de nuvem/grupo de recursos e assinatura.

1. Em **Máquinas Virtuais**, selecione as VMs que deseja replicar.
1. Selecione **OK**.

### <a name="configure-replication-settings"></a>Definir configurações de replicação

O Site Recovery cria as configurações padrão e a política de replicação para a região de destino. É possível alterar essas configurações conforme necessário.

1. Selecione **Configurações** para ver o destino e as configurações de replicação.

1. Para substituir as configurações de destino padrão, selecione **Personalizar** ao lado de **Grupo de recursos, Rede, Armazenamento e Disponibilidade**.

   ![Definir configurações](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Personalize as configurações de destino, conforme resumido na tabela.

   | **Configuração** | **Detalhes** |
   | --- | --- |
   | **Assinatura de destino** | Por padrão, a assinatura de destino será igual à assinatura de origem. Selecione **Personalizar** para escolher outra assinatura de destino no mesmo locatário do Azure Active Directory. |
   | **Local de destino** | A região de destino usada para recuperação de desastre.<br/><br/> É recomendável que a localização de destino corresponda à localização do cofre do Site Recovery. |
   | **Grupo de recursos de destino** | O grupo de recursos na região de destino que contém as VMs do Azure após o failover.<br/><br/> Por padrão, o Site Recovery cria um grupo de recursos na região de destino com um sufixo `asr`. O local do grupo de recursos de destino pode ser qualquer região exceto a região em que as máquinas virtuais de origem estão hospedadas. |
   | **Rede virtual de destino** | A rede na região de destino na qual as VMs estarão localizadas após o failover.<br/><br/> Por padrão, o Site Recovery cria uma rede virtual (e sub-redes) na região de destino com um sufixo `asr`. |
   | **Contas de armazenamento de cache** | O Site Recovery utiliza uma conta de armazenamento na região de origem. As alterações às VMs de origem são enviadas para essa conta, antes da replicação para a localização de destino.<br/><br/> Caso esteja usando a conta de armazenamento em cache habilitado para firewall, habilite a opção **Permitir serviços confiáveis da Microsoft**. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Além disso, permita acesso a, pelo menos, uma sub-rede da VNet de origem. |
   | **Contas de armazenamento de destino (a VM de origem não usa discos gerenciados)** | Por padrão, o Site Recovery cria uma nova conta de armazenamento na região de destino, para espelhar a conta de armazenamento da VM de origem.<br/><br/> Caso esteja usando a conta de armazenamento de cache com firewall habilitado, assegure-se de **Permitir serviços confiáveis da Microsoft**. |
   | **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)** | Por padrão, o Site Recovery cria discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou Premium) do disco gerenciado da VM de origem. Só é possível personalizar o tipo de Disco. |
   | **Conjuntos de disponibilidade de destino** | Por padrão, o Azure Site Recovery cria um conjunto de disponibilidade na região de destino com um nome que tenha um sufixo `asr` para a parte de VMs de um conjunto de disponibilidade na região de origem. Caso o conjunto de disponibilidade criado pelo Azure Site Recovery já exista, ele será reutilizado. |
   | **Zonas de disponibilidade de destino** | Por padrão, o Site Recovery atribui o mesmo número da zona que a região de origem na região de destino se a região de destino oferecer suporte a zonas de disponibilidade.<br/><br/> Se a região de destino não oferecer suporte a zonas de disponibilidade, as VMs de destino forem configuradas como instâncias isoladas por padrão.<br/><br/> Selecione **Personalizar** para configurar VMs como parte de um conjunto de disponibilidade na região de destino.<br/><br/> Você não poderá alterar o tipo de disponibilidade (instância única, conjunto de disponibilidade ou zona de disponibilidade) depois de habilitar a replicação. Para alterar o tipo de disponibilidade, desabilite e habilite a replicação. |

1. Para personalizar as configurações da política de replicação, selecione **Personalizar** ao lado da **Política de replicação** e modifique as configurações conforme necessário.

   | **Configuração** | **Detalhes** |
   | --- | --- |
   | **Nome da política de replicação** | Nome da política. |
   | **Retenção do ponto de recuperação** | Por padrão, o Site Recovery mantém os pontos de recuperação por 24 horas. É possível configurar um valor entre 1 e 72 horas. |
   | **Frequência de instantâneos consistentes com aplicativo** | Por padrão, o Site Recovery obtém um instantâneo consistente com o aplicativo a cada 4 horas. É possível configurar qualquer valor entre 1 e 12 horas.<br/><br/> Um instantâneo consistente com o aplicativo é um instantâneo em um ponto no tempo dos dados do aplicativo dentro da VM. O VSS (Serviço de Cópias de Sombra de Volume) garante que o aplicativo na VM esteja em um estado consistente quando o instantâneo for criado. |
   | **Grupo de replicação** | Se o aplicativo precisa de consistência de VMs múltiplas entre VMs, você pode criar um grupo de replicação para essas VMs. Por padrão, as VMs selecionadas não fazem parte de nenhum grupo de replicação. |

1. Em **Personalizar**, selecione **Sim** para a consistência de várias VMs caso queira adicionar VMs a um grupo de replicação novo ou existente. Depois, selecione **OK**.

   > [!NOTE]
   > - Todos os computadores de um grupo de replicação terão pontos de recuperação consistentes com o aplicativo e consistentes com falhas compartilhados quando passarem por failover.
   > - Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho (faz uso intenso da CPU). Ela deverá ser usada somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência entre vários computadores.
   > - Você pode ter um máximo de 16 VMs em um grupo de replicação.
   > - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Assegure-se de que não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs através desta porta.
   > - Para VMs do Linux em um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 seja aberto manualmente, de acordo com as diretrizes da versão específica do Linux.

### <a name="configure-encryption-settings"></a>Definir configurações de criptografia

Se a VM de origem tiver o ADE (Azure Disk Encryption) habilitado, examine as configurações.

1. Verifique as configurações:
   1. **Cofres de chaves de criptografia de disco**: Por padrão, o Site Recovery cria um cofre de chaves nas chaves de criptografia do disco da VM de origem, com um sufixo `asr`. Se o cofre de chaves já existir, ele será reutilizado.
   1. **Cofres de chaves de criptografia de chave**: Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo `asr` e se baseia nas chaves de criptografia de chave da VM de origem. Caso o cofre de chave criado pelo Azure Site Recovery já exista, ele será reutilizado.
1. Selecione **Personalizar** para selecionar cofres de chaves personalizados.

> [!NOTE]
> Somente VMs do Azure executando sistemas operacionais Windows e [habilitadas para criptografia com o aplicativo do Azure AD](https://aka.ms/ade-aad-app) são compatíveis com o Azure Site Recovery atualmente.

### <a name="track-replication-status"></a>Acompanhar o status de replicação

Depois que a replicação for habilitada, você poderá acompanhar o status do trabalho.

1. Em **Configurações**, selecione **Atualizar** para obter o status mais recente.
1. Acompanhe o progresso e o status da seguinte maneira:
   1. Acompanhe o progresso do trabalho **Habilitar proteção** em **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
   1. Em **Configurações** > **Itens Replicados**, você pode exibir o status das máquinas virtuais e o andamento da replicação inicial. Selecione a VM para fazer uma busca detalhada nas configurações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou a recuperação de desastre para uma VM do Azure. Agora você pode executar uma simulação de recuperação de desastre para verificar se o failover funciona conforme o esperado.

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md)
