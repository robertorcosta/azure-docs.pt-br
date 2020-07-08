---
title: Guia de configuração acelerada do laboratório de sala de aula do Azure Lab Services
description: Este guia ajuda desenvolvedores a configurar rapidamente uma conta de laboratório para uso em escolas.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad1a679c556316c2d23a713ffa5ac5dfe86cce0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445569"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração de laboratórios de sala de aula

O processo de publicação de um laboratório para os alunos pode levar várias horas, dependendo do número de VMs (máquinas virtuais) que serão criadas. Reserve pelo menos um dia para configurar um laboratório. Isso garantirá que ele esteja funcionando corretamente e dará tempo suficiente para publicar as VMs dos alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Entenda os requisitos de laboratório de sua classe

Antes de configurar um novo laboratório, lembre-se de pensar no seguinte.

### <a name="what-software-requirements-does-the-class-have"></a>Quais são os requisitos de software para a classe?

Com base nos objetivos de aprendizado da classe, decida quais sistemas operacionais, aplicativos e ferramentas precisam ser instalados nas VMs do laboratório. Para configurar VMs de laboratório, você tem três opções:

- **Usar uma de imagem do Azure Marketplace**: o Azure Marketplace fornece centenas de imagens que você pode usar ao criar um laboratório. Para algumas classes, uma dessas imagens já pode conter tudo o que você precisa.

- **Criar uma imagem personalizada**: você pode criar sua própria imagem personalizada usando uma imagem do Azure Marketplace como ponto de partida e personalizando-a ao instalar softwares adicionais e fazendo alterações na configuração.

- **Usar uma imagem personalizada existente**: você pode reutilizar as imagens personalizadas que criou anteriormente ou que foram criadas por outros administradores ou pelo corpo docente da sua escola. Isso exige que os administradores tenham configurado uma galeria de imagens compartilhadas, que é um repositório para salvar imagens personalizadas.

> [!NOTE]
> Os administradores são responsáveis por habilitar as imagens personalizadas e as do Azure Marketplace para que você possa usá-las. Coordene com o departamento de TI para garantir que as imagens necessárias estejam habilitadas. As imagens personalizadas que você cria são habilitadas automaticamente para uso nos laboratórios que você possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quais são os requisitos de hardware da classe?

Há uma variedade de tamanhos de computação que você pode escolher:

- Tamanhos de virtualização aninhados, para que você possa conceder acesso aos alunos a uma máquina capaz de hospedar várias VMs aninhadas. Por exemplo, você pode usar esse tamanho de computação para cursos de rede.

- Tamanhos de GPU, para que os alunos possam usar tipos de aplicativos que usam muito o computador. Por exemplo, essa opção pode ser apropriada para inteligência artificial e aprendizado de máquina.

Confira o guia sobre [Dimensionamento de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa de tamanhos de computação disponíveis.

> [!NOTE]
> Dependendo da região em que você criará o laboratório, poderá ver menos tamanhos de computação disponíveis, pois isso varia por região. De maneira geral, você deve selecionar o menor tamanho de computação mais próximo a suas necessidades. Com o Azure Lab Services, você poderá configurar um novo laboratório com uma capacidade de computação diferente posteriormente, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quais são as dependências da classe em recursos externos do Azure ou da rede?

Se suas VMs de laboratório precisarem usar recursos externos, como um banco de dados, compartilhamento de arquivos ou servidor de licenciamento, coordene com seus administradores para garantir que seu laboratório tenha acesso a esses recursos.

Para obter acesso aos recursos do Azure *não* protegidos por uma rede virtual, não é necessário buscar configurações adicionais por seus administradores. Você pode acessar esses recursos por meio da Internet pública.

> [!NOTE]
> Você deve considerar se pode reduzir as dependências do laboratório para recursos externos fornecendo o recurso diretamente na VM. Por exemplo, para eliminar a necessidade de ler dados de um banco de dados externo, você poderá instalar o banco de dados diretamente na VM.  

### <a name="how-will-costs-be-controlled"></a>Como os custos serão controlados?

Os serviços de laboratório usam um modelo de preços pago conforme o uso, o que significa que você paga apenas pelo tempo em que a VM está em execução no laboratório. Para controlar os custos, você tem três opções que normalmente são usadas em conjunto:

- **Agendamento**: o agendamento permite que você controle automaticamente quando as VMs nos laboratórios são iniciadas e desligadas.
- **Cota**: a cota controla o número de horas durante as quais os alunos terão acesso a uma VM fora das horas agendadas. Se a cota for atingida enquanto um aluno a estiver usando, a VM será desligada automaticamente. O aluno não conseguirá reiniciar a VM, a menos que a cota seja aumentada.
- **Desligamento automático**: quando habilitada, a configuração de desligamento automático faz com que as VMs do Windows sejam desligadas automaticamente após um determinado período de tempo, depois que um aluno se desconectou de uma sessão de protocolo RDP (Remote Desktop). Por padrão, essa configuração é desabilitada.  

    > [!NOTE]
    > No momento, essa configuração só existe no Windows.

### <a name="how-will-students-save-their-work"></a>Como os alunos salvarão seus trabalhos?

Cada aluno tem uma VM atribuída a ele durante seu tempo de vida útil da máquina no laboratório. Eles podem optar por:

- Salvar diretamente na VM.
- Salvar em um local externo, como OneDrive ou GitHub.

É possível configurar o OneDrive automaticamente para alunos em suas VMs no laboratório.

> [!NOTE]
> Para garantir que os alunos tenham acesso contínuo ao trabalho salvo fora do laboratório e, depois que a aula terminar, recomendamos que os alunos salvem seus trabalhos em um repositório externo.

### <a name="how-will-students-connect-to-their-vm"></a>Como os alunos se conectarão à VM?

Para VMs do RDP com Windows, recomendamos que os alunos usem a [Área de Trabalho Remota da Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). O cliente de Área de Trabalho Remota é compatível com Macs, Chromebooks e Windows.

Para VMs do Linux, os alunos podem usar SSH ou RDP. Para que os alunos se conectem usando RDP, você deve instalar e configurar os pacotes RDP e GUI necessários.

## <a name="set-up-your-lab"></a>Configurar o laboratório

Depois de entender os requisitos para a criação do laboratório de sua classe, você estará pronto para configurá-lo. Acompanhe os links nesta seção para ver como configurar seu laboratório.

1. **Criar um laboratório.** Confira o tutorial em [criando um de laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) para obter instruções.

    > [!NOTE]
    > Se sua classe requer virtualização aninhada, siga as etapas em [Como habilitar a virtualização aninhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalizar imagens e publicar VMs no laboratório.** Conecte-se a uma VM especial chamada VM de modelo. Siga as etapas nos guias a seguir:
    - [Criar e gerenciar uma VM modelo](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usar uma galeria de imagens compartilhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se você usa o Windows, veja também as instruções em [como preparar uma VM de modelo do Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Essas instruções incluem etapas para configurar o OneDrive e o Office para uso pelos alunos.

1. **Gerenciar a capacidade e o pool de VMs.** Você pode ampliar ou reduzir a capacidade da VM com facilidade, conforme necessário para sua classe. Tenha em mente que aumentar a capacidade da VM pode levar várias horas, pois isso exige que se configure novas VMs. Siga as etapas em [configuração e gerenciamento de um pool de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Adicionar e gerenciar os usuários do laboratório.** Para adicionar usuários ao laboratório, siga as etapas nos seguintes tutoriais:
   - [Adicionar usuários ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites aos usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Para obter informações sobre os tipos de contas que os alunos podem usar, confira [Contas de alunos](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Definir controles de custo.** Para controlar os custos de um laboratório, defina agendamentos, cotas e desligamento automático. Consulte os seguintes tutoriais:

   - [Definir um agendamento](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de sistema operacional que você instalou, uma VM pode levar vários minutos para ser iniciada. Para garantir que uma VM de laboratório esteja pronta para ser usada durante um horário de agendamento, recomendamos iniciar as VMs com 30 minutos de antecedência.

   - [Definir cotas para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [definir cota adicional para um usuário específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Habilitar desligamento automático ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Os agendamentos e cotas não se aplicam à VM modelo, mas as configurações de desligamento automático sim. 
        > 
        > Quando você cria um laboratório, a VM modelo é criada, mas não iniciada. Você pode iniciá-lo, conectar-se a ele e instalar qualquer software de pré-requisito para o laboratório e, em seguida, publicá-lo. Quando você publicar a VM de modelo, ela será desligada automaticamente, caso você ainda não o tenha feito. 
        > 
        > Durante a execução, as VMs modelo incorrem em **custos**, portanto, verifique se elas estão desligadas quando não estiverem em uso. 


1. **Usar o painel.** Para obter instruções, confira [Como usar o painel do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > O custo estimado mostrado no painel se refere ao valor máximo esperado de uso pelos alunos do laboratório. Por exemplo, cotas não usadas por seus alunos *não* são cobradas. Os custos estimados *não* refletem nenhuma cobrança pelo uso da VM de modelo, da Galeria de imagens compartilhada ou quando o criador do laboratório inicia um computador do usuário.

## <a name="next-steps"></a>Próximas etapas

- [Acompanhar o uso de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Acessar um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)