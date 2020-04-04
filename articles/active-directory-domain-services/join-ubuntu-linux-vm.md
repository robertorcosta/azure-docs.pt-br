---
title: Inscreva-se em um Ubuntu VM para a Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e juntar uma máquina virtual do Ubuntu Linux a um domínio gerenciado do Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 95373ab8ff78c5bcb856e6d7e6d67d8525cd3f7e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655126"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Junte-se a uma máquina virtual do Ubuntu Linux a um domínio gerenciado do Azure AD Domain Services

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode juntar VMs a um domínio gerenciado pelo Azure Active Directory Domain Services (AD DS). Quando você junta uma VM a um domínio gerenciado pelo Azure AD DS, contas de usuário e credenciais do domínio podem ser usadas para fazer login e gerenciar servidores. As associações de grupos do domínio gerenciado pelo Azure AD DS também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como juntar uma VM Linux do Ubuntu a um domínio gerenciado pelo Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que faz parte do domínio gerenciado pelo Azure AD DS.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Crie e conecte-se a um Ubuntu Linux VM

Se você tiver um Ubuntu Linux VM existente no Azure, conecte-se a ele usando SSH e continue até a próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar um VM Linux Ubuntu ou quiser criar uma VM de teste para uso com este artigo, você pode usar um dos seguintes métodos:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Ao criar a VM, preste atenção às configurações da rede virtual para garantir que a VM possa se comunicar com o domínio gerenciado pelo Azure AD DS:

* Implante a VM na mesma, ou em uma rede virtual com peered, na qual você habilitou os Serviços de Domínio AD do Azure.
* Implante a VM em uma sub-rede diferente da instância azure AD Domain Services.

Uma vez que a VM seja implantada, siga as etapas para se conectar à VM usando SSH.

## <a name="configure-the-hosts-file"></a>Configurar o arquivo de hosts

Para ter certeza de que o nome do host VM está configurado corretamente para o domínio gerenciado, edite o arquivo */etc/hosts* e defina o nome do host:

```console
sudo vi /etc/hosts
```

No arquivo *hosts,* atualize o *endereço localhost.* No exemplo a seguir:

* *aaddscontoso.com* é o nome de domínio DNS do seu domínio Gerenciado pelo Azure AD DS.
* *ubuntu* é o nome de host da sua VM Ubuntu que você está juntando ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Quando terminar, salve *hosts* e saia `:wq` do arquivo hosts usando o comando do editor.

## <a name="install-required-packages"></a>Instalar os pacotes necessários

A VM precisa de alguns pacotes adicionais para se juntar à VM ao domínio gerenciado pelo Azure AD DS. Para instalar e configurar esses pacotes, atualize e instale as ferramentas de adesão ao domínio usando`apt-get`

Durante a instalação do Kerberos, o pacote *krb5-user* solicita o nome do reino em ALL UPPERCASE. Por exemplo, se o nome do seu domínio Gerenciado Pelo Azure AD DS for *aaddscontoso.com,* entre *em AADDSCONTOSO.COM* como o reino. A instalação `[realm]` grava `[domain_realm]` as seções e as seções no arquivo de configuração */etc/krb5.conf.* Certifique-se de especificar o reino de um ALL UPPERCASE:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Configurar o Protocolo de Tempo de Rede (NTP)

Para que a comunicação de domínio funcione corretamente, a data e a hora da VM do Ubuntu devem sincronizar com o domínio gerenciado pelo Azure AD DS. Adicione o nome de host NTP gerenciado pelo Azure AD DS ao arquivo */etc/ntp.conf.*

1. Abra o arquivo *ntp.conf* com um editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. No arquivo *ntp.conf,* crie uma linha para adicionar o nome DNS gerenciado do domínio Azure AD DS. No exemplo a seguir, uma entrada para *aaddscontoso.com* é adicionada. Use seu próprio nome DNS:

    ```console
    server aaddscontoso.com
    ```

    Quando terminar, salve e saia do arquivo `:wq` *ntp.conf* usando o comando do editor.

1. Para garantir que a VM esteja sincronizada com o domínio gerenciado pelo Azure AD DS, as seguintes etapas são necessárias:

    * Pare o servidor NTP
    * Atualize a data e a hora do domínio gerenciado
    * Inicie o serviço NTP

    Execute os seguintes comandos para completar essas etapas. Use seu próprio nome `ntpdate` DNS com o comando:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Inscisse a VM para o domínio gerenciado

Agora que os pacotes necessários estão instalados na VM e NTP está configurado, junte a VM ao domínio gerenciado pelo Azure AD DS.

1. Use `realm discover` o comando para descobrir o domínio gerenciado pelo Azure AD DS. O exemplo a seguir descobre o reino *AADDSCONTOSO.COM*. Especifique o nome de domínio gerenciado pelo Azure AD DS em ALL UPPERCASE:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se `realm discover` o comando não conseguir encontrar o domínio gerenciado pelo Azure AD DS, revise as seguintes etapas de solução de problemas:

    * Certifique-se de que o domínio é acessível a partir da VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é devolvida.
    * Verifique se a VM está implantada na mesma ou em uma rede virtual peered na qual o domínio gerenciado pelo Azure AD DS está disponível.
    * Confirme se as configurações do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerenciado pelo Azure AD DS.

1. Agora inicialize Kerberos `kinit` usando o comando. Especifique um usuário que faz parte do domínio gerenciado pelo Azure AD DS. Se necessário, [adicione uma conta de usuário a um grupo no Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Novamente, o nome de domínio gerenciado pelo Azure AD DS deve ser inserido em ALL UPPERCASE. No exemplo a seguir, `contosoadmin@aaddscontoso.com` a conta nomeada é usada para inicializar o Kerberos. Digite sua própria conta de usuário que faz parte do domínio gerenciado pelo Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Finalmente, junte a máquina ao domínio gerenciado pelo Azure AD DS usando o `realm join` comando. Use a mesma conta de usuário que faz parte do domínio gerenciado do Azure `kinit` AD `contosoadmin@AADDSCONTOSO.COM`DS que você especificou no comando anterior, como:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Leva alguns momentos para se juntar à VM ao domínio gerenciado pelo Azure AD DS. O exemplo a seguir mostra que a VM se juntou com sucesso ao domínio gerenciado pelo Azure AD DS:

```output
Successfully enrolled machine in realm
```

Se a VM não conseguir concluir com sucesso o processo de adesão ao domínio, certifique-se de que o grupo de segurança de rede da VM permita o tráfego kerberos de saída na porta TCP + UDP 464 para a sub-rede virtual para o domínio gerenciado pelo Azure AD DS.

## <a name="update-the-sssd-configuration"></a>Atualize a configuração do SSSD

Um dos pacotes instalados em uma etapa anterior foi para o System Security Services Daemon (SSSD). Quando um usuário tenta entrar em uma VM usando credenciais de domínio, o SSSD retransmite a solicitação a um provedor de autenticação. Neste cenário, o SSSD usa o Azure AD DS para autenticar a solicitação.

1. Abra o arquivo *sssd.conf* com um editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Comente a linha para *use_fully_qualified_names* da seguinte forma:

    ```console
    # use_fully_qualified_names = True
    ```

    Quando terminar, salve e saia do arquivo *sssd.conf* usando o `:wq` comando do editor.

1. Para aplicar a alteração, reinicie o serviço SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Configure as configurações da conta de usuário e do grupo

Com a VM juntada ao domínio gerenciado pelo Azure AD DS e configurada para autenticação, há algumas opções de configuração do usuário para concluir. Essas alterações de configuração incluem permitir autenticação baseada em senha e criar automaticamente diretórios domésticos na VM local quando os usuários de domínio fazem login pela primeira vez.

### <a name="allow-password-authentication-for-ssh"></a>Permitir autenticação de senha para SSH

Por padrão, os usuários só podem fazer login em uma VM usando autenticação baseada em chave pública SSH. Falha na autenticação baseada em senha. Quando você se junta à VM a um domínio gerenciado pelo Azure AD DS, essas contas de domínio precisam usar autenticação baseada em senha. Atualize a configuração SSH para permitir a autenticação baseada em senha da seguinte forma.

1. Abra o arquivo *sshd_conf* com um editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Atualize a linha de *autenticação de senha* para *sim*:

    ```console
    PasswordAuthentication yes
    ```

    Quando terminar, salve *sshd_conf* e saia `:wq` do arquivo sshd_conf usando o comando do editor.

1. Para aplicar as alterações e permitir que os usuários entrem usando uma senha, reinicie o serviço SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Configurar a criação automática do diretório base

Para habilitar a criação automática do diretório inicial quando um usuário entrar pela primeira vez, complete as seguintes etapas:

1. Abra o arquivo */etc/pam.d/sessão comum* em um editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Adicione a seguinte linha neste `session optional pam_sss.so`arquivo abaixo da linha:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Quando terminar, salve e saia do `:wq` arquivo de sessão *comum* usando o comando do editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder os privilégios sudo de grupo 'Administradores de controlador de domínio do AAD'

Para conceder aos membros do *grupo AAD DC Administrators* privilégios administrativos no Ubuntu VM, adicione uma entrada aos */etc/sudoers*. Uma vez adicionados, os membros do grupo `sudo` *Administradores AAD DC* podem usar o comando no Ubuntu VM.

1. Abra o arquivo *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao arquivo final de */etc/sudoers:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, salve e saia `Ctrl-X` do editor usando o comando.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Faça login na VM usando uma conta de domínio

Para verificar se a VM foi aderida com sucesso ao domínio gerenciado pelo Azure AD DS, inicie uma nova conexão SSH usando uma conta de usuário de domínio. Confirme se um diretório inicial foi criado e que a adesão ao grupo do domínio é aplicada.

1. Crie uma nova conexão SSH a partir do seu console. Use uma conta de domínio que pertence `ssh -l` ao domínio `contosoadmin@aaddscontoso.com` gerenciado usando o comando, como e, em seguida, digite o endereço de sua VM, como *ubuntu.aaddscontoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. Quando você estiver conectado com sucesso à VM, verifique se o diretório inicial foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no *diretório /home* com seu próprio diretório que corresponde à conta de usuário.

1. Agora verifique se as adesões do grupo estão sendo resolvidas corretamente:

    ```console
    id
    ```

    Você deve ver as adesões do grupo do domínio gerenciado pelo Azure AD DS.

1. Se você entrou na VM como membro do grupo *Administradores AAD DC,* verifique se você pode usar corretamente o `sudo` comando:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para conectar a VM ao domínio gerenciado pelo Azure AD DS ou fazer login com uma conta de domínio, consulte [Problemas de adesão ao domínio 'Solução de problemas'.](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
