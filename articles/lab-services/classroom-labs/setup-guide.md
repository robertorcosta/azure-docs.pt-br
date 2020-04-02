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
ms.openlocfilehash: b87107545761e10c6fb648e54064fbafbdbacd52
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546002"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração do laboratório de sala de aula

O processo de publicação de um laboratório para seus alunos pode levar até várias horas, dependendo do número de máquinas virtuais (VMs) que serão criadas em seu laboratório.  Você deve dar a si mesmo pelo menos um dia para montar um laboratório para garantir que ele está funcionando corretamente e para dar tempo suficiente para publicar VMs dos alunos.

## <a name="understand-your-classs-lab-requirements"></a>Entenda os requisitos de laboratório da sua classe

Antes de montar um novo laboratório, você deve considerar as seguintes perguntas:

**Quais os requisitos de software que a classe tem?**

Com base nos objetivos de aprendizagem da sua classe, você deve decidir quais sO, aplicativos, ferramentas, etc. precisam ser instalados nas VMs do laboratório.   Para configurar vMs de laboratório, você tem três opções:

- **Use uma imagem do Azure Marketplace** – O Marketplace fornece centenas de imagens que você pode usar ao criar um laboratório.  Para algumas classes, uma imagem de marketplace já pode conter tudo o que você precisa para sua aula.

- **Crie uma nova imagem personalizada** - Você pode criar sua própria imagem personalizada usando uma imagem de mercado como ponto de partida e personalizando-a instalando software adicional, fazendo alterações de configuração, etc.

- **Use uma imagem personalizada existente** - Você pode reutilizar imagens personalizadas existentes que você criou anteriormente ou que foram criadas por outros administradores ou professores em sua escola; isso exige que seus administradores tenham configurado uma Galeria de Imagens Compartilhadas, que é um repositório para salvar imagens personalizadas.

> [!NOTE]
> Seus administradores são responsáveis por habilitar o Marketplace e imagens personalizadas para que você possa usá-las; você precisará coordenar com seu departamento de TI para garantir que as imagens que você precisa estejam habilitadas.  As imagens personalizadas que você cria são automaticamente habilitadas para uso dentro de laboratórios que você possui.

**Quais requisitos de hardware a classe tem?**

Há uma variedade de tamanhos de computação que você pode escolher que inclui:

- Tamanhos de virtualização aninhados para que você possa dar acesso aos alunos a uma VM que seja capaz de hospedar várias VMs aninhadas; por exemplo, esse tamanho de computação é frequentemente usado para cursos de Networking.

- Tamanhos de GPU para que seus alunos possam usar tipos de aplicativos com uso intensivo de computador, como para Inteligência Artificial e Machine Learning.

Consulte o guia de [dimensionamento de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa dos tamanhos de computação disponíveis.

> [!NOTE]
> Dependendo da região em que seu laboratório será criado, você pode ver menos tamanhos de computação disponíveis, uma vez que isso varia de acordo com a região.  Nossa recomendação geral é selecionar o menor tamanho de computação mais próximo às suas necessidades.  Com o Azure Lab Services, você pode criar um novo laboratório com uma capacidade computacional diferente mais tarde, se necessário.

**Quais dependências a classe tem sobre recursos externos do Azure ou de rede?**

Se as VMs do seu laboratório precisarem usar recursos externos, como banco de dados, compartilhamento de arquivos, servidor de licenciamento, etc. você precisará coordenar com seus administradores para garantir que seu laboratório tenha acesso a esses recursos.

Para acesso aos recursos do Azure que *não* são protegidos por uma rede virtual, então você pode acessar esses recursos através da internet pública sem qualquer configuração adicional por seus administradores.

> [!NOTE]
> Você deve considerar se você pode reduzir as dependências do seu laboratório para recursos externos, fornecendo o recurso diretamente na VM.  Por exemplo, para eliminar a necessidade de ler dados de um banco de dados externo, você pode instalar o banco de dados diretamente na VM.  

**Como os custos serão controlados?**

A Lab Services usa um modelo de preços de pagamento, o que significa que você só paga pelo tempo que uma VM de laboratório está executando.  Para controlar os custos, você tem três opções que normalmente são usadas em conjunto uma com a outra:

- **Cronograma** - Um cronograma permite que você controle automaticamente quando as VMs de seus laboratórios são iniciadas e desligadas.
- **Cota** - A cota controla o número de horas que os alunos terão acesso a uma VM fora do horário de funcionamento.  Se a cota for atingida enquanto um aluno estiver usando- a, a VM será automaticamente desligada e o aluno não poderá reiniciar a VM a menos que a cota seja aumentada.
- **Desligamento automático** - Quando ativado, a configuração de desligamento automático faz com que as VMs do Windows se desliguem automaticamente após um certo período de tempo, uma vez que um aluno tenha se desconectado de sua sessão RDP.  Por padrão, essa configuração é desabilitada.  

    > [!NOTE]
    > Esta configuração atualmente só existe para windows.

**Como os alunos salvarão seu trabalho?**

Cada aluno é designado seu próprio VM que é atribuído a eles para a vida do laboratório.  Eles podem escolher:

- Salve diretamente na VM.
- Economize em um local externo, como OneDrive, GitHub, etc.

Para usar o OneDrive, você pode optar por configurá-lo automaticamente para estudantes em suas VMs de laboratório.  Informações adicionais sobre isso são fornecidas abaixo.

> [!NOTE]
> Para garantir que seus alunos tenham acesso contínuo ao seu trabalho salvo fora do laboratório, o que inclui após o término da aula, recomendamos que os alunos salvem seu trabalho em um repositório externo.

**Como os alunos se conectarão à Sua VM?**

Para VMs RDP e Windows, recomendamos que os alunos usem [o cliente Microsoft Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  O cliente de desktop remoto suporta Macs, Chromebooks e Windows.

Para VMs Linux, os alunos podem usar SSH ou RDP.   Para conectar usando RDP, você é responsável pela instalação e configuração dos pacotes RDP e GUI necessários.  Mais detalhes sobre isso são fornecidos abaixo.

## <a name="set-up-your-lab"></a>Configurar seu laboratório

Uma vez que você entenda os requisitos para o laboratório da sua classe, você está pronto para configurá-lo.  Siga os links nesta seção para ver como configurar seu laboratório.

1. **Criar um laboratório**

   Consulte o tutorial sobre a [criação de um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) para obter instruções.

    > [!NOTE]
    > Se sua classe exigir virtualização aninhada, consulte as etapas do guia de como fazer que mostra [a ativação da virtualização aninhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalizar imagens e publicar VMs de laboratório**

    Para personalizar imagens e publicar VMs em seu laboratório, você se conecta a uma VM especial chamada VM modelo; consulte as etapas dos seguintes guias:
    - [Criar e gerenciar um modelo VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Use uma galeria de imagens compartilhadas](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se você estiver usando o Windows, você também deve consultar as instruções no guia de como [preparar um VM modelo do Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template).  Essas instruções incluem etapas para configurar o OneDrive e o Office para que seus alunos usem.

1. **Gerenciar pool e capacidade de VM**

   Você pode facilmente aumentar ou diminuir a capacidade de VM conforme necessário pela sua classe.  Tenha em mente que o aumento da capacidade da VM pode levar várias horas, já que isso envolve a criação de novas VMs.  Consulte as etapas do guia de como fazer para [configurar e gerenciar um pool de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Adicionar e gerenciar os usuários do laboratório**

   Para adicionar usuários ao seu laboratório, consulte as etapas dos seguintes tutoriais:
   - [Adicionar usuários ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites aos usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Além disso, consulte o artigo a seguir para obter informações sobre os tipos de contas que os alunos podem usar:
    - [Contas estudantis](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Definir controles de custos**

    Para controlar os custos do seu laboratório, defina horários, cotas e desligamento automático; consulte as instruções nos seguintes tutoriais:

   - [Defina um cronograma](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de sistema operacional instalado, uma VM pode levar vários minutos para ser iniciada.  Para garantir que uma VM de laboratório esteja pronta para uso durante o horário programado, recomendamos que você inicie as VMs com 30 minutos de antecedência para garantir que as VMs estejam funcionando e prontas para uso.

   - [Defina cotas para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [estabeleça cotas adicionais para um usuário específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Habilitar desligamento automático ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Horários, cotas e desligamento automático *não* se aplicam ao modelo VM.  Como resultado, você deve garantir que você desligue o modelo VM quando ele não estiver sendo usado, caso contrário, ele continuará a incorrer em custos.  Além disso, por padrão, quando você cria um laboratório, o modelo VM é iniciado automaticamente, então você vai querer ter certeza de que você terminará imediatamente a configuração do laboratório e desligo o modelo VM.

1. **Usar o dashboard**

    Consulte o guia de como [usar o painel do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard) para obter instruções.

    > [!NOTE]
    > Os custos estimados mostrados no painel é o custo máximo que você pode esperar para o uso do laboratório pelos alunos.  Por exemplo, você *não* será cobrado por horas de cotas não utilizadas por seus alunos.  Os custos estimados *não* refletem quaisquer encargos para o uso do modelo VM ou da Galeria de Imagens Compartilhadas.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Acompanhar o uso de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Acessar um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)