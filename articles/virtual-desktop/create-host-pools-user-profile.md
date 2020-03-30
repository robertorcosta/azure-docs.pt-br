---
title: Compartilhamento de contêiner de perfil do Windows Virtual Desktop FSLogix - Azure
description: Como configurar um contêiner de perfil FSLogix para um pool de hosts do Windows Virtual Desktop usando um compartilhamento de arquivos virtual baseado em máquina.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250914"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivo

O serviço de desktop virtual do Windows oferece os contêineres de perfil FSLogix como a solução recomendada de perfil de usuário. Não recomendamos o uso da solução USER Profile Disk (UPD), que será depreciada em versões futuras do Windows Virtual Desktop.

Este artigo lhe dirá como configurar um compartilhamento de contêiner de perfil FSLogix para um pool de hosts usando um compartilhamento de arquivos virtual baseado em máquina. Para obter mais documentação do FSLogix, consulte o [site fSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Se você estiver procurando material de comparação sobre as diferentes opções de armazenamento de contêiner de perfil FSLogix no Azure, consulte [opções de armazenamento para contêineres de perfil FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Crie uma nova máquina virtual que funcionará como um compartilhamento de arquivos

Ao criar a máquina virtual, certifique-se de colocá-la na mesma rede virtual que as máquinas virtuais do pool de host ou em uma rede virtual que tenha conectividade com as máquinas virtuais do pool de host. Você pode criar uma máquina virtual de várias maneiras:

- [Crie uma máquina virtual a partir de uma imagem da Azure Gallery](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Crie uma máquina virtual a partir de uma imagem gerenciada](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Crie uma máquina virtual a partir de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, junte-a ao domínio fazendo as seguintes coisas:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie o **Painel de Controle** e selecione **Sistema**.
3. Selecione **Nome do computador,** **selecione Alterar configurações**e, em seguida, selecione **Alterar...**
4. Selecione **Domínio** e, em seguida, insira o domínio Active Directory na rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para as máquinas de adesão ao domínio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Prepare a máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuários

A seguir, instruções gerais sobre como preparar uma máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuários:

1. Adicione os usuários do Windows Virtual Desktop Active Directory a um [grupo de segurança active directory](/windows/security/identity-protection/access-control/active-directory-security-groups/). Esse grupo de segurança será usado para autenticar os usuários do Windows Virtual Desktop à máquina virtual de compartilhamento de arquivos que você acabou de criar.
2. [Conecte-se à máquina virtual de compartilhamento de arquivos](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na máquina virtual de compartilhamento de arquivos, crie uma pasta na **unidade C** que será usada como compartilhamento de perfil.
4. Clique com o botão direito do mouse na nova pasta, selecione **Propriedades,** **selecione Compartilhar,** e selecione **Compartilhamento avançado...**.
5. Selecione **Compartilhar esta pasta,** selecionar **Permissões...** e, em seguida, **selecione Adicionar...**.
6. Pesquise o grupo de segurança ao qual você adicionou os usuários do Windows Virtual Desktop e certifique-se de que o grupo tenha **controle total**.
7. Depois de adicionar o grupo de segurança, clique com o botão direito do mouse na pasta, selecione **Propriedades,** **selecione Compartilhar**e copie o **Caminho de rede** para usar posteriormente.

Para obter mais informações sobre permissões, consulte a [documentação fSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Configure o recipiente de perfil FSLogix

Para configurar as máquinas virtuais com o software FSLogix, faça o seguinte em cada máquina registrada no pool de host:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Inicie um navegador de internet e navegue [até este link](https://go.microsoft.com/fwlink/?linkid=2084562) para baixar o agente FSLogix.
3. Navegue \\ \\até a\\versão \\ \\win32 ou x64\\no arquivo .zip e execute **fSLogixAppsSetup** para instalar o agente FSLogix.  Para saber mais sobre como instalar o FSLogix, consulte [Baixar e instalar o FSLogix](/fslogix/install-ht/).
4. Navegue até **os aplicativos** > **FSLogix** > do programa**para** confirmar o agente instalado.
5. No menu inicial, execute **RegEdit** como administrador. Navegue até **o software\\\\\\de HKEY_LOCAL_MACHINE de computador FSLogix**.
6. Crie uma chave chamada **Perfis**.
7. Crie os seguintes valores para a chave Perfis:

| Nome                | Type               | Dados/Valor                        |
|---------------------|--------------------|-----------------------------------|
| habilitado             | DWORD              | 1                                 |
| VHDLocalização        | Valor de cadeia de caracteres múltipla | "Caminho de rede para compartilhamento de arquivos"     |

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../security-center/security-center-just-in-time.md).
