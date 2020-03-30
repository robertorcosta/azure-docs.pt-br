---
title: Montar um laboratório de Hacking Ético com a Azure Lab Services | Microsoft Docs
description: Aprenda a montar um laboratório usando o Azure Lab Services para ensinar hacking ético.
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133186"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Criar um laboratório para ensinar aula de hacking ético 
Este artigo mostra como montar uma classe que se concentre no lado forense do hacking ético. O teste de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta obter acesso ao sistema ou rede para demonstrar vulnerabilidades que um invasor mal-intencionado pode explorar. 

Em uma classe de hacking ético, os alunos podem aprender técnicas modernas para se defenderem de vulnerabilidades. Cada aluno recebe uma máquina virtual de host do Windows Server com duas máquinas virtuais aninhadas – uma com uma imagem do [Metasploitable3](https://github.com/rapid7/metasploitable3) e outra com uma imagem do [Kali Linux](https://www.kali.org/). A máquina virtual Metasploitable é usada para fins de exploração e a máquina virtual Kali fornece acesso às ferramentas necessárias para executar tarefas forenses.

Este artigo tem duas seções principais. A primeira seção abrange como criar o laboratório de sala de aula. A segunda seção abrange como criar a máquina de modelos com virtualização aninhada ativada e com as ferramentas e imagens necessárias. Neste caso, uma imagem metasploitável e uma imagem Kali Linux em uma máquina que tem Hyper-V habilitado para hospedar as imagens.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta existente. Veja o tutorial a seguir para criar uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplique as seguintes configurações:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- | 
| Média (Virtualização Aninhado) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Máquina de modelo 

Depois que a máquina de modelo for criada, inicie a máquina e conecte-se a ela para completar as três tarefas principais a seguir. 
 
1. Configure a máquina para virtualização aninhada. Ele permite que todos os recursos apropriados do Windows, como o Hyper-V, e configura a rede para que as imagens Hyper-V possam se comunicar entre si e com a internet.
2. Configure a imagem [Kali](https://www.kali.org/) Linux. Kali é uma distribuição Linux que inclui ferramentas para testes de penetração e auditoria de segurança.
3. Configure a imagem metasploitável. Para este exemplo, a imagem [Metasploitable3](https://github.com/rapid7/metasploitable3) será usada. Essa imagem é criada para ter vulnerabilidades de segurança propositadamente.

Um script que automatiza as tarefas descritas acima está disponível no [Lab Services Ethical Hacking Scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Prepare a máquina de modelos para virtualização aninhada
Siga as instruções [deste artigo](how-to-enable-nested-virtualization-template-vm.md) para preparar sua máquina virtual de modelo para virtualização aninhada. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configure uma máquina virtual aninhada com imagem Kali Linux
Kali é uma distribuição Linux que inclui ferramentas para testes de penetração e auditoria de segurança.

1. Baixar imagem [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)de .  
    1. Baixe o **Kali Linux Hyper-V 64 Bit** para Hyper-V.
    1. Extrair o arquivo .7z.  Se você ainda não tem 7 zip, baixe-o de [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html). Lembre-se da localização da pasta extraída como você vai precisar dela mais tarde.
2. Abra **o Gerenciador Hyper-V** de Ferramentas Administrativas.
1. Selecione **Ação**e selecione **Importar Máquina Virtual**. 
1. Na página **Localizar pasta** do assistente Importar **máquina virtual,** escolha a localização da pasta extraída que contém a imagem Kali Linux.

    ![Localizar caixa de diálogo pasta](../media/class-type-ethical-hacking/locate-folder.png)
1. Na página **Selecionar máquina virtual,** selecione a imagem Kali Linux.  Neste caso, a imagem é **kali-linux-2019.3-hyperv**.

    ![Selecione a imagem de Kali](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Na página **Escolher tipo de importação,** selecione **Copiar a máquina virtual (criar uma nova identificação exclusiva)**.

    ![Escolha o tipo de importação](../media/class-type-ethical-hacking/choose-import-type.png)
1. Aceite as configurações padrão em **Escolher pastas para arquivos de máquina virtual** e escolher pastas para armazenar páginas de discos **rígidos virtuais** e, em seguida, selecione **Next**.
1. Na página **Conectar rede,** escolha **LabServicesSwitch** criado anteriormente na seção **Preparar modelo para virtualização aninhada** deste artigo e, em seguida, selecione **Next**.

    ![Conectar página de rede](../media/class-type-ethical-hacking/connect-network.png)
1. Selecione **Concluir** na página **Resumo.** Aguarde até que as operações de cópia e importação sejam concluídas. A máquina virtual Kali Linux agora estará disponível no Hyper-V.
1. No **Hyper-V Manager,** escolha **Action** -> **Start**e escolha **Action** -> **Connect** para se conectar à máquina virtual.  
12. O nome de usuário padrão é `root` e a senha é `toor`. 

    > [!NOTE]
    > Se você precisar desbloquear a imagem, pressione a tecla CTRL e arraste o mouse para cima.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Configure uma VM aninhada com imagem metasploitável  
A imagem Rapid7 Metasploitable é uma imagem configurada propositalmente com vulnerabilidades de segurança. Você usará esta imagem para testar e encontrar problemas. As instruções a seguir mostram como usar uma imagem metasploitável pré-criada. No entanto, se uma versão mais recente da imagem [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)metasplociável for necessária, veja .

1. Navegue [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)até . Preencha o formulário para baixar a imagem e selecione o botão **Enviar.**
1. Selecione o botão **Baixar Metasploitable Now.**
1. Quando o arquivo zip for baixado, extraia o arquivo zip e lembre-se da localização.
1. Converta o arquivo vmdk extraído em um arquivo vhdx para que você possa usar com Hyper-V. Para isso, abra o PowerShell com privilégios administrativos e navegue até a pasta onde reside o arquivo vmdk e siga estas instruções:
    1. Baixe o [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)e execute mvmc_setup arquivo msi quando solicitado.
    1. Importe o módulo do PowerShell.  O local padrão em que o módulo está instalado é C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Converta o vmdk em um arquivo vhd que pode ser usado pelo Hyper-V. Essa operação pode demorar alguns minutos.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Copie o metástaseu recém-criado.vhdx para C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Crie uma nova máquina virtual Hyper-V.
    1. Abra **o Hyper-V Manager**.
    1. Escolha **ação** -> **nova** -> máquina**virtual**.
    1. Na página **Antes de começar,** o novo assistente de **máquina virtual,** clique **em Next**.
    1. Na **página Especificar nome e local,** **digite Metasploitable** para o **nome**e selecione **Next**.

        ![Novo assistente de imagem VM](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na **página Especificar geração,** aceite os padrões e selecione **Next**.
    1. Na página **Atribuir memória,** digite **512 MB** na **memória de inicialização**e selecione **Next**. 

        ![Atribuir uma página de memória](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na **página Configurar rede,** deixe a conexão como **Não Conectada**. Você configurará o adaptador de rede mais tarde.
    1. Na página **Conectar disco rígido virtual,** selecione **Usar um disco rígido virtual existente**. Navegue até o local para obter o arquivo **metasploitable.vhdx** criado na etapa anterior e selecione **Next**. 

        ![Conectar página de disco de rede virtual](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na **página Completar a nova** página assistente de máquina virtual e selecionar **Concluir**.
    1. Uma vez que a máquina virtual seja criada, selecione-a no Hyper-V Manager. Não ligue a máquina ainda.  
    1. Escolha**configurações de** **ação** -> .
    1. Na **caixa de diálogo Configurações para Metasploitable,** selecione **Adicionar hardware**. 
    1. Selecione **o adaptador de rede legado**e selecione **Adicionar**.

        ![Página do adaptador de rede](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na página **do Adaptador de rede legado,** selecione **LabServicesSwitch** para a configuração **do Switch Virtual** e selecione **OK**. LabServicesSwitch foi criado ao preparar a máquina de modelo para Hyper-V na seção **Preparar modelo para virtualização aninhada.**

        ![Página do adaptador legacy network](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A imagem metasplociável está agora pronta para uso. No **Hyper-V Manager,** escolha **Action** -> **Start**e escolha **Action** -> **Connect** para se conectar à máquina virtual.  O nome de usuário padrão é **msfadmin** e a senha é **msfadmin**. 


O modelo agora é atualizado e tem imagens necessárias para uma aula de teste de penetração ética de hackers, uma imagem com ferramentas para fazer o teste de penetração e outra imagem com vulnerabilidades de segurança para descobrir. A imagem do modelo agora pode ser publicada para a classe. Selecione o botão **Publicar** na página do modelo para publicar o modelo no laboratório.
  

## <a name="cost"></a>Custo  
Se você quiser estimar o custo deste laboratório, você pode usar o seguinte exemplo: 
 
Para uma turma de 25 alunos com 20 horas de aula programadas e 10 horas de cota para lição de casa ou tarefas, o preço para o laboratório seria: 

25 alunos * (20 + 10) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,50 USD. 

Para obter mais informações sobre preços, consulte Preços de [Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo te levou através das etapas para criar um laboratório para a aula de hackers éticos. Ele inclui etapas para configurar a virtualização aninhada para criar duas máquinas virtuais dentro da máquina virtual host para testes penetrantes.

## <a name="next-steps"></a>Próximas etapas
Os próximos passos são comuns à criação de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users). 

