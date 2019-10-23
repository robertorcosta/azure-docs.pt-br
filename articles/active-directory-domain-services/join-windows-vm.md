---
title: Adicionar uma VM do Windows Server a um domínio gerenciado | Microsoft Docs
description: Neste tutorial, saiba como ingressar uma máquina virtual do Windows Server em um domínio gerenciado do Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: iainfou
ms.openlocfilehash: 00e717202116cf9a48c2c2d889374d451b8e4d45
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754368"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Tutorial: Ingressar uma máquina virtual do Windows Server em um domínio gerenciado

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Com um domínio gerenciado do Azure AD DS, você pode fornecer recursos de ingresso no domínio e gerenciamento para VMs (máquinas virtuais) no Azure. Este tutorial mostra como criar uma VM do Windows Server e, em seguida, ingressá-la em um domínio gerenciado do Azure AD DS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM do Windows Server
> * Conectar a VM do Windows Server a uma rede virtual do Azure
> * Ingressar a VM no domínio gerenciado do Azure AD DS

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.
    * Verifique se a sincronização de hash de senha ou a redefinição de senha self-service do Azure AD Connect foram realizadas de modo que a conta possa entrar no domínio gerenciado do Azure AD DS.

Se você já tiver uma VM que quer ingressar em um domínio, pule para a seção de [ingressar a VM no domínio gerenciado do Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você criará uma VM do Windows Server e a ingressará no domínio gerenciado do Azure AD DS usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Criar uma máquina virtual do Windows Server

Para ver como ingressar um computador em um domínio gerenciado do Azure AD DS, crie uma VM do Windows Server. Essa VM está conectada a uma rede virtual do Azure que fornece conectividade ao domínio gerenciado do Azure AD DS. O processo de ingressar em um domínio gerenciado do Azure AD DS é igual a ingressar em um domínio local normal do Active Directory Domain Services.

Se você já tiver uma VM que quer ingressar em um domínio, pule para a seção de [ingressar a VM no domínio gerenciado do Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. No canto superior esquerdo do portal do Azure, selecione **+ Criar um recurso**.
2. Em **Começar**, escolha **Windows Server 2016 Datacenter**.

    ![Escolher criar uma VM do Windows Server 2016 Datacenter no portal do Azure](./media/join-windows-vm/select-vm-image.png)

3. Na janela **Básico**, defina as principais configurações da máquina virtual. Deixe as configurações padrão de *Opções de disponibilidade*, *Imagem* e *Tamanho*.

    | Parâmetro            | Valor sugerido   |
    |----------------------|-------------------|
    | Resource group       | Selecione ou crie um grupo de recursos, como *myResourceGroup* |
    | Nome da máquina virtual | Insira um nome para a VM, como *myVM* |
    | Região               | Escolha uma região para criar sua VM, como *Leste dos EUA* |
    | Nome de Usuário             | Insira um nome de usuário para a conta de administrador local para criar na VM, como *azureuser* |
    | Senha             | Insira e confirme uma senha segura para o administrador local criar na VM. Não especifique as credenciais da conta de usuário de um domínio. |

4. Por padrão, as VMs criadas no Azure não podem ser acessadas pela Internet. Essa configuração ajuda a aumentar a segurança da VM e reduz a área de possíveis ataques. Na próxima etapa deste tutorial, será necessário conectar-se à VM usando o protocolo RDP e, em seguida, ingressar o Windows Server no domínio gerenciado do Azure AD DS.

    Quando o protocolo RDP estiver habilitado, haverá a possibilidade de ocorrer ataques automatizados de logon, o que pode desabilitar contas com nomes comuns, como *admin* ou *administrador* devido a várias tentativas seguidas de logon. O protocolo RDP deve ser habilitado somente quando solicitado e limitado a um conjunto de intervalos de IP autorizados. [Acesso à VM Just In Time do Azure][jit-access], como parte da Central de Segurança do Azure, pode habilitar essas sessões de RDP curtas e restritas. Também é possível [criar e usar um host do Azure Bastion (atualmente em versão prévia)][azure-bastion] para permitir acesso somente por meio do portal do Azure via SSL.

    Neste tutorial, habilite manualmente as conexões RDP com a VM.

    Em **Portas de entrada públicas**, selecione a opção para **Permitir portas selecionadas**. No menu suspenso **Selecionar portas de entrada**, escolha *RDP*.

5. Quando terminar, selecione **Avançar: Discos**.
6. No menu suspenso de **Tipo de disco de SO**, escolha *SSD Standard* e **Avançar: Rede**.
7. A VM precisa se conectar a uma sub-rede da rede virtual do Azure que possa se comunicar com a sub-rede em que o domínio gerenciado do Azure AD DS foi implantado. É recomendável que um domínio gerenciado do Azure AD DS seja implantado em uma sub-rede própria e dedicada. Não implante a VM na mesma sub-rede do domínio gerenciado do Azure AD DS.

    Existem duas maneiras principais de implantar a VM e conectar-se a uma sub-rede apropriada da rede virtual:
    
    * Criar ou selecionar uma sub-rede existente na mesma rede virtual em que o domínio gerenciado do Azure AD DS está implantado.
    * Selecionar uma sub-rede em uma rede virtual do Azure que esteja conectada a ela usando o [emparelhamento de rede virtual do Azure][vnet-peering].
    
    Se você selecionar uma sub-rede de rede virtual que não esteja conectada à sub-rede da instância do Azure AD DS, não poderá ingressar a VM no domínio gerenciado. Neste tutorial, vamos criar uma nova sub-rede na rede virtual do Azure.

    No painel **Rede**, selecione a rede virtual em que o domínio gerenciado do Azure AD DS está implantado, como *myVnet*
8. Neste exemplo, a sub-rede *DomainServices* existente mostra que o domínio gerenciado do Azure AD DS está conectado. Não conecte sua VM a essa sub-rede. Para criar uma sub-rede para a VM, selecione **Gerenciar configuração de sub-rede**.

    ![Escolher gerenciar a configuração de sub-rede no portal do Azure](./media/join-windows-vm/manage-subnet.png)

9. Selecione **+ Sub-rede** e digite um nome para ela, como *ManagedVMs*. Forneça um **Intervalo de endereços (bloco CIDR)** , como *10.1.1.0/24*. Verifique se esse intervalo de endereço IP não se sobrepõe a quaisquer outros intervalos de endereços locais ou do Azure. Deixe as outras opções com os valores padrão e clique em **OK**.

    ![Criar uma configuração de sub-rede no portal do Azure](./media/join-windows-vm/create-subnet.png)

10. A sub-rede demora alguns segundos para ser criada. Após a criação, clique no *X* para fechar a janela da sub-rede.
11. Volte ao painel **Rede** para criar uma VM e escolha a sub-rede que você criou no menu suspenso, como *ManagedVMs*. Novamente, verifique se você escolheu a sub-rede correta e não implante a VM na mesma sub-rede do domínio gerenciado do Azure AD DS.
12. Deixe as outras opções com os valores padrão e clique em **Gerenciamento**.
13. Defina **Diagnóstico de inicialização** como *Desativado*. Deixe as outras opções com os valores padrão e clique em **Revisar + criar**.
14. Revise as configurações da VM e selecione **Criar**.

São necessários alguns minutos para criar a VM. O portal do Azure mostra o status da implantação. Quando a VM estiver pronta, selecione **Ir para o recurso**.

![Vá para o recurso da VM no portal do Azure após a criação bem-sucedida](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Conectar-se à VM do Windows Server

Agora, vamos nos conectar à VM do Windows Server recém-criada usando o RDP e ingressar o domínio gerenciado do Azure AD DS. Use as credenciais do administrador local especificadas quando a VM foi criada na etapa anterior, caso não existam credenciais de domínio.

1. No painel **Visão geral**, selecione **Conectar**.

    ![Conectar-se à uma máquina virtual do Windows no portal do Azure](./media/join-windows-vm/connect-to-vm.png)

1. Selecione a opção para *Baixar o arquivo RDP*. Salve esse arquivo RDP no navegador da Web.
1. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
1. Insira as credenciais do administrador local digitadas na etapa anterior para criar a VM, como *localhost\azureuser*
1. Se você vir um aviso de certificado durante o processo de entrada, selecione **Sim** ou **Continuar** para se conectar.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Ingressar a VM no domínio gerenciado do Azure AD DS

Com a VM criada e a conexão RDP estabelecida, agora vamos ingressar a máquina virtual do Windows Server no domínio gerenciado do Azure AD DS. É o mesmo processo quando um computador se conecta a um domínio local normal do Active Directory Domain Services.

1. O **Gerenciador do Servidor** deve abrir por padrão ao entrar na VM. Caso contrário, no menu **Iniciar**, selecione **Gerenciador do Servidor**.
1. No painel esquerdo da janela **Gerenciador do Servidor**, selecione **Servidor Local**. Em **Propriedades**, no painel direito, escolha **Grupo de trabalho**.

    ![Abrir o Gerenciador do Servidor na VM e editar a propriedade do grupo de trabalho](./media/join-windows-vm/server-manager.png)

1. Na janela **Propriedades do sistema**, selecione **Alterar** para ingressar o domínio gerenciado do Azure AD DS.

    ![Escolher alterar as propriedades do grupo de trabalho ou do domínio](./media/join-windows-vm/change-domain.png)

1. Na caixa **Domínio**, especifique o nome do domínio gerenciado do Azure AD DS, como *contoso.com*, e clique em **OK**.

    ![Especificar o domínio gerenciado do Azure AD DS a ser ingressado](./media/join-windows-vm/join-domain.png)

1. Insira as credenciais do domínio para ingressá-lo. Use as credenciais de um usuário que pertença ao grupo *Administradores do Azure AD DC*. Somente os membros desse grupo têm privilégios para ingressar computadores no domínio gerenciado do Azure AD DS. As credenciais da conta podem ser especificadas em uma das seguintes maneiras:

    * **Formato UPN** (recomendado): insira o sufixo do nome UPN da conta de usuário, conforme configurado no Azure AD. Por exemplo, o sufixo UPN do usuário *contosoadmin* seria `contosoadmin@contoso.onmicrosoft.com`. Há alguns casos de uso comuns em que o formato UPN pode ser usado de forma confiável para entrar no domínio em vez do formato *SAMAccountName*:
        * Se o prefixo de UPN de um usuário for longo, por exemplo, *deehasareallylongname*, o *SAMAccountName* poderá ser gerado automaticamente.
        * Se vários usuários tiverem o mesmo prefixo UPN no locatário do Azure AD, por exemplo, *dee*, o formato *SAMAccountName* poderá ser gerado automaticamente.
    * **Formato SAMAccountName:** insira o nome da conta no formato *SAMAccountName*. Por exemplo, o *SAMAccountName* do usuário *contosoadmin* seria `CONTOSO\contosoadmin`.

1. São necessários alguns segundos para ingressar no domínio gerenciado do Azure AD DS. Quando terminar, a seguinte mensagem lhe dará as boas-vindas ao domínio:

    ![Bem-vindo ao domínio](./media/join-windows-vm/join-domain-successful.png)

    Selecione **OK** para continuar.

1. Para concluir o processo de ingresso no domínio gerenciado do Azure AD DS, reinicie a VM.

> [!TIP]
> É possível ingressar uma VM no domínio usando o PowerShell com o cmdlet [Add-Computer][add-computer]. O exemplo a seguir ingressa o domínio *CONTOSO* e, em seguida, reinicia a VM. Quando solicitado, insira as credenciais de um usuário que pertença ao grupo de *administradores do Azure AD DC*:
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Para ingressar uma VM no domínio sem se conectar a ela e configurar a conexão manualmente, você também pode explorar o uso do cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] do Azure PowerShell.

Após reiniciar a VM do Windows Server, todas as políticas aplicadas ao domínio gerenciado do Azure AD DS serão enviadas por push à VM. Agora, também é possível entrar na VM do Windows Server usando as credenciais de domínio corretas.

## <a name="clean-up-resources"></a>Limpar recursos

No próximo tutorial, você usará essa VM do Windows Server para instalar as ferramentas de gerenciamento que permitem administrar o domínio gerenciado do Azure AD DS. Se você não quiser continuar nesta série de tutoriais, revise as seguintes etapas de limpeza para [desabilitar o RDP](#disable-rdp) ou [excluir a VM](#delete-the-vm). Caso contrário, [prossiga para o próximo tutorial](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Desfazer o ingresso da VM no domínio gerenciado do Azure AD DS

Para remover a VM do domínio gerenciado do Azure AD DS, siga novamente as etapas para [ingressar a VM em um domínio](#join-the-vm-to-the-azure-ad-ds-managed-domain). Em vez de ingressar no domínio gerenciado do Azure AD DS, escolha ingressar em um grupo de trabalho, como o *WORKGROUP* padrão. Depois que a VM for reinicializada, o objeto de computador será removido do domínio gerenciado do Azure AD DS.

Se você [excluir a VM](#delete-the-vm) sem deixar o domínio, um objeto de computador órfão será deixado no Azure AD DS.

### <a name="disable-rdp"></a>Desabilitar o RDP

Se você continuar a usar a VM do Windows Server criada neste tutorial para executar seus aplicativos e suas cargas de trabalho, lembre-se de que o RDP foi aberto na Internet. Para aumentar a segurança e reduzir o risco de ataque, o RDP precisa ser desabilitado na Internet. Para desabilitar o RDP para a VM do Windows Server pela Internet, conclua as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**
1. Escolha seu grupo de recursos, por exemplo, *myResourceGroup*.
1. Escolha sua VM, por exemplo, *myVM*, e selecione *Rede*.
1. Nas **Regras de segurança de rede de entrada** do grupo de segurança de rede, selecione a regra que permite o RDP e escolha **Excluir**. Levam alguns segundos para remover a regra de segurança de entrada.

### <a name="delete-the-vm"></a>Excluir a VM

Se você não for usar essa VM do Windows Server, exclua-a seguindo estas etapas:

1. No menu esquerdo, selecione **Grupos de recursos**
1. Escolha seu grupo de recursos, por exemplo, *myResourceGroup*.
1. Escolha sua VM, por exemplo, *myVM*, e selecione **Excluir**. Selecione **Sim** para confirmar a exclusão do recurso. São necessários alguns minutos para excluir a VM.
1. Quando a VM for excluída, selecione o disco do sistema operacional, o adaptador de rede e quaisquer outros recursos com o prefixo *myVM-* e exclua-os.

## <a name="troubleshoot-domain-join-issues"></a>Solucionar problemas de ingresso no domínio

A VM do Windows Server deve ser ingressada com êxito no domínio gerenciado do Azure AD DS, do mesmo modo que um computador local normal ingressaria em um domínio do Active Directory Domain Services. Se a VM do Windows Server não puder ingressar no domínio gerenciado do Azure AD DS, isso indicará que há um problema de credencial ou de conectividade. Revise as seguintes seções de solução de problemas para ingressar o domínio gerenciado com êxito.

### <a name="connectivity-issues"></a>Problemas de conectividade

Se você não receber uma solicitação de credenciais para ingressar no domínio, haverá um problema de conectividade. A VM não pode alcançar o domínio gerenciado do Azure AD DS na rede virtual.

Após tentar cada uma dessas etapas de solução de problemas, tente ingressar a VM do Windows Server no domínio gerenciado novamente.

* Verifique se a VM está conectada à mesma rede virtual em que o Azure AD DS está habilitado ou se há uma conexão de rede emparelhada.
* Tente executar ping do nome de domínio DNS do domínio gerenciado, como `ping contoso.com`.
    * Se a solicitação de ping falhar, tente executar ping dos endereços IP do domínio gerenciado, por exemplo, `ping 10.0.0.4`. O endereço IP do ambiente é exibido na página *Propriedades* quando você seleciona o domínio gerenciado do Azure AD DS na lista de recursos do Azure.
    * Se você conseguir executar o ping do endereço IP, mas não do domínio, o DNS poderá estar configurado incorretamente. Verifique se os endereços IP do domínio gerenciado estão configurados como servidores DNS para a rede virtual.
* Tente liberar o cache do resolvedor DNS na máquina virtual usando o comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemas de credenciais

Se você receber uma solicitação de credenciais para ingressar no domínio, mas em seguida um erro após inserir essas credenciais, a VM será capaz de se conectar ao domínio gerenciado do Azure AD DS. As credenciais fornecidas não permitem que a VM ingresse no domínio gerenciado do Azure AD DS.

Após tentar cada uma dessas etapas de solução de problemas, tente ingressar a VM do Windows Server no domínio gerenciado novamente.

* Certifique-se de que a conta de usuário especificada pertença ao grupo *Administradores do AAD DC*.
* Tente usar o formato UPN para especificar as credenciais, como `contosoadmin@contoso.onmicrosoft.com`. Se houver vários usuários com o mesmo prefixo UPN no seu locatário ou se o prefixo UPN for muito longo, o *SAMAccountName* da sua conta poderá ser gerado automaticamente. Nesses casos, o formato de *SAMAccountName* da sua conta pode ser diferente do que você espera ou usa em seu domínio local.
* Verifique se você [habilitou a sincronização de senhas][password-sync] para o domínio gerenciado. Sem esta etapa de configuração, os hashes de senha exigidos não estarão presentes no domínio gerenciado do Azure AD DS para autenticar corretamente sua tentativa de logon.
* Aguarde a conclusão da sincronização de senha. Quando a senha de uma conta de usuário é alterada, uma sincronização automática em segundo plano do Azure AD a atualiza no Azure AD DS. Leva algum tempo para que a senha fique disponível para ser usada para ingresso no domínio.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma VM do Windows Server
> * Conectar a VM do Windows Server a uma rede virtual do Azure
> * Ingressar a VM no domínio gerenciado do Azure AD DS

Para administrar o domínio gerenciado do Azure AD DS, configure uma VM de gerenciamento usando o ADAC (Centro Administrativo do Active Directory).

> [!div class="nextstepaction"]
> [Instalar ferramentas de administração em uma VM de gerenciamento](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
