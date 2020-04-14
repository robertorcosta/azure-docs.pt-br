---
title: Contêineres de perfil FSLogix NetApp Windows Virtual Desktop - Azure
description: Como criar um contêiner de perfil FSLogix usando arquivos Do Azure NetApp no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 149fb5818ee360c7333997655ea9eb8d7ded346c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270886"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Crie um contêiner de perfil FSLogix para um pool de hosts usando arquivos do Azure NetApp

Recomendamos o uso de contêineres de perfil FSLogix como uma solução de perfil de usuário para o [serviço Windows Virtual Desktop](overview.md). Os contêineres de perfil FSLogix armazenam um perfil de usuário completo em um único contêiner e são projetados para vagar perfis em ambientes de computação remota não persistentes, como o Windows Virtual Desktop. Quando você faz login, o contêiner se conecta dinamicamente ao ambiente de computação usando um disco rígido virtual (VHD) compatível localmente (VHDX) e um disco rígido virtual Hyper-V (VHDX). Essas tecnologias avançadas de driver de filtro permitem que o perfil do usuário esteja disponível imediatamente e apareça no sistema exatamente como um perfil de usuário local. Para saber mais sobre os contêineres de perfil FSLogix, consulte [os contêineres de perfil FSLogix e os arquivos Azure](fslogix-containers-azure-files.md).

Você pode criar contêineres de perfil FSLogix usando [o Azure NetApp Files](https://azure.microsoft.com/services/netapp/), um serviço de plataforma nativa do Azure fácil de usar que ajuda os clientes a fornecer de forma rápida e confiável volumes de SMB de nível corporativo para seus ambientes de Desktop Virtual windows. Para saber mais sobre os arquivos do Azure NetApp, veja [o que é o Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

Este guia mostrará como configurar uma conta azure NetApp Files e criar contêineres de perfil FSLogix no Windows Virtual Desktop.

Este artigo pressupõe que você já tenha [pools de host](create-host-pools-azure-marketplace.md) configurados e agrupados em um ou mais inquilinos em seu ambiente de Desktop Virtual do Windows. Para saber como configurar inquilinos, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md) e no nosso blog da Tech [Community](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

As instruções neste guia são especificamente para usuários do Windows Virtual Desktop. Se você está procurando por orientações mais gerais sobre como configurar arquivos do Azure NetApp e criar contêineres de perfil FSLogix fora do Windows Virtual Desktop, consulte o [Configure Arquivos Do NetApp do Azure e crie um quickstart de volume NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Este artigo não abrange as melhores práticas para garantir o acesso ao compartilhamento de Arquivos do Azure NetApp.

>[!NOTE]
>Se você estiver procurando material de comparação sobre as diferentes opções de armazenamento de contêiner de perfil FSLogix no Azure, consulte [opções de armazenamento para contêineres de perfil FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um contêiner de perfil FSLogix para um pool de host, você deve:

- Configurar e configurar o Windows Virtual Desktop
- Provisione um pool de hosts do Windows Virtual Desktop
- [Habilite sua assinatura de Arquivos Do Azure NetApp](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Configure sua conta Azure NetApp Files

Para começar, você precisa configurar uma conta do Azure NetApp Files.

1. Entre no [portal do Azure](https://portal.azure.com). Certifique-se de que sua conta tenha permissões de contribuinte ou administrador.

2. Selecione o **ícone Azure Cloud Shell** à direita da barra de pesquisa para abrir o Azure Cloud Shell.

3. Uma vez que o Azure Cloud Shell esteja aberto, selecione **PowerShell**.

4. Se esta é a sua primeira vez usando o Azure Cloud Shell, crie uma conta de armazenamento na mesma assinatura que você mantém seus Arquivos Azure NetApp e Windows Virtual Desktop.

   ![A janela da conta de armazenamento com o botão criar armazenamento na parte inferior da janela destacada em vermelho.](media/create-storage-button.png)

5. Uma vez que o Azure Cloud Shell carregue, execute os dois cmdlets seguintes.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. No lado esquerdo da janela, selecione **Todos os serviços**. Digite **Arquivos Azure NetApp** na caixa de pesquisa que aparece na parte superior do menu.

   ![Uma captura de tela de um usuário inserindo "Azure NetApp Files" na caixa de pesquisa All services. Os resultados da pesquisa mostram o recurso Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Selecione **Arquivos do Azure NetApp** nos resultados da pesquisa e selecione **Criar**.

8. Selecione o botão **Adicionar.**
9. Quando a nova guia **de conta do NetApp** for aberta, digite os seguintes valores:

    - Para **Nome,** digite o nome da sua conta netapp.
    - Para **Assinatura**, selecione a assinatura da conta de armazenamento configurada na etapa 4 a partir do menu suspenso.
    - Para **o grupo Resource,** selecione um grupo de recursos existente no menu suspenso ou crie um novo selecionando **Criar novo**.
    - Para **Localização,** selecione a região para sua conta NetApp no menu suspenso. Esta região deve ser a mesma região que suas VMs de hospedagem de sessão.

   >[!NOTE]
   >Atualmente, o Azure NetApp Files não suporta a montagem de um volume entre as regiões.

10. Quando terminar, selecione **Criar** para criar sua conta do NetApp.

## <a name="create-a-capacity-pool"></a>Criar um pool de capacidade

Em seguida, crie um novo pool de capacidade: 

1. Vá para o menu Azure NetApp Files e selecione sua nova conta.
2. No menu da conta, selecione **Pools de capacidade** no serviço Armazenamento.
3. Selecione **Adicionar pool**.
4. Quando a **guia Do pool de capacidade nova** for aberta, digite os seguintes valores:

    - Para **Nome,** digite um nome para o novo pool de capacidade.
    - Para **nível de serviço,** selecione o valor desejado no menu suspenso. Recomendamos **premium** para a maioria dos ambientes.
       >[!NOTE]
       >A configuração Premium fornece o rendimento mínimo disponível para um nível de Serviço Premium, que é de 256 MBps. Você pode precisar ajustar esse throughput para um ambiente de produção. O throughput final é baseado na relação descrita nos [limites de Throughput](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Para **Tamanho (TiB),** digite o tamanho da piscina de capacidade que melhor se adapte às suas necessidades. O tamanho mínimo é 4 TiB.

5. Quando tiver terminado, selecione **OK**.

## <a name="join-an-active-directory-connection"></a>Participe de uma conexão Active Directory

Depois disso, você precisa participar de uma conexão Active Directory.

1. Selecione **conexões de diretório ativo** no menu no lado esquerdo da página e selecione o botão **'Participar'** para abrir a página **'Participar diretório ativo'.**

   ![Uma captura de tela do menu De conexões do Diretório Ativo.](media/active-directory-connections-menu.png)

2. Digite os seguintes valores na página **Participar do Diretório Ativo** para participar de uma conexão:

    - Para **DNS primário,** digite o endereço IP do servidor DNS em seu ambiente que pode resolver o nome de domínio.
    - Para **domínio,** digite seu nome de domínio totalmente qualificado (FQDN).
    - Para **prefixo SMB Server (Conta do Computador),** digite a seqüência desejando anexar ao nome da conta do computador.
    - Para **Nome de Usuário,** digite o nome da conta com permissões para realizar a adesão ao domínio.
    - Para **Senha,** digite a senha da conta.

## <a name="create-a-new-volume"></a>Criar um novo volume

Em seguida, você precisará criar um novo volume.

1. Selecione **Volumes**e selecione **Adicionar volume**.

2. Quando a **guia Criar uma** guia de volume for aberta, digite os seguintes valores:

    - Para **nome de volume,** digite um nome para o novo volume.
    - Para **pool de capacidade,** selecione o pool de capacidade que você criou no menu suspenso.
    - Para **Cota (GiB),** digite o tamanho de volume apropriado para o seu ambiente.
    - Para **rede Virtual,** selecione uma rede virtual existente que tenha conectividade com o controlador de domínio a partir do menu suspenso.
    - Em **Subnet,** selecione **Criar novo**. Tenha em mente que esta sub-rede será delegada aos Arquivos do Azure NetApp.

3.  Selecione **Next: Protocolo \> ** para abrir a guia Protocolo e configurar seus parâmetros de acesso ao volume.

## <a name="configure-volume-access-parameters"></a>Configurar parâmetros de acesso ao volume

Depois de criar o volume, configure os parâmetros de acesso ao volume.

1.  Selecione **SMB** como o tipo de protocolo.
2.  Em Configuração no menu suspenso **do Active Directory,** selecione o mesmo diretório que você inseriu originalmente em [Participar de uma conexão Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Tenha em mente que há um limite de um Diretório Ativo por assinatura.
3.  Na caixa de texto **Nome compartilhar,** digite o nome da ação usada pelo pool de host de sessão e seus usuários.

4.  Selecione **'Revisar + criar'** na parte inferior da página. Isso abre a página de validação. Depois que seu volume for validado com sucesso, selecione **Criar**.

5.  Neste ponto, o novo volume começará a ser implantado. Uma vez que a implantação esteja concluída, você pode usar o compartilhamento De arquivos do Azure NetApp.

6.  Para ver o caminho de montagem, **selecione Ir para recurso** e procurá-lo na guia Visão geral.

    ![Uma captura de tela da tela Visão Geral com uma seta vermelha apontando para o caminho de montagem.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configure o FSLogix em máquinas virtuais de host de sessão (VMs)

Esta seção é baseada em [Criar um recipiente de perfil para um pool de host usando um compartilhamento de arquivos](create-host-pools-user-profile.md).

1. [Baixe o arquivo FSLogix .zip](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) enquanto você ainda está remotano VM do host de sessão.

2. Descompacte o arquivo baixado.

3. No arquivo, vá para **x64** > **Releases** e execute **FSLogixAppsSetup.exe**. O menu de instalação será aberto.

4.  Se você tiver uma chave de produto, digite-a na caixa de texto Da chave do produto.

5. Selecione a caixa de seleção ao lado **de Eu concordo com os termos e condições da licença**.

6. Selecione **Instalar**.

7. Navegue até **C:\\Arquivos\\\\do programa Aplicativos FSLogix** para confirmar o agente instalado.

8. No menu Iniciar, execute **RegEdit** como administrador.

9. Navegue até **o software\\\\\\de HKEY_LOCAL_MACHINE de computador FSLogix**.

10. Crie uma chave chamada **Perfis**.

11.  Crie um valor chamado **Ativado** com **um** REG_DWORD tipo definido como um valor de dados de **1**.

12. Crie um valor chamado **VHDLocations** com um tipo **de Várias Cordas** e defina seu valor de dados para o URI para o compartilhamento de arquivos do Azure NetApp.

13. Crie um valor chamado **DeleteLocalProfileWhenVHDShouldApply** com um valor DWORD de 1 para evitar problemas com perfis locais existentes antes de fazer login.

     >[!WARNING]
     >Tenha cuidado ao criar o valor DeleteLocalProfileWhenVHDShouldApply. Quando o sistema FSLogix Profiles determina que um usuário deve ter um perfil FSLogix, mas um perfil local já existe, o Profile Container excluirá permanentemente o perfil local. Em seguida, o usuário será conectado com o novo perfil FSLogix.

## <a name="assign-users-to-session-host"></a>Atribuir usuários ao host de sessão

1. Abra **o PowerShell ISE** como administrador e faça login no Windows Virtual Desktop.

2. Execute os seguintes cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Quando solicitado para credenciais, digite as credenciais para o usuário com as funções de Criador de Inquilino ou Dono rdS/RDS Contribuinte no inquilino do Windows Virtual Desktop.

4. Execute os cmdlets a seguir para atribuir um usuário a um grupo de desktop remoto:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Certifique-se de que os usuários podem acessar o compartilhamento de arquivos do Azure NetApp

1. Abra seu navegador de <https://rdweb.wvd.microsoft.com/webclient/index.html>internet e vá para .

2. Faça login com as credenciais de um usuário atribuído ao grupo De desktop remoto.

3. Depois de estabelecer a sessão de usuário, faça login no portal do Azure com uma conta administrativa.

4. Abra **os arquivos do Azure NetApp,** selecione sua conta Azure NetApp Files e, em seguida, selecione **Volumes**. Assim que o menu Volumes for aberto, selecione o volume correspondente.

   ![Uma captura de tela da conta do NetApp que você configurou anteriormente no portal Azure com o botão Volumes selecionado.](media/netapp-account.png)

5. Vá para a guia **Visão Geral** e confirme se o contêiner de perfil FSLogix está usando espaço.

6. Conecte-se diretamente a qualquer parte VM do pool de host usando o Remote Desktop e abra o **File Explorer.** Em seguida, navegue até o **caminho de** \\ \\montagem\\(no exemplo a seguir, o caminho de montagem é anf-SMB-3863.gt1107.onmicrosoft.com anf-VOL).

   Dentro desta pasta, deve haver um perfil VHD (ou VHDX) como o do exemplo a seguir.

   ![Uma captura de tela do conteúdo da pasta no caminho de montagem. Dentro está um único arquivo VHD chamado "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Próximas etapas

Você pode usar os recipientes de perfil do FSLogix para configurar um compartilhamento de perfil de usuário. Para saber como criar compartilhamentos de perfil de usuário com seus novos contêineres, consulte [Criar um contêiner de perfil para um pool de host usando um compartilhamento de arquivos](create-host-pools-user-profile.md).
