---
title: Configure um laboratório de scripts de shell Linux com o Azure Lab Services | Microsoft Docs
description: Aprenda a montar um laboratório para ensinar scripting shell no Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443543"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Criar um laboratório para ensinar scripting shell no Linux
Este artigo mostra como montar um laboratório para ensinar scripts shell no Linux. O script é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de exemplo, a classe abrange scripts do Bash tradicionais e scripts avançados. Scripts avançados são scripts que combinam comandos do Bash e o Ruby. Essa abordagem permite que o Ruby passe os dados e que os comandos do Bash interajam com o shell. 

Os alunos que fazem essas aulas de script obtêm uma máquina virtual do Linux para aprender os conceitos básicos do Linux e também se familiarizarem com os scripts do shell Bash. A máquina virtual do Linux é fornecida com o acesso à Área de Trabalho Remota habilitado e com os editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta de laboratório existente. Veja o tutorial a seguir para criar uma nova conta de laboratório: [Tutorial to Setup a Lab Account](tutorial-setup-lab-account.md).

Depois de criar a conta de laboratório, habilite as seguintes configurações na conta do laboratório: 

| Configuração da conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens de mercado | Habilite a imagem [LTS do Ubuntu Server 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) para uso em sua conta de laboratório. Para obter mais informações, consulte [Especificar imagens do Marketplace disponíveis para os criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplicar as seguintes configurações:

| Configurações de laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da máquina virtual (VM) | Pequena  |
| Imagem vm | [Servidor Ubuntu 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Habilite a conexão remota da área de trabalho | Habilitar. <p>A ativação dessa configuração permitirá que professores e alunos se conectem às Suas VMs usando o RDP (Remote Desktop). Para obter mais informações, consulte [Habilitar desktop remoto para máquinas virtuais Linux em um laboratório no Azure Lab Services](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Instale desktop e xrdp
A imagem [LTS do Ubuntu Server 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) não tem servidor de desktop remoto instalado por padrão. Siga as instruções na Instalação e configure a [Área de Trabalho Remota para conectar a uma VM Linux no artigo do Azure](../../virtual-machines/linux/use-remote-desktop.md) para instalar os pacotes necessários na máquina de modelo para se conectar via protocolo remoto de desktop.

## <a name="install-ruby"></a>Instalar Ruby
Ruby é uma linguagem dinâmica de código aberto que pode ser combinada com scripts de bash. Esta seção mostra `apt-get` como usar para instalar a versão mais recente do [Ruby](https://www.ruby-lang.org/).

1. Instale atualizações executando os seguintes comandos:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Instale [o Ruby](https://www.ruby-lang.org/).  Ruby é uma linguagem dinâmica de código aberto que pode ser combinada com scripts de bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalar ferramentas de desenvolvimento
Esta seção mostra como instalar alguns editores de texto. Gedit é o editor de texto padrão para o ambiente de desktop gnome. Foi projetado como um editor de texto de uso geral. Visual Studio Code é um editor de texto que inclui suporte para depuração e integração de controle de origem.

> [!NOTE]
> Existem vários editores de texto diferentes disponíveis. Visual Studio Code e gedit são apenas dois exemplos.

1. Instale [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Instale [o Visual Studio Code](https://code.visualstudio.com/).  O código visual studio pode ser instalado usando a Snap Store.  Para opções alternativas de instalação, consulte [downloads alternativos do Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    O modelo agora é atualizado e tem tanto a linguagem de programação quanto as ferramentas de desenvolvimento necessárias para completar o laboratório. A imagem do modelo agora pode ser publicada no laboratório. Selecione o botão **Publicar** na página do modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo 
Se você quiser estimar o custo deste laboratório, você pode usar o seguinte exemplo:
 
Para uma turma de 25 alunos com 20 horas de aula programadas e 10 horas de cota para lição de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 20 Unidades de Laboratório * 0,01 USD por hora = 150 USD

Para obter mais informações sobre os preços pode ser encontrado no seguinte documento: [Preços de Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo te levou através das etapas para criar um laboratório para aulas de roteiro. Enquanto este artigo se concentrou na configuração de ferramentas de scripting Ruby na máquina Linux, a mesma configuração pode ser usada para outras classes de scripting como Python no Linux.

## <a name="next-steps"></a>Próximas etapas
Os próximos passos são comuns à criação de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users). 





