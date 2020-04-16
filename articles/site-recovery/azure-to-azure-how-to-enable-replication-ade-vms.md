---
title: Habilitar a replicação para VMs Azure criptografados na recuperação do site do Azure
description: Este artigo descreve como configurar a replicação para VMs habilitados para criptografia de disco Azure de uma região do Azure para outra usando o Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408630"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais habilitadas para criptografia de disco Azure para outra região do Azure

Este artigo descreve como replicar VMs do Azure com a Azure Disk Encryption (ADE) habilitada, de uma região do Azure para outra.

>[!NOTE]
> Atualmente, o Site Recovery suporta ADE, com e sem AAD (AAD) Active Directory (AAD) para VMs executando sistemas operacionais Windows. Para sistemas operacionais Linux, só suportamos ADE sem AAD. Além disso, para máquinas que executam o ADE 1.1 (sem AAD), as VMs devem estar usando discos gerenciados. As VMs com discos não gerenciados não são suportadas. Se você mudar de ADE 0.1 (com AAD) para 1.1, você precisa desativar a replicação e habilitar a replicação de uma VM depois de ativar o 1.1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Permissões de usuário necessárias
A Recuperação do Site exige que o usuário tenha permissões para criar o cofre de chaves na região de destino e copiar chaves do cofre chave da região de origem para o cofre-chave da região alvo.

Para habilitar a replicação de VMs habilitados para criptografia de disco do portal Azure, o usuário precisa das seguintes permissões tanto na região de origem quanto nos cofres-chave da região de **destino.**

- Permissões de cofre de chave
    - Listar, criar e obter
    
- Permissões secretas de cofre de chave
    - Operações secretas de gestão
        - Obter, Listar e Definir
    
- Permissões de chave do cofre chave (necessáriaapenas se as VMs usarem chave de criptografia de chave para criptografar chaves de criptografia de disco)
    - Principais Operações de Gerenciamento
        - Obter, Listar e Criar
    - Operações criptográficas
        - Descriptografar e Criptografar

Para gerenciar permissões, vá para o recurso do cofre chave no portal. Adicione as permissões necessárias para o usuário. O exemplo a seguir mostra como habilitar permissões para o cofre-chave *ContosoWeb2Keyvault*, que está na região de origem.

1. Ir para **Home** > **Keyvaults** > **ContosoWeb2KeyVault > políticas de acesso**.

   ![Janela de permissões do cofre da chave](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Você pode ver que não há permissões de usuário. Selecione **Adicionar nova**. Digite as informações do usuário e permissões.

   ![Permissões do cofre de chaves](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se o usuário que está habilitando a recuperação de desastres (DR) não tiver permissões para copiar as chaves, um administrador de segurança que tenha permissões apropriadas pode usar o script a seguir para copiar os segredos e chaves de criptografia para a região de destino.

Para solucionar problemas de permissões, consulte [os problemas de permissão do cofre-chave](#trusted-root-certificates-error-code-151066) mais tarde neste artigo.

>[!NOTE]
>Para habilitar a replicação de VMs habilitados para criptografia de disco do portal, você precisa de pelo menos permissões de "Listar" nos cofres, segredos e chaves das chaves.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiar chaves de criptografia de disco para a região DR usando o script PowerShell

1. [Abra o código de script bruto "CopyKeys".](https://aka.ms/ade-asr-copy-keys-code)
2. Copie o script para um arquivo e **nomeie-o Copy-keys.ps1**.
3. Abra o aplicativo Windows PowerShell e vá até a pasta onde você salvou o arquivo.
4. Execute Copy-keys.ps1.
5. Forneça credenciais do Azure para fazer login.
6. Escolha a **assinatura do Azure** de suas VMs.
7. Aguarde o carregamento dos grupos de recursos e selecione o **grupo Recurso** de suas VMs.
8. Selecione as VMs da lista exibida. Apenas VMs habilitados para criptografia de disco estão na lista.
9. Selecione o **local de destino**.

    - **Cofres de chave de criptografia de disco**
    - **Cofres chave de criptografia**

   Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. O nome do cofre tem um sufixo "asr" baseado nas chaves de criptografia de disco vm de origem. Se já existe um cofre de chaves que foi criado pelo Site Recovery, ele será reutilizado. Selecione um cofre de chaves diferente da lista, se necessário.

## <a name="enable-replication"></a>Habilitar a replicação

Para este exemplo, a região principal do Azure é o leste da Ásia, e a região secundária é o Sudeste Asiático.

1. No cofre, selecione **+Replicar**.
2. Observe os seguintes campos.
    - **Fonte**: o ponto de origem das VMs que, neste caso, é o **Azure**.
    - **Localização da fonte**: A região do Azure onde você deseja proteger suas máquinas virtuais. Para este exemplo, a localização de origem é "Ásia Oriental".
    - **Modelo de implantação**: O modelo de implantação do Azure das máquinas de origem.
    - **Assinatura de origem**: a assinatura à qual suas máquinas virtuais de origem pertencem. Pode ser qualquer assinatura que esteja no mesmo inquilino do Azure Active Directory que o cofre de serviços de recuperação.
    - **Grupo de recursos**: o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as VMs do grupo de recursos selecionados estão listadas para proteção na próxima etapa.

3. Em **Máquinas** > Virtuais**Selecione máquinas virtuais,** selecione cada VM que deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, selecione **OK**.

4. Em **Configurações,** você pode configurar as seguintes configurações do site de destino.

    - **Localização do destino**: O local onde os dados da máquina virtual de origem serão replicados. A Recuperação do Site fornece uma lista de regiões de destino adequadas com base na localização da máquina selecionada. Recomendamos que você use o mesmo local que a localização do cofre do Recovery Services.
    - **Assinatura de destino**: A assinatura de destino usada para recuperação de desastres. Por padrão, a assinatura de destino será igual à assinatura de origem.
    - **Grupo de recursos de destino**: o grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas. Por padrão, a Recuperação do Site cria um novo grupo de recursos na região de destino. O nome fica com o sufixo "asr". Se um grupo de recursos já existe que foi criado pelo Azure Site Recovery, ele será reutilizado. Você também pode optar por personalizá-lo, como mostrado na seção a seguir. A localização do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região de destino. O nome fica com o sufixo "asr". É mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se sua VM de origem não usar discos gerenciados)**: Por padrão, o Site Recovery cria uma nova conta de armazenamento de destino imitando a configuração de armazenamento vm de origem. Se uma conta de armazenamento já existe, ela é reutilizada.
    - **Réplica de discos gerenciados (se sua VM de origem usar discos gerenciados)**: O Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem do mesmo tipo de armazenamento (padrão ou premium) que os discos gerenciados da VM de origem.
    - **Contas de armazenamento em cache**: A Recuperação do Site precisa de uma conta de armazenamento extra chamada armazenamento de *cache* na região de origem. Todas as alterações nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. Eles são então replicados para o local alvo.
    - **Conjunto de disponibilidade**: Por padrão, a Recuperação do Site cria um novo conjunto de disponibilidade na região-alvo. O nome tem o sufixo "asr". Se um conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
    - **Cofres de chave de criptografia de disco**: Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. Ele tem um sufixo "asr" baseado nas chaves de criptografia de disco VM de origem. Se um cofre-chave criado pelo Azure Site Recovery já existe, ele será reutilizado.
    - **Cofres de chaves de criptografia de chave**: Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo "asr" baseado nas chaves de criptografia da chave VM de origem. Se um cofre-chave criado pelo Azure Site Recovery já existir, ele será reutilizado.
    - **Política de replicação**: Define as configurações para o histórico de retenção de ponto de recuperação e a freqüência de instantâneo consistente com o aplicativo. Por padrão, o Site Recovery cria uma nova política de replicação com configurações padrão de *24 horas* para retenção de ponto de recuperação e *60 minutos* para freqüência de instantâneo consistente com o aplicativo.

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Siga essas etapas para modificar as configurações de destino padrão de recuperação do site.

1. Selecione **Personalizar** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de assinaturas que estão disponíveis no inquilino azure AD.

2. Selecione **Personalizar** ao lado de "Conjunto de recursos, configurações de rede, armazenamento e disponibilidade" para modificar as seguintes configurações padrão:
    - Para **o grupo de recursos Target,** selecione o grupo de recursos da lista de grupos de recursos no local de destino da assinatura.
    - Para **rede virtual Target,** selecione a rede a partir de uma lista de redes virtuais no local de destino.
    - Para **o conjunto Disponibilidade,** você pode adicionar configurações de configuração de disponibilidade à VM, se elas fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de armazenamento de**destino, selecione a conta a ser usada.

2. Selecione **Personalizar** ao lado de "Configurações de criptografia" para modificar as seguintes configurações padrão:
   - Para **o cofre de chaves de criptografia**de disco target , selecione o cofre de chaves de criptografia de disco de destino da lista de cofres-chave no local de destino da assinatura.
   - Para **o cofre de chaves de criptografia de destino**, selecione o cofre de chaves de criptografia de destino da lista de cofres-chave no local de destino da assinatura.

3. Selecione **Criar recurso de destino** > **Habilitar a replicação**.
4. Depois que as VMs estiverem habilitadas para replicação, você pode verificar o estado de saúde das VMs em **itens replicados**.

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para ser atualizado, sem progresso aparente. Clique **em Atualizar** para obter o status mais recente.

## <a name="update-target-vm-encryption-settings"></a>Atualizar configurações de criptografia da VM de destino
Nos seguintes cenários, você será obrigado a atualizar as configurações de criptografia VM de destino:
  - Você habilitou a replicação de recuperação de site na VM. Mais tarde, você habilitou a criptografia de disco na VM de origem.
  - Você habilitou a replicação de recuperação de site na VM. Mais tarde, você alterou a chave de criptografia de disco ou a chave de criptografia da fonte VM.

Você pode usar [um script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) para copiar as chaves de criptografia para a região de destino e, em seguida, atualizar as configurações de criptografia de destino no cofre > de **serviços de recuperação***replicado item* > **Properties** > **Compute e Network**.

![Atualizar janela de diálogo de configurações ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Solucionar problemas de permissão do cofre-chave durante a replicação do Azure-to-Azure VM

A recuperação do site do Azure requer pelo menos permissão de leitura no cofre de chaves da região de origem e permissão de gravação no cofre de chaves da região alvo para ler o segredo e copiá-lo para o cofre de chaves da região alvo. 

**Causa 1:** Você não tem permissão "GET" no **cofre de chaves da região de origem** para ler as chaves. </br>
**Como corrigir:** Independentemente de você ser um admin de assinatura ou não, é importante que você tenha permissão no cofre principal.

1. Vá para a região de origem Cofre de chaves que neste exemplo é "ContososourceKeyvault" > **políticas de acesso** 
2. Em **Select Principal** adicione seu nomedradmin@contoso.comde usuário, por exemplo: "
3. Em **Tecla permissões** selecione GET 
4. Sob **permissão secreta** selecione GET 
5. Salvar a política de acesso

**Causa 2:** Você não tem permissão necessária no **cofre da região alvo** para escrever as chaves. </br>

*Por exemplo:* Você tenta replicar uma VM que tenha o cofre-chave *ContososourceKeyvault* em uma região de origem.
Você tem todas as permissões no cofre da região de origem. Mas durante a proteção, você seleciona o cofre de chaves já criado ContosotargetKeyvault, que não tem permissões. Ocorre um erro.

Permissão necessária no [cofre de chaves alvo](#required-user-permissions)

**Como corrigir:** Vá para **Home** > **Keyvaults** > **ContosotargetKeyvault** > **Access políticas** e adicione as permissões apropriadas.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
