---
title: Guia de configuração do laboratório acelerado para Azure Lab Services
description: Este guia ajuda desenvolvedores a configurar rapidamente uma conta de laboratório para uso em escolas.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8ef168aefb69df32f57b623bb488adbb97cbd411
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659668"
---
# <a name="lab-setup-guide"></a>Guia de configuração do laboratório

O processo de publicação de um laboratório para seus alunos pode levar várias horas.  A quantidade de tempo depende do número de VMs (máquinas virtuais) que serão criadas em seu laboratório. Permita pelo menos um dia para configurar um laboratório, para garantir que ele esteja funcionando corretamente e para permitir tempo suficiente para publicar as VMs dos alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Entenda os requisitos de laboratório de sua classe

Antes de configurar um novo laboratório, lembre-se de pensar no seguinte.

### <a name="what-software-requirements-does-the-class-have"></a>Quais são os requisitos de software para a classe?

Com base nos objetivos de aprendizado da classe, decida quais sistemas operacionais, aplicativos e ferramentas precisam ser instalados nas VMs do laboratório. Para configurar VMs de laboratório, você tem três opções:

- **Usar uma de imagem do Azure Marketplace**: o Azure Marketplace fornece centenas de imagens que você pode usar ao criar um laboratório. Para algumas classes, uma dessas imagens já pode conter tudo o que você precisa.

- **Criar uma imagem personalizada**: você pode criar sua própria imagem personalizada usando uma imagem do Azure Marketplace como ponto de partida e personalizando-a ao instalar softwares adicionais e fazendo alterações na configuração.

- **Usar uma imagem personalizada existente**: você pode reutilizar as imagens personalizadas que criou anteriormente ou que foram criadas por outros administradores ou pelo corpo docente da sua escola. Para usar imagens personalizadas, os administradores precisam configurar uma galeria de imagens compartilhada.  Uma galeria de imagens compartilhadas é um repositório que é usado para salvar imagens personalizadas.

> [!NOTE]
> Os administradores são responsáveis por habilitar as imagens personalizadas e as do Azure Marketplace para que você possa usá-las. Coordene com o departamento de TI para garantir que as imagens necessárias estejam habilitadas. As imagens personalizadas que você cria são habilitadas automaticamente para uso nos laboratórios que você possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quais são os requisitos de hardware da classe?

Há diferentes tamanhos de computação que você pode escolher:

- Tamanhos de virtualização aninhados, para que você possa conceder acesso a alunos a uma VM que pode hospedar várias VMs aninhadas. Por exemplo, você pode usar esse tamanho de computação para redes ou aulas de Ethical Hacking.

- Tamanhos de GPU, para que os alunos possam usar tipos de aplicativos que usam muito o computador. Por exemplo, essa opção é geralmente usada com inteligência artificial e aprendizado de máquina.

Para obter orientação sobre como selecionar o tamanho apropriado da VM, leia os seguintes artigos:
- [Dimensionamento da VM](./administrator-guide.md#vm-sizing)
- [Mudando de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Dependendo da região em que você criará o laboratório, poderá ver menos tamanhos de computação disponíveis, pois isso varia por região. De maneira geral, você deve selecionar o menor tamanho de computação mais próximo a suas necessidades. Com o Azure Lab Services, você poderá configurar um novo laboratório com uma capacidade de computação diferente posteriormente, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quais são as dependências da classe em recursos externos do Azure ou da rede?
Suas VMs de laboratório podem precisar de acesso a recursos externos, como acessar um banco de dados, um compartilhamento de arquivos ou um servidor de licenciamento.  Para permitir que suas VMs de laboratório usem recursos externos, coordene com os administradores de ti.

> [!NOTE]
> Você deve considerar se pode reduzir as dependências do laboratório para recursos externos fornecendo o recurso diretamente na VM. Por exemplo, para eliminar a necessidade de ler dados de um banco de dados externo, você poderá instalar o banco de dados diretamente na VM.  

### <a name="how-will-costs-be-controlled"></a>Como os custos serão controlados?
Os serviços de laboratório usam um modelo de preços pago conforme o uso, o que significa que você paga apenas pelo tempo em que a VM está em execução no laboratório. Para controlar os custos, você tem três opções que normalmente são usadas em conjunto:

- **Agendamento**: o agendamento permite que você controle automaticamente quando as VMs nos laboratórios são iniciadas e desligadas.
- **Cota**: a cota controla o número de horas durante as quais os alunos terão acesso a uma VM fora das horas agendadas.  Quando um aluno está usando sua VM e sua cota é atingida, a VM é desligada automaticamente.  O aluno não conseguirá reiniciar a VM, a menos que a cota seja aumentada.
- **Desligamento** automático: quando habilitado, a configuração de desligamento automática faz com que as VMs do Windows sejam desligadas automaticamente depois que um aluno é desconectado de uma sessão de protocolo RDP (RDP). Por padrão, essa configuração está desabilitada.

Leia os artigos a seguir para obter mais informações:
- [Estimar custos](./cost-management-guide.md#estimate-the-lab-costs)
- [Gerenciar os custos](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Como os alunos salvarão seus trabalhos?
Cada aluno tem uma VM atribuída a ele durante seu tempo de vida útil da máquina no laboratório. Eles podem optar por:

- Salvar diretamente na VM.
- Salvar em um local externo, como OneDrive ou GitHub.

É possível configurar o OneDrive automaticamente para alunos em suas VMs no laboratório.

> [!NOTE]
> Para garantir que os alunos tenham acesso contínuo ao trabalho salvo fora do laboratório e, depois que a aula terminar, recomendamos que os alunos salvem seus trabalhos em um repositório externo.

### <a name="how-will-students-connect-to-their-vm"></a>Como os alunos se conectarão à VM?
Para VMs do RDP com Windows, recomendamos que os alunos usem a [Área de Trabalho Remota da Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). O cliente de Área de Trabalho Remota é compatível com Macs, Chromebooks e Windows.

Para VMs do Linux, os alunos podem usar SSH ou RDP. Para que os alunos se conectem usando RDP, você deve instalar e configurar os pacotes RDP e GUI necessários.

### <a name="will-students-also-be-using-microsoft-teams"></a>Os alunos também estarão usando o Microsoft Teams?
O Azure Lab Services integra-se com as equipes da Microsoft para que os professores possam criar e gerenciar seus laboratórios dentro das equipes.  Da mesma forma, os alunos podem acessar o laboratório dentro das equipes.

Para obter mais informações, consulte o seguinte artigo:
- [Azure Lab Services no Microsoft Teams](./lab-services-within-teams-overview.md)

## <a name="set-up-your-lab"></a>Configurar o laboratório

Depois de entender os requisitos para a criação do laboratório de sua classe, você estará pronto para configurá-lo. Acompanhe os links nesta seção para ver como configurar seu laboratório.  Observe que diferentes etapas são fornecidas dependendo de se você estiver usando laboratórios dentro de equipes.

1. **Criar um laboratório.** Consulte os tutoriais sobre como criar um laboratório:
    - [Crie um laboratório de sala de aula](./tutorial-setup-classroom-lab.md#create-a-classroom-lab) para obter instruções.
    - [Criar um laboratório por meio do Teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Se sua classe requer virtualização aninhada, siga as etapas em [Como habilitar a virtualização aninhada](./how-to-enable-nested-virtualization-template-vm.md).

1. **Personalizar imagens e publicar VMs no laboratório.** Conecte-se a uma VM especial chamada VM de modelo. Siga as etapas nos guias a seguir:
    - [Criar e gerenciar uma VM modelo](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Usar uma galeria de imagens compartilhada](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Se você usa o Windows, veja também as instruções em [como preparar uma VM de modelo do Windows](./how-to-prepare-windows-template.md). Essas instruções incluem etapas para configurar o OneDrive e o Office para uso pelos alunos.

1. **Gerenciar a capacidade e o pool de VMs.** Você pode ampliar ou reduzir a capacidade da VM com facilidade, conforme necessário para sua classe. Tenha em mente que aumentar a capacidade da VM pode levar várias horas porque as novas VMs estão sendo configuradas. Consulte as etapas nos seguintes artigos:
    - [Configurar e gerenciar um pool de VMs](./how-to-set-virtual-machine-passwords.md)
    - [Gerenciar um pool de VMs no Lab Services das equipes](./how-to-manage-vm-pool-within-teams.md)

1. **Adicionar e gerenciar os usuários do laboratório.** Para adicionar usuários ao laboratório, siga as etapas nos seguintes tutoriais:
   - [Adicionar usuários ao laboratório](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Enviar convites aos usuários](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Gerenciar listas de usuários do Lab Services de equipes](./how-to-manage-user-lists-within-teams.md)

    Para obter informações sobre os tipos de contas que os alunos podem usar, confira [Contas de alunos](./how-to-configure-student-usage.md#student-accounts).
  
1. **Definir controles de custo.** Para controlar os custos do seu laboratório, defina agendas, cotas e desligamento automático. Consulte os seguintes tutoriais:

   - [Definir um agendamento](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Dependendo do tipo de sistema operacional que você instalou, uma VM pode levar vários minutos para ser iniciada. Para garantir que uma VM de laboratório esteja pronta para ser usada durante um horário de agendamento, recomendamos iniciar as VMs com 30 minutos de antecedência.

   - [Definir cotas para usuários](./how-to-configure-student-usage.md#set-quotas-for-users) e [definir cota adicional para um usuário específico](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Habilitar desligamento automático ao desconectar](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Os agendamentos e cotas não se aplicam à VM modelo, mas as configurações de desligamento automático sim. 
        > 
        > Quando você cria um laboratório, a VM modelo é criada, mas não iniciada. Você pode iniciá-la, conectar-se a ela e instalar qualquer software de pré-requisito para o laboratório e, em seguida, publicá-la. Quando você publicar a VM de modelo, ela será desligada automaticamente, caso você ainda não tenha feito isso. 
        > 
        > As VMs de modelo incorrem em **custos** durante a execução, portanto, verifique se elas estão desligadas quando não precisam estar em execução.

    - [Criar e gerenciar agendas de serviços de laboratório dentro de equipes](./how-to-create-schedules-within-teams.md) 

1. **Usar o painel.** Para obter instruções, confira [Como usar o painel do laboratório](./use-dashboard.md).

    > [!NOTE]
    > O custo estimado mostrado no painel se refere ao valor máximo esperado de uso pelos alunos do laboratório. Por exemplo, cotas não usadas por seus alunos *não* são cobradas. Os custos estimados *não* refletem nenhuma cobrança pelo uso da VM de modelo, da Galeria de imagens compartilhada ou quando o criador do laboratório inicia um computador do usuário.

## <a name="next-steps"></a>Próximas etapas

- [Acompanhar o uso de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Acessar um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)