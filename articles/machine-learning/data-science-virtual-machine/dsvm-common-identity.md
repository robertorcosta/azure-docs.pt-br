---
title: Criar uma identidade comum
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar contas de usuário comuns que podem ser usadas em várias Máquinas Virtuais de Ciência de Dados. Você pode usar o Azure Active Directory ou um Active Directory local para autenticar usuários na Máquina Virtual de Ciência de Dados.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208152"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Configure uma identidade comum em uma Máquina Virtual de Data Science

Em uma máquina virtual do Microsoft Azure (VM), incluindo uma Máquina Virtual de Data Science (DSVM), você cria contas de usuário locais enquanto fornece a VM. Os usuários então se autenticam na VM usando essas credenciais. Se você tem várias VMs que seus usuários precisam acessar, o gerenciamento de credenciais pode ficar muito complicado. Uma excelente solução é implantar contas e gerenciamento de usuários comuns através de um provedor de identidade baseado em padrões. Através desta abordagem, você pode usar um único conjunto de credenciais para acessar vários recursos no Azure, incluindo vários DSVMs.

O Active Directory é um provedor de identidade popular e é suportado no Azure tanto como um serviço de nuvem quanto como um diretório local. Você pode usar o Azure AD (Azure Active Directory) ou o Active Directory local para autenticar usuários em uma DSVM autônoma ou em um cluster de DSVMs em um conjunto de dimensionamento de máquina virtual do Azure. Para isso, integre as instâncias de DSVM em um domínio do Active Directory.

Se você já tem o Active Directory, você pode usá-lo como seu provedor de identidade comum. Se você não tiver o Active Directory, você pode executar uma instância de diretório ativo gerenciado no Azure através [do Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

A documentação [do Azure AD](https://docs.microsoft.com/azure/active-directory/) fornece [instruções detalhadas de gerenciamento,](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)incluindo orientação sobre como conectar o Azure AD ao seu diretório local, se você tiver um.

Este artigo descreve como configurar um serviço de domínio active directory totalmente gerenciado no Azure usando o Azure AD DS. Em seguida, você pode juntar seus DSVMs ao domínio do Active Directory gerenciado. Essa abordagem permite que os usuários acessem um pool de DSVMs (e outros recursos do Azure) através de uma conta de usuário comum e credenciais.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurar um domínio do Active Directory totalmente gerenciado no Azure

O Azure AD DS simplifica o gerenciamento de identidades, fornecendo um serviço totalmente gerenciado no Azure. Nesse domínio do Active Directory, você pode gerenciar usuários e grupos. Para configurar um domínio do Active Directory e contas de usuário do Active Directory hospedados no Azure em seu diretório, siga estas etapas:

1. No portal do Azure, adicione o usuário ao Active Directory: 

   1. Faça login no centro de administração do [Azure Active Directory](https://aad.portal.azure.com) usando uma conta que é um administrador global para o diretório.
    
   1. Selecione **Azure Active Directory** e, em seguida, **Usuários e grupos**.
    
   1. Em **Usuários e grupos,** selecione **Todos os usuários**e selecione Novo **usuário**.
   
           The **User** pane opens:
      
      ![O painel "Usuário"](./media/add-user.png)
    
   1. Insira os detalhes do usuário, como **Nome** e **Nome de usuário**. A parte de nome de domínio do nome de usuário deve ser o nome de domínio padrão inicial "[nome de domínio].onmicrosoft.com" ou um [nome de domínio personalizado](../../active-directory/add-custom-domain.md) verificado e não federado, como "contoso.com".
    
   1. Copie ou anote a senha de usuário gerada para que você possa fornecê-la ao usuário depois que esse processo estiver concluído.
    
   1. Opcionalmente, você pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função do diretório** para o usuário. 
    
   1. Em **Usuário,** selecione **Criar**.
    
   1. Distribua com segurança a senha gerada para o novo usuário para que ele possa fazer login.

1. Crie uma instância do Azure AD DS. Siga as instruções em Ativar serviços de [domínio do diretório ativo do Azure usando o portal Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (a seção "Criar uma instância e configurar configurações básicas"). É importante atualizar as senhas de usuários existentes no Active Directory para que a senha do Azure AD DS fique sincronizada. Também é importante adicionar DNS ao Azure AD DS, conforme descrito em "Complete os campos na janela Basics do portal Azure para criar uma instância Azure AD DS" nessa seção.

1. Crie uma sub-rede DSVM separada na rede virtual criada na seção "Criar e configurar a rede virtual" da etapa anterior.
1. Crie uma ou mais instâncias De DSVM na sub-rede DSVM.
1. Siga as [instruções](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para adicionar o DSVM ao Active Directory. 
1. Monte um compartilhamento de arquivos Azure para hospedar seu diretório de casa ou notebook para que seu espaço de trabalho possa ser montado em qualquer máquina. (Se você precisar de permissões apertadas no nível de arquivo, você precisará de um sistema de arquivos de rede [NFS] em execução em uma ou mais VMs.)

   1. [Criar um compartilhamento de arquivos do Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Monte esta ação no Linux DSVM. Quando você seleciona **Conectar** para o compartilhamento de arquivos do Azure em sua conta de armazenamento no portal Azure, o comando para executar no shell bash no DSVM Linux aparece. O comando se parece com o seguinte:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Por exemplo, suponha que você montou seu compartilhamento de Arquivos Azure em /data/espaço de trabalho. Agora, crie diretórios para cada um de seus usuários no compartilhamento: /data/workspace/user1, /data/workspace/user2, e assim por diante. Criar um diretório `notebooks` no workspace de cada usuário. 
1. Criar links simbólicos para `notebooks` em `$HOME/userx/notebooks/remote`.   

Agora você tem os usuários na instância do Active Directory hospedados no Azure. Usando credenciais do Active Directory, os usuários podem fazer login em qualquer DSVM (SSH ou JupyterHub) que se junte ao Azure AD DS. Como o workspace do usuário está em um compartilhamento de Arquivos do Azure, os usuários têm acesso a seus notebooks e outros trabalhos de qualquer DSVM quando eles usam o JupyterHub.

Para o dimensionamento automático, você pode usar um conjunto de dimensionamento de máquinas virtuais para criar um pool de VMs que ingressaram no domínio dessa maneira e com o disco compartilhado montado. Os usuários podem fazer login em qualquer máquina disponível no conjunto de escalas da máquina virtual e ter acesso ao disco compartilhado onde seus notebooks são salvos. 

## <a name="next-steps"></a>Próximas etapas

* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)



