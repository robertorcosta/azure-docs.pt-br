---
title: Guia de configuração de laboratório de sala de aula acelerado para Azure Lab Services
description: Este guia ajuda os criadores de laboratório a configurar rapidamente uma conta de laboratório para uso em sua escola.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878095"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração do laboratório de sala de aula

O processo de publicação de um laboratório para seus alunos pode levar várias horas, dependendo do número de VMs (máquinas virtuais) que serão criadas em seu laboratório. Permita pelo menos um dia para configurar um laboratório, para garantir que ele esteja funcionando corretamente e para permitir tempo suficiente para publicar as VMs dos alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Entenda os requisitos de laboratório de sua classe

Antes de configurar um novo laboratório, você deve considerar as perguntas a seguir.

### <a name="what-software-requirements-does-the-class-have"></a>Quais requisitos de software a classe tem?

Com base nos objetivos de aprendizado de sua classe, decida quais sistemas operacionais, aplicativos e ferramentas precisam ser instalados nas VMs do laboratório. Para configurar VMs de laboratório, você tem três opções:

- **Usar uma imagem do Azure Marketplace: o**Azure Marketplace fornece centenas de imagens que você pode usar ao criar um laboratório. Para algumas classes, uma dessas imagens pode já conter tudo o que você precisa para sua classe.

- **Criar uma nova imagem personalizada**: você pode criar sua própria imagem personalizada usando uma imagem do Azure Marketplace como um ponto de partida e personalizando-a instalando software adicional e fazendo alterações de configuração.

- **Usar uma imagem personalizada existente**: você pode reutilizar as imagens personalizadas existentes que você criou anteriormente ou que foram criadas por outros administradores ou corpo docente na sua escola. Isso exige que os administradores tenham configurado uma galeria de imagens compartilhadas, que é um repositório para salvar imagens personalizadas.

> [!NOTE]
> Os administradores são responsáveis por habilitar imagens do Azure Marketplace e imagens personalizadas para que você possa usá-las. Coordene com o departamento de ti para garantir que as imagens necessárias estejam habilitadas. As imagens personalizadas que você cria são habilitadas automaticamente para uso nos laboratórios que você possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quais requisitos de hardware a classe tem?

Há uma variedade de tamanhos de computação que você pode escolher:

- Tamanhos de virtualização aninhados, para que você possa conceder acesso a alunos a uma VM capaz de hospedar várias VMs aninhadas. Por exemplo, você pode usar esse tamanho de computação para cursos de rede.

- Tamanhos de GPU, para que os alunos possam usar tipos de aplicativos que usam muitos computadores. Por exemplo, essa opção pode ser apropriada para inteligência artificial e aprendizado de máquina.

Consulte o guia em [dimensionamento de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa de tamanhos de computação disponíveis.

> [!NOTE]
> Dependendo da região do seu laboratório, você poderá ver menos tamanhos de computação disponíveis, pois isso varia por região. Em geral, você deve selecionar o menor tamanho de computação mais próximo de suas necessidades. Com o Azure Lab Services, você pode configurar um novo laboratório com uma capacidade de computação diferente posteriormente, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quais dependências a classe tem em recursos externos do Azure ou da rede?

Se suas VMs de laboratório precisarem usar recursos externos, como um banco de dados, compartilhamento de arquivos ou servidor de licenciamento, coordene com seus administradores para garantir que seu laboratório tenha acesso a esses recursos.

Para obter acesso aos recursos do Azure que *não* são protegidos por uma rede virtual, você não precisa buscar configuração adicional pelos administradores. Você pode acessar esses recursos por meio da Internet pública.

> [!NOTE]
> Você deve considerar se pode reduzir as dependências do laboratório para recursos externos fornecendo o recurso diretamente na VM. Por exemplo, para eliminar a necessidade de ler dados de um banco de dado externo, você pode instalar o banco de dados diretamente na VM.  

### <a name="how-will-costs-be-controlled"></a>Como os custos serão controlados?

Os serviços de laboratório usam um modelo de preços pago conforme o uso, o que significa que você paga apenas pelo tempo em que uma VM de laboratório está em execução. Para controlar os custos, você tem três opções que normalmente são usadas em conjunto umas com as outras:

- **Agenda**: uma agenda permite que você controle automaticamente quando as VMs de seus laboratórios são iniciadas e desligadas.
- **Cota**: a cota controla o número de horas que os alunos terão acesso a uma VM fora das horas agendadas. Se a cota for atingida enquanto um aluno a estiver usando, a VM será desligada automaticamente. O aluno não é capaz de reiniciar a VM, a menos que a cota seja aumentada.
- **Desligamento automático**: Quando habilitada, a configuração de desligamento automático faz com que as VMs do Windows sejam desligadas automaticamente após um determinado período de tempo, depois que um aluno se desconectou de uma sessão de protocolo RDP (RDP). Por padrão, essa configuração é desabilitada.  

    > [!NOTE]
    > Essa configuração atualmente só existe para Windows.

### <a name="how-will-students-save-their-work"></a>Como os alunos irão salvar seu trabalho?

Os alunos são cada uma atribuída à sua própria VM, que é atribuída a eles durante o tempo de vida do laboratório. Eles podem optar por:

- Salve diretamente na VM.
- Salve em um local externo, como OneDrive ou GitHub.

É possível configurar o OneDrive automaticamente para alunos em suas VMs de laboratório.

> [!NOTE]
> Para garantir que os alunos tenham acesso contínuo ao trabalho salvo fora do laboratório e, depois que a classe terminar, recomendamos que os alunos salvem seu trabalho em um repositório externo.

### <a name="how-will-students-connect-to-their-vm"></a>Como os alunos irão se conectar à VM?

Para VMs do RDP para o Windows, recomendamos que os alunos usem o [cliente área de trabalho remota da Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Área de Trabalho Remota cliente dá suporte a Macs, Chromebooks e Windows.

Para VMs do Linux, os alunos podem usar o SSH ou o RDP. Para que os alunos se conectem usando o RDP, você deve instalar e configurar os pacotes RDP e GUI necessários.

## <a name="set-up-your-lab"></a>Configurar seu laboratório

Depois de entender os requisitos para o laboratório de sua classe, você estará pronto para configurá-lo. Siga os links nesta seção para ver como configurar seu laboratório.

1. **Crie um laboratório.** Consulte o tutorial sobre como [criar um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) para obter instruções.

    > [!NOTE]
    > Se sua classe requer virtualização aninhada, consulte as etapas em [habilitando a virtualização aninhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalize imagens e publique VMs de laboratório.** Conecte-se a uma VM especial chamada VM de modelo. Consulte as etapas nos seguintes guias:
    - [Criar e gerenciar uma VM de modelo](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usar uma galeria de imagens compartilhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se estiver usando o Windows, você também deverá ver as instruções em [preparando uma VM de modelo do Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Essas instruções incluem etapas para configurar o OneDrive e o Office para seus alunos usarem.

1. **Gerenciar a capacidade e o pool de VMs.** Você pode dimensionar ou reduzir a capacidade da VM com facilidade, conforme necessário para sua classe. Tenha em mente que aumentar a capacidade da VM pode levar várias horas, porque isso envolve a configuração de novas VMs. Consulte as etapas em [Configurando e gerenciando um pool de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Adicionar e gerenciar usuários de laboratório.** Para adicionar usuários ao seu laboratório, consulte as etapas nos seguintes tutoriais:
   - [Adicionar usuários ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Para obter informações sobre os tipos de contas que os alunos podem usar, consulte [contas de alunos](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Definir controles de custo.** Para controlar os custos do seu laboratório, defina agendas, cotas e desligamento automático. Consulte os seguintes tutoriais:

   - [Definir um agendamento](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de sistema operacional que você instalou, uma VM pode levar vários minutos para ser iniciada. Para garantir que uma VM de laboratório esteja pronta para ser usada durante as horas agendadas, recomendamos que você inicie as VMs 30 minutos de antecedência.

   - [Definir cotas para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [definir cota adicional para um usuário específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Habilitar desligamento automático ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Agendas, cotas e desligamento automático não se aplicam à VM do modelo. Como resultado, você deve certificar-se de desligar a VM de modelo quando ela não estiver sendo usada. Caso contrário, ele continua incorrendo em custos. Além disso, por padrão, quando você cria um laboratório, a VM de modelo é iniciada automaticamente. Verifique se você concluiu imediatamente a configuração do laboratório e desligue a VM do modelo.

1. **Use o painel.** Para obter instruções, consulte [usando o painel do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > O custo estimado mostrado no painel é o custo máximo que você pode esperar para o uso dos alunos do laboratório. Por exemplo, você não será cobrado por horas de cotas *não* utilizadas por seus alunos. Os custos estimados *não* refletem nenhum encargo pelo uso da VM de modelo ou da Galeria de imagens compartilhada.

## <a name="next-steps"></a>Próximas etapas

- [Acompanhar o uso de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Acessar um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)