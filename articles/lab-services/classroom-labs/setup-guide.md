---
title: Guia de configuração de laboratório de sala de aula acelerado para os serviços do Laboratório Azure
description: Este guia ajuda os criadores de laboratório saem rapidamente de uma conta de laboratório para uso dentro de sua escola.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878095"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração do laboratório de sala de aula

O processo de publicação de um laboratório para seus alunos pode levar até várias horas, dependendo do número de máquinas virtuais (VMs) que serão criadas em seu laboratório. Permita pelo menos um dia para montar um laboratório, para garantir que ele está funcionando corretamente e para permitir tempo suficiente para publicar VMs dos alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Entenda os requisitos de laboratório de sua classe

Antes de montar um novo laboratório, você deve considerar as seguintes perguntas.

### <a name="what-software-requirements-does-the-class-have"></a>Quais os requisitos de software que a classe tem?

Com base nos objetivos de aprendizagem da sua classe, decida qual sistema operacional, aplicativos e ferramentas precisam ser instalados nas VMs do laboratório. Para configurar vMs de laboratório, você tem três opções:

- **Use uma imagem do Azure Marketplace**: O Azure Marketplace fornece centenas de imagens que você pode usar quando está criando um laboratório. Para algumas aulas, uma dessas imagens já pode conter tudo o que você precisa para sua aula.

- **Crie uma nova imagem personalizada**: Você pode criar sua própria imagem personalizada usando uma imagem do Azure Marketplace como ponto de partida e personalizando-a instalando software adicional e fazendo alterações de configuração.

- **Use uma imagem personalizada existente**: Você pode reutilizar imagens personalizadas existentes que você criou anteriormente ou que foram criadas por outros administradores ou professores em sua escola. Isso exige que seus administradores tenham configurado uma galeria de imagens compartilhadas, que é um repositório para salvar imagens personalizadas.

> [!NOTE]
> Seus administradores são responsáveis por habilitar imagens e imagens personalizadas do Azure Marketplace para que você possa usá-las. Coordene com seu departamento de TI para garantir que as imagens necessárias estejam habilitadas. As imagens personalizadas que você cria são automaticamente habilitadas para uso dentro de laboratórios que você possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quais requisitos de hardware a classe tem?

Há uma variedade de tamanhos de computação que você pode escolher:

- Tamanhos de virtualização aninhados, para que você possa dar acesso aos alunos a uma VM que é capaz de hospedar várias VMs aninhadas. Por exemplo, você pode usar esse tamanho de computação para cursos de networking.

- Tamanhos de GPU, para que seus alunos possam usar tipos de aplicativos com uso intensivo de computador. Por exemplo, essa escolha pode ser apropriada para inteligência artificial e aprendizado de máquina.

Consulte o guia de [dimensionamento de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa dos tamanhos de computação disponíveis.

> [!NOTE]
> Dependendo da região do seu laboratório, você pode ver menos tamanhos de computação disponíveis, porque isso varia de acordo com a região. Geralmente, você deve selecionar o menor tamanho de computação que esteja mais próximo de suas necessidades. Com o Azure Lab Services, você pode montar um novo laboratório com uma capacidade computacional diferente mais tarde, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quais dependências a classe tem sobre recursos externos do Azure ou de rede?

Se as VMs do seu laboratório precisarem usar recursos externos, como banco de dados, compartilhamento de arquivos ou servidor de licenciamento, coordene com seus administradores para garantir que seu laboratório tenha acesso a esses recursos.

Para ter acesso aos recursos do Azure que *não* são protegidos por uma rede virtual, você não precisa buscar configuração adicional por seus administradores. Você pode acessar esses recursos através da internet pública.

> [!NOTE]
> Você deve considerar se você pode reduzir as dependências do seu laboratório para recursos externos, fornecendo o recurso diretamente na VM. Por exemplo, para eliminar a necessidade de ler dados de um banco de dados externo, você pode instalar o banco de dados diretamente na VM.  

### <a name="how-will-costs-be-controlled"></a>Como os custos serão controlados?

A Lab Services usa um modelo de preços de pagamento, o que significa que você só paga pelo tempo que uma VM de laboratório está executando. Para controlar os custos, você tem três opções que normalmente são usadas em conjunto uma com a outra:

- **Cronograma**: Um cronograma permite que você controle automaticamente quando as VMs de seus laboratórios são iniciadas e desligadas.
- **Cota**: A cota controla o número de horas que os alunos terão acesso a uma VM fora do horário de funcionamento. Se a cota for atingida enquanto um aluno estiver usando-a, a VM será automaticamente desligada. O aluno não é capaz de reiniciar a VM a menos que a cota seja aumentada.
- **Desligamento automático**: Quando ativado, a configuração de desligamento automático faz com que as VMs do Windows se desliguem automaticamente após um certo período de tempo, depois que um aluno se desconectou de uma sessão RDP (Remote Desktop Protocol, protocolo de desktop remoto). Por padrão, essa configuração é desabilitada.  

    > [!NOTE]
    > Esta configuração atualmente só existe para windows.

### <a name="how-will-students-save-their-work"></a>Como os alunos salvarão seu trabalho?

Cada aluno é designado seu próprio VM, que é atribuído a eles para a vida do laboratório. Eles podem escolher:

- Salve diretamente na VM.
- Economize em um local externo, como OneDrive ou GitHub.

É possível configurar o OneDrive automaticamente para alunos em suas VMs de laboratório.

> [!NOTE]
> Para garantir que seus alunos tenham acesso contínuo ao seu trabalho salvo fora do laboratório, e após o término da aula, recomendamos que os alunos salvem seu trabalho em um repositório externo.

### <a name="how-will-students-connect-to-their-vm"></a>Como os alunos se conectarão à Sua VM?

Para VMs RDP e Windows, recomendamos que os alunos usem o [cliente Microsoft Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). O cliente de desktop remoto suporta Macs, Chromebooks e Windows.

Para VMs Linux, os alunos podem usar SSH ou RDP. Para que os alunos se conectem usando RDP, você deve instalar e configurar os pacotes RDP e GUI necessários.

## <a name="set-up-your-lab"></a>Configurar seu laboratório

Depois de entender os requisitos para o laboratório da sua classe, você está pronto para configurá-lo. Siga os links nesta seção para ver como configurar seu laboratório.

1. **Crie um laboratório.** Consulte o tutorial sobre a [criação de um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) para obter instruções.

    > [!NOTE]
    > Se sua classe exigir virtualização aninhada, veja as etapas para [ativar a virtualização aninhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalize imagens e publique VMs de laboratório.** Conecte-se a uma VM especial chamada VM modelo. Veja os passos nos seguintes guias:
    - [Criar e gerenciar um modelo VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usar uma galeria de imagens compartilhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se você estiver usando o Windows, você também deve ver as instruções na [preparação de um modelo VM do Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Essas instruções incluem etapas para configurar o OneDrive e o Office para que seus alunos usem.

1. **Gerencie o pool e a capacidade da VM.** Você pode facilmente aumentar ou diminuir a capacidade de VM, conforme necessário pela sua classe. Tenha em mente que o aumento da capacidade da VM pode levar várias horas, pois isso envolve a configuração de novas VMs. Veja os passos na [configuração e gerenciamento de um pool de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Adicione e gerencie usuários de laboratório.** Para adicionar usuários ao seu laboratório, consulte as etapas dos seguintes tutoriais:
   - [Adicionar usuários ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites aos usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Para obter informações sobre os tipos de contas que os alunos podem usar, consulte [contas de estudantes](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Defina controles de custos.** Para controlar os custos do seu laboratório, defina horários, cotas e desligamento automático. Consulte os seguintes tutoriais:

   - [Defina um cronograma](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de sistema operacional instalado, uma VM pode levar vários minutos para ser iniciada. Para garantir que um VM de laboratório esteja pronto para uso durante o horário programado, recomendamos que você inicie as VMs com 30 minutos de antecedência.

   - [Defina cotas para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [estabeleça cotas adicionais para um usuário específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Habilitar desligamento automático ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Horários, cotas e desligamento automático não se aplicam ao modelo VM. Como resultado, você deve garantir que você desligue o modelo VM quando ele não estiver sendo usado. Caso contrário, continua a incorrer em custos. Além disso, por padrão, quando você cria um laboratório, o modelo VM é iniciado automaticamente. Certifique-se de terminar imediatamente a configuração do laboratório e desligue o modelo VM.

1. **Use o painel de instrumentos.** Para obter instruções, consulte [usando o painel do laboratório.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > O custo estimado mostrado no painel é o custo máximo que você pode esperar para o uso do laboratório pelos alunos. Por exemplo, você *não* será cobrado por horas de cotas não utilizadas por seus alunos. Os custos estimados *não* refletem quaisquer encargos para o uso do modelo VM ou da galeria de imagens compartilhadas.

## <a name="next-steps"></a>Próximas etapas

- [Acompanhar o uso de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Acessar um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)