---
title: Início Rápido – Criar uma VM Linux no portal do Azure
description: Neste início rápido, você aprende a usar o portal do Azure para criar uma máquina virtual Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6bf9a89a4806db53797191336578ef9148886181
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81759227"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Início Rápido: Criar uma máquina virtual do Linux no portal do Azure

As máquinas virtuais (VM) do Azure podem ser criadas por meio do Portal do Azure. O portal do Azure é uma interface de usuário baseada em navegador para criar recursos do Azure. Este início rápido mostra como usar o portal do Azure para implantar uma máquina virtual (VM) Linux que executa o Ubuntu 18.04 LTS. Para ver a VM em ação, você também habilita o SSH na VM e instala o servidor Web do NGINX.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chaves SSH, você pode ignorar esta etapa.

Abra um shell bash e use [ssh-keygen](https://www.ssh.com/ssh/keygen/) para criar um par de chaves SSH. Se você não tiver um shell bash no seu computador local, você pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).


1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu na parte superior da página, escolha o ícone `>_` para abrir o Cloud Shell.
1. Verifique se o CloudShell diz **Bash** no canto superior esquerdo. Se ele disser PowerShell, use a lista suspensa para selecionar **Bash** e selecione **Confirmar** para alternar para o shell Bash.
1. Digite `ssh-keygen -t rsa -b 2048` para criar a chave SSH. 
1. Será solicitado que você insira um arquivo no qual salvar o par de chaves. Basta pressionar **Enter** para salvar no local padrão, listado entre colchetes. 
1. Será solicitado que você insira uma frase secreta. Você pode digitar uma frase secreta para a chave SSH ou pressionar **Enter** para continuar sem uma frase secreta.
1. O comando `ssh-keygen` gera as chaves públicas e privadas com o nome padrão do `id_rsa` no `~/.ssh directory`. O comando retorna o caminho completo para a chave pública. Use o caminho para a chave pública para exibir seu conteúdo com `cat` digitando `cat ~/.ssh/id_rsa.pub`.
1. Copie a saída desse comando e salve-a em algum lugar para usá-la posteriormente neste artigo. Essa é sua chave pública e você precisará dela ao configurar sua conta de administrador para fazer logon em sua VM.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com), se você ainda não fez isso.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Digite **máquinas virtuais** na pesquisa.
1. Em **Serviços**, selecione **Máquinas virtuais**.
1. Na página **Máquinas Virtuais**, selecione **Adicionar**. A página **Criar uma máquina virtual** é aberta.
1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, escolha **Criar** grupo de recursos. Digite *myResourceGroup* no nome*. 

    ![Criar um grupo de recursos para sua VM](./media/quick-create-portal/project-details.png)

1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual**, escolha *Leste dos EUA* para **Região** e escolha *Ubuntu 18.04 LTS* para sua **Imagem**. Deixe os outros padrões.

    ![Seção de detalhes da instância](./media/quick-create-portal/instance-details.png)

1. Em **Conta de administrador**, selecione **Chave pública SSH**, digite seu nome de usuário e, em seguida, cole a chave pública. Remova os espaços em branco à esquerda ou à direita em sua chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. Em **Regras de porta de entrada** > **Portas de entrada públicas**, escolha **Permitir portas selecionadas** e, em seguida, selecione **SSH (22)** e **HTTP (80)** na lista suspensa. 

    ![Abrir portas para RDP e HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.

1. Na página **Criar uma máquina virtual**, você pode ver os detalhes sobre a VM que você está prestes a criar. Quando estiver pronto, selecione **Criar**.

Levará alguns minutos para que sua VM seja implantada. Quando a implantação for concluída, vá para a próxima seção.

    
## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Crie uma conexão SSH com a VM.

1. Selecione o botão **Conectar** na página visão geral da sua VM. 

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Na página **Conectar à máquina virtual**, mantenha as opções padrão para conectar-se com o endereço IP na porta 22. Em **Logon usando a conta local de VM** um comando de conexão é mostrado. Selecione o botão para copiar o comando. O exemplo a seguir mostra a aparência do comando de conexão SSH:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Usando o mesmo shell bash que você usou para criar seu par de chaves SSH (você pode reabrir o Cloud Shell selecionando `>_` novamente ou indo para `https://shell.azure.com/bash`), cole o comando de conexão SSH no shell para criar uma sessão SSH.

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do NGINX. Na sua sessão de SSH, atualize suas fontes de pacote e, em seguida, instale o pacote mais recente do NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Quando terminar, digite `exit` para sair da sessão SSH.


## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Use um navegador da Web de sua escolha para exibir a página inicial padrão do NGINX. Digite o endereço IP público da VM como o endereço Web. O endereço IP público pode ser encontrado na página de visão geral de VM ou como parte da cadeia de conexão SSH usada anteriormente.

![Site padrão NGINX](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione **Excluir**, em seguida, confirme o nome do grupo de recursos para excluir.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, criou um Grupo de Segurança de Rede e uma regra e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)
