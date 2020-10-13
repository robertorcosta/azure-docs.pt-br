---
title: Configurar um laboratório de Ethical Hacking com o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório usando Azure Lab Services para ensinar ao Ethical Hacking.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ae0d57223edb68d1bed4ad64a005dd33da019dd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631674"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Configurar um laboratório para ensinar a classe de Ethical Hacking 
Este artigo mostra como configurar uma classe que se concentra no lado da perícia de Ethical Hacking. O teste de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta obter acesso ao sistema ou rede para demonstrar vulnerabilidades que um invasor mal-intencionado pode explorar. 

Em uma classe de hacking ético, os alunos podem aprender técnicas modernas para se defenderem de vulnerabilidades. Cada aluno recebe uma máquina virtual de host do Windows Server com duas máquinas virtuais aninhadas – uma com uma imagem do [Metasploitable3](https://github.com/rapid7/metasploitable3) e outra com uma imagem do [Kali Linux](https://www.kali.org/). A máquina virtual Metasploitable é usada para fins de exploração e a máquina virtual Kali fornece acesso às ferramentas necessárias para executar tarefas forenses.

Este artigo tem duas seções principais. A primeira seção aborda como criar o laboratório de sala de aula. A segunda seção aborda como criar o computador de modelo com a virtualização aninhada habilitada e com as ferramentas e imagens necessárias. Nesse caso, uma imagem Metasploitável e uma imagem do Kali Linux em um computador com o Hyper-V habilitado para hospedar as imagens.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta existente. Consulte o tutorial a seguir para criar uma nova conta de laboratório: [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplicar as seguintes configurações:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- | 
| Médio (virtualização aninhada) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Computador de modelo 

Após a criação do computador de modelo, inicie o computador e conecte-o para concluir as três principais tarefas a seguir. 
 
1. Configure o computador para virtualização aninhada. Ele habilita todos os recursos do Windows apropriados, como o Hyper-V, e configura a rede para que as imagens do Hyper-V possam se comunicar entre si e com a Internet.
2. Configure a imagem do [Kali](https://www.kali.org/) Linux. O Kali é uma distribuição do Linux que inclui ferramentas para teste de penetração e auditoria de segurança.
3. Configure a imagem de Metasploit. Para este exemplo, a imagem [Metasploitable3](https://github.com/rapid7/metasploitable3) será usada. Essa imagem é criada para ter vulnerabilidades de segurança intencionalmente.

Um script que automatiza as tarefas descritas acima está disponível em [scripts de Ethical Hacking dos serviços de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparar computador de modelo para virtualização aninhada
Siga as instruções neste [artigo](how-to-enable-nested-virtualization-template-vm.md) para preparar sua máquina virtual de modelo para a virtualização aninhada. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurar uma máquina virtual aninhada com a imagem do Kali Linux
O Kali é uma distribuição do Linux que inclui ferramentas para teste de penetração e auditoria de segurança.

1. Baixar imagem de [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) .  
    1. Baixe o **Kali Linux Hyper-v 64 bit** para Hyper-v.
    1. Extraia o arquivo. 7z.  Se você ainda não tiver 7 zip, baixe-o de [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Lembre-se do local da pasta extraída, pois você precisará dela mais tarde.
2. Abra o **Gerenciador do Hyper-V** em ferramentas administrativas.
1. Selecione **ação**e, em seguida, selecione **importar máquina virtual**. 
1. Na página **Localizar pasta** do assistente para **importar máquina virtual** , escolha o local da pasta extraída que contém a imagem do Kali Linux.

    ![Caixa de diálogo Localizar pasta](./media/class-type-ethical-hacking/locate-folder.png)
1. Na página **selecionar máquina virtual** , selecione a imagem do Kali Linux.  Nesse caso, a imagem é **Kali-Linux-2019,3-HyperV**.

    ![Selecionar imagem Kali](./media/class-type-ethical-hacking/select-kali-image.png)
1.  Na página **escolher tipo de importação** , selecione **copiar a máquina virtual (criar uma nova ID exclusiva)**.

    ![Escolher tipo de importação](./media/class-type-ethical-hacking/choose-import-type.png)
1. Aceite as configurações padrão em **escolher pastas para arquivos de máquina virtual** e **escolha pastas para armazenar as páginas de discos rígidos virtuais** e, em seguida, selecione **Avançar**.
1. Na página **conectar rede** , escolha **LabServicesSwitch** criado anteriormente na seção **preparar modelo para virtualização aninhada** deste artigo e, em seguida, selecione **Avançar**.

    ![Página conectar rede](./media/class-type-ethical-hacking/connect-network.png)
1. Selecione **concluir** na página **Resumo** . Aguarde até que as operações de cópia e importação sejam concluídas. A máquina virtual Kali Linux agora estará disponível no Hyper-V.
1. No **Gerenciador do Hyper-V**, escolha início da **ação**  ->  **Start**e escolha **ação**  ->  **conectar** para conectar-se à máquina virtual.  
12. O nome de usuário padrão é `root` e a senha é `toor`. 

    > [!NOTE]
    > Se você precisar desbloquear a imagem, pressione a tecla CTRL e arraste o mouse para cima.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Configurar uma VM aninhada com imagem Metasploitável  
A imagem Rapid7 Metasploitable é uma imagem configurada propositadamente com vulnerabilidades de segurança. Você usará essa imagem para testar e encontrar problemas. As instruções a seguir mostram como usar uma imagem do Metasploitable criada previamente. No entanto, se uma versão mais nova da imagem Metasploitável for necessária, consulte [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Baixe a imagem Metasploitável.
    1. Navegue até [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html). Preencha o formulário para baixar a imagem e selecione o botão **Enviar** .
    2. Selecione o botão **baixar agora** .
    3. Quando o arquivo zip for baixado, extraia o arquivo zip e lembre-se do local do arquivo. vmdk que é Metasploitável.
1. Converta o arquivo VMDK extraído em um arquivo vhdx para que você possa usar o arquivo vhdx com o Hyper-V. Há várias ferramentas disponíveis para converter imagens do VMware em imagens do Hyper-V e vice-versa.  Vamos usar o [conversor STARWIND V2V](https://www.starwindsoftware.com/starwind-v2v-converter).  Para baixar, consulte a [página de download do conversor do STARWIND V2V](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Inicie o **conversor STARWIND V2V**.
    1. Na página **selecionar local da imagem a ser convertida** , escolha **arquivo local**.  Selecione **Avançar**.
    1. Na página **imagem de origem** , navegue até e selecione o arquivo. vmdk que você extraiu na etapa anterior para a configuração **nome** .  Selecione **Avançar**.
    1. Na **imagem selecionar local da destino**, escolha **arquivo local**.  Selecione **Avançar**.
    1. Na página **selecionar formato de imagem de destino** , escolha **VHD/VHDX**.  Selecione **Avançar**.
    1. Na página **selecionar opção para formato de imagem VHD/VHDX** , escolha **imagem de aumento de vhdx**.  Selecione **Avançar**.
    1. Na página **selecionar nome do arquivo de destino** , aceite o nome de arquivo padrão.  Selecione **Converter**.
    1. Na página **conversão** , aguarde até que a imagem seja convertida.  Isso pode levar alguns minutos.  Selecione **concluir** quando a conversão for concluída.
1. Crie uma nova máquina virtual do Hyper-V.
    1. Abra o **Gerenciador do Hyper-V**.
    1. Escolha **ação**  ->  **nova**  ->  **máquina virtual**.
    1. Na página **antes de começar** do assistente de **nova máquina virtual**, selecione **Avançar**.
    1. Na página **especificar nome e local** , digite **Metasploitable** para o **nome**e selecione **Avançar**.

        ![Assistente de nova imagem de VM](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na página **especificar geração** , aceite os padrões e selecione **Avançar**.
    1. Na página **atribuir memória** , insira **512 MB** para a **memória de inicialização**e selecione **Avançar**. 

        ![Atribuir uma página de memória](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na página **Configurar rede** , deixe a conexão como **não conectada**. Você configurará o adaptador de rede mais tarde.
    1. Na página **conectar disco rígido virtual** , selecione **usar um disco rígido virtual existente**. Navegue até o local do arquivo **. vhdx** que é possível criar na etapa anterior e selecione **Avançar**. 

        ![Página conectar disco de rede virtual](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na página **concluindo o assistente de nova máquina virtual** e selecione **concluir**.
    1. Depois que a máquina virtual for criada, selecione-a no Gerenciador do Hyper-V. Não ative o computador ainda.  
    1. Escolha **Action**  ->  **configurações**de ação.
    1. Na caixa de diálogo **configurações para o Metasploitable** para, selecione **adicionar hardware**. 
    1. Selecione **adaptador de rede herdado**e selecione **Adicionar**.

        ![Página adaptador de rede](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na página **adaptador de rede herdado** , selecione **LabServicesSwitch** para a configuração do **comutador virtual** e selecione **OK**. LabServicesSwitch foi criado ao preparar o computador de modelo para o Hyper-V na seção **preparar modelo para virtualização aninhada** .

        ![Página adaptador de rede herdada](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A imagem Metasploitable agora está pronta para uso. No **Gerenciador do Hyper-V**, escolha início da **ação**  ->  **Start**e escolha **ação**  ->  **conectar** para conectar-se à máquina virtual.  O nome de usuário padrão é **msfadmin** e a senha é **msfadmin**. 


O modelo agora está atualizado e tem imagens necessárias para uma classe de teste de penetração de Ethical Hacking, uma imagem com ferramentas para fazer o teste de penetração e outra imagem com vulnerabilidades de segurança para descobrir. A imagem de modelo agora pode ser publicada na classe. Selecione o botão **Publicar** na página de modelo para publicar o modelo no laboratório.
  

## <a name="cost"></a>Custo  
Se desejar estimar o custo desse laboratório, você poderá usar o seguinte exemplo: 
 
Para uma classe de 25 alunos com 20 horas de tempo de aula agendada e 10 horas de cota para tarefas ou atividades, o preço do laboratório seria: 

25 alunos * (20 + 10) horas * 55 unidades de laboratório * 0, 1 USD por hora = 412,50 USD. 

Para obter mais informações sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para a aula de Ethical Hacking. Ele inclui etapas para configurar a virtualização aninhada para criar duas máquinas virtuais dentro da máquina virtual do host para fazer testes de penetração.

## <a name="next-steps"></a>Próximas etapas
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Enviar links de registro por email para alunos](how-to-configure-student-usage.md#send-invitations-to-users). 

