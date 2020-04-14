---
title: Criar o contêiner de perfil FSLogix Azure Files Active Directory Domain Services - Azure
description: Este artigo descreve como criar um contêiner de perfil FSLogix com arquivos Azure e serviços de domínio do diretório ativo do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265766"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Crie um contêiner de perfil FSLogix com arquivos Azure

Este artigo mostrará como criar um contêiner de perfil FSLogix com arquivos Azure e Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já configurou uma instância Azure AD DS. Se você ainda não tiver um, siga as instruções em [Criar um domínio gerenciado básico](../active-directory-domain-services/tutorial-create-instance.md) primeiro e depois retorne aqui.

## <a name="add-azure-ad-ds-admins"></a>Adicionar administradores Azure AD DS

Para adicionar admins adicionais, você cria um novo usuário e concede-lhes permissões.

Para adicionar um admin:

1. Selecione O Diretório Ativo do **Azure** na barra lateral e selecione **Todos os usuários**e selecione Novo **usuário**.

2.  Digite os detalhes do usuário nos campos.

3. No painel diretório ativo do Azure no lado esquerdo da tela, selecione **Grupos**.

4. Selecione o **grupo Administradores AAD DC.**

5. No painel esquerdo, selecione **Membros**e selecione **Adicionar membros** no painel principal. Isso mostrará uma lista de todos os usuários disponíveis no Azure AD. Selecione o nome do perfil de usuário que você acabou de criar.

## <a name="set-up-an-azure-storage-account"></a>Configure uma conta de armazenamento do Azure

Agora é hora de ativar a autenticação do Azure AD DS sobre o SMB (Server Message Block, bloco de mensagens do servidor). 

Para habilitar a autenticação:

1. Se você ainda não tiver, configure e implante uma conta de armazenamento v2 Azure de uso geral seguindo as instruções em [Criar uma conta de armazenamento Azure](../storage/common/storage-account-create.md).

2. Depois de terminar de configurar sua conta, selecione **Ir para recurso**.

3. Selecione **Configuração** no painel do lado esquerdo da tela e, em seguida, habilite a **autenticação do Diretório Ativo do Azure para arquivos Azure** no painel principal. Quando terminar, selecione **Salvar**.

4. Selecione **Visão geral** no painel no lado esquerdo da tela e, em seguida, selecione **Arquivos** no painel principal.

5. Selecione **'Compartilhar arquivo'** e digite o **Nome** e a **Cota** nos campos que aparecem no lado direito da tela.

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Outros usuários precisarão de permissões de acesso para acessar seu compartilhamento de arquivos. Para fazer isso, você precisará atribuir a cada usuário uma função com as permissões de acesso apropriadas.

Para atribuir permissões de acesso aos usuários:

1. A partir do portal Azure, abra o compartilhamento de arquivos criado em [Configurar uma conta do Azure Storage](#set-up-an-azure-storage-account).

2. Selecione **controle de acesso (IAM)**.

3. Selecione **Adicionar uma atribuição de função**.

4. Na guia **'Adicionar função',** selecione a função incorporada apropriada na lista de papéis. Você precisará pelo menos selecionar **O Contribuinte de Compartilhamento de Dados de Arquivos de Armazenamento SMB** para obter permissões adequadas.

5. Para **atribuir acesso a,** selecione **usuário, grupo ou principal de serviço do Azure Active Directory**.

6. Selecione um nome ou endereço de e-mail para a identidade target Azure Active Directory.

7. Clique em **Salvar**.

## <a name="get-the-storage-account-access-key"></a>Obtenha a chave de acesso da conta de armazenamento

Em seguida, você precisará obter a chave de acesso para sua conta de armazenamento.

Para obter a chave de acesso da conta de armazenamento:

1. Na barra lateral do portal Azure, selecione **Contas de armazenamento**.

2. Na lista de contas de armazenamento, selecione a conta para a qual você habilitou o Azure AD DS e criou as funções personalizadas nas etapas acima.

3. Em **Configurações,** selecione **Teclas de acesso** e copie a chave da **chave1**.

4. Vá para a guia **Máquinas Virtuais** e localize qualquer VM que se torne parte do seu pool de host.

5. Selecione o nome da máquina virtual (VM) em **Máquinas Virtuais (adVM)** e selecione **Conectar**

    Isso irá baixar um arquivo RDP que permitirá que você faça login na VM com suas próprias credenciais.

    ![Uma captura de tela da guia RDP da janela Conectar à máquina virtual.](media/rdp-tab.png)

6. Quando você tiver feito o assinatura na VM, execute um prompt de comando como administrador.

7. Execute o comando a seguir:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Substitua por `<desired-drive-letter>` uma letra de unidade `y:`de sua escolha (por exemplo, ).
    - Substitua todas `<storage-account-name>` as instâncias com o nome da conta de armazenamento especificada anteriormente.
    - Substitua pelo `<share-name>` nome do compartilhamento que você criou anteriormente.
    - Substitua pela `<storage-account-key>` chave da conta de armazenamento do Azure.

    Por exemplo:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Execute o seguinte comando para conceder ao usuário acesso total ao compartilhamento de arquivos do Azure.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Substitua pela `<mounted-drive-letter>` letra da unidade que deseja que o usuário use.
    - Substitua pelo `<user-email>` UPN do usuário que usará esse perfil para acessar as VMs do host de sessão.

    Por exemplo:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Criar um contêiner de perfil

Agora que seus perfis estão prontos, vamos criar um contêiner de perfil FSLogix.

Para configurar um contêiner de perfil FSLogix:

1. Faça login no VM de host de sessão configurado no início deste artigo e, em seguida, [baixe e instale o agente FSLogix](/fslogix/install-ht/).

2. Descompacte o arquivo do agente FSLogix que você baixou e vá para **x64** > **Releases,** em seguida, abra **FSLogixAppsSetup.exe**.

3. Uma vez que o instalador seja lançado, selecione **Eu concordo com os termos e condições da licença.** Se aplicável, forneça uma nova chave.

4. Selecione **Instalar**.

5. Abra **a unidade C,** depois vá para os aplicativos**FSLogix** >  **de arquivos** > do programa**para** ter certeza de que o agente FSLogix foi instalado corretamente.

     >[!NOTE]
     > Se houver várias VMs no pool de host, você precisará repetir as etapas 1 a 5 para cada VM.

6. Execute **O Editor de Registro** (RegEdit) como administrador.

7. Navegue até o**software** > **de HKEY_LOCAL_MACHINE** >  **de computador** > **FSLogix,** clique com o botão direito do mouse no **FSLogix,** selecione **Novo**e selecione **Teca**.

8. Crie uma nova chave chamada **Perfis**.

9.  Clique com o botão direito do mouse em **Perfis,** selecione **Novo**e selecione **DWORD (32 bits).** Nomeie o valor **Ativado** e defina o valor **de Dados** como **1**.

    ![Uma captura de tela da tecla Profiles. O arquivo REG_DWORD é destacado e seu valor de Dados é definido como 1.](media/dword-value.png)

10. Clique com o botão direito do mouse em **Perfis,** selecione **Novo**e selecione **Valor de Várias Cordas**. Nomeie o valor **VHDLocations** e defina o `\\fsprofile.file.core.windows.net\share` URI para o compartilhamento De arquivos Azure como o valor de Dados.

    ![Uma captura de tela da tecla Profiles mostrando o arquivo VHDLocations. Seu valor de dados mostra o URI para o compartilhamento de arquivos do Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Atribuir usuários a um host de sessão

Agora você precisará atribuir usuários ao seu host de sessão.

Para atribuir aos usuários:

1. Execute o Windows PowerShell como administrador e execute o cmdlet a seguir para fazer login no Windows Virtual Desktop com o PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Quando solicitado para credenciais, digite o mesmo usuário que foi concedido a função TenantCreator, RDS Owner ou RDS Contributor no inquilino do Windows Virtual Desktop.

2. Execute os cmdlets a seguir para atribuir o usuário ao grupo de desktop remoto:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Como os cmdlets anteriores, `<your-wvd-tenant>` `<wvd-pool>`certifique-se de substituir , e `<user-principal>` com os valores relevantes.

    Por exemplo:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Certifique-se de que seu perfil funciona

Agora tudo o que você tem que fazer é ter certeza de que o perfil que você criou existe e funciona como planejado.

Para verificar seu perfil:

1. Abra um navegador e vá para [o cliente web do Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

2. Faça login com a conta de usuário atribuída ao grupo De desktop remoto.

3. Uma vez estabelecida a sessão de usuário, abra o portal do Azure e faça login com uma conta administrativa.

4. Na barra lateral, selecione **Contas de armazenamento**.

5. Selecione a conta de armazenamento configurada como o compartilhamento de arquivos para o pool de host de sessão e habilitado com o Azure AD DS.

6. Selecione o ícone **Arquivos** e expanda seu compartilhamento.

    Se tudo estiver configurado corretamente, você deve ver um **Diretório** com um `<user SID>-<username>`nome formatado assim: .

## <a name="next-steps"></a>Próximas etapas

Se você está procurando maneiras alternativas de criar contêineres de perfil FSLogix, confira os seguintes artigos:

- [Crie um recipiente de perfil para um pool de host usando um compartilhamento de arquivos](create-host-pools-user-profile.md).
- [Crie um contêiner de perfil FSLogix para um pool de hosts usando arquivos do Azure NetApp](create-fslogix-profile-container.md)

Você pode encontrar informações mais detalhadas sobre conceitos relacionados a contêineres FSlogix para arquivos Azure em [contêineres de perfil FSLogix e arquivos Azure](fslogix-containers-azure-files.md).
