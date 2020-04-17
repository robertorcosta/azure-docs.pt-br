---
title: Criar um laboratório para ensinar análise de big data usando o Azure Lab Services | Microsoft Docs
description: Aprenda a configurar um laboratório para ensinar a análise de big data usando a implantação do Docker da Hortonworks Data Platform (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538772"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Configure um laboratório para análise de big data usando a implantação do Docker da HortonWorks Data Platform

Este artigo mostra como montar um laboratório para ensinar uma aula de análise de big data.  Com esse tipo de aula, os alunos aprendem a lidar com grandes volumes de dados e aplicam algoritmos de aprendizagem de máquina e estatística para obter insights de dados.  Um objetivo fundamental para os alunos é aprender a usar ferramentas de análise de dados, como o [pacote de software de código aberto da Apache Hadoop,](https://hadoop.apache.org/) que fornece ferramentas para armazenar, gerenciar e processar big data.

Neste laboratório, os alunos usarão uma versão comercial popular de Hadoop fornecida pela [Cloudera](https://www.cloudera.com/), chamada [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html).  Especificamente, os alunos usarão [o HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) que é uma versão simplificada e fácil de usar da plataforma que é livre de custos e destinada à aprendizagem e experimentação.  Embora esta classe possa usar máquinas virtuais Windows ou Linux (VM) com hdp sandbox implantado, este artigo mostrará como usar o Windows.

Outro aspecto interessante deste laboratório, é que vamos implantar hdp sandbox no laboratório VMs usando contêineres [Docker.](https://www.docker.com/)  Cada contêiner Docker fornece seu próprio ambiente isolado para que aplicativos de software seja executados no interior.  Conceitualmente, os contêineres Docker são como VMs aninhados e podem ser usados para implantar e executar facilmente uma grande variedade de aplicativos de software com base em imagens de contêineres fornecidas no [Docker Hub](https://www.docker.com/products/docker-hub).  O script de implantação da Cloudera para HDP Sandbox puxa automaticamente a [imagem HDP Sandbox 3.0.1 Docker](https://hub.docker.com/r/hortonworks/sandbox-hdp) do Docker Hub e executa dois contêineres Docker:
  - caixa de areia-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [Tutorial to Setup a Lab Account](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações da conta de laboratório

Habilite as configurações descritas na tabela abaixo para a conta do laboratório. Para obter mais informações sobre como habilitar imagens de marketplace, consulte [Especificar imagens do Marketplace disponíveis para criadores de laboratórios](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem de mercado| Habilite a imagem do Windows 10 Pro para uso dentro de sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações de laboratório

Use as configurações na tabela abaixo ao montar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório em sala de aula](tutorial-setup-classroom-lab.md).

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| Medium (Virtualização Aninhado). Este tamanho vm é mais adequado para bancos de dados relacionais, cache na memória e análises.  Esse tamanho também suporta virtualização aninhada.|  
|Imagem de máquina virtual| Windows 10 Pro|

> [!NOTE] 
> Precisamos usar medium (virtualização aninhada) já que a implantação do HDP Sandbox usando o Docker requer:
>   - Windows Hyper-V com virtualização aninhada
>   - Pelo menos 10 GB de RAM

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para configurar a máquina de modelo, vamos:
- Instalar o Docker
- Implantar caixa de areia HDP
- Use o PowerShell e o Windows Task Scheduler para iniciar automaticamente os contêineres Docker

### <a name="install-docker"></a>Instalar o Docker

As etapas desta seção são baseadas nas [instruções da Cloudera para implantação com contêineres Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Para usar os contêineres Docker, você deve primeiro instalar o Docker Desktop no modelo VM:

1. Siga as etapas na [seção Pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) para instalar [o Docker for Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Certifique-se de que os **contêineres use o Windows em vez da** opção de configuração de contêineres Linux sejam deixados sem controle.

1. Certifique-se de que **os recursos do Windows Containers e do Hyper-V** estejam ligados.
   ![Ativar ou desativar recursos do Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Siga as etapas na seção [Memória para Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) para configurar a configuração de memória do Docker.

    > [!WARNING]
    > Se você verificar inadvertidamente os contêineres use o Windows em vez da opção **de contêineres Linux** ao instalar o Docker, você não verá as configurações de configuração de memória.  Para corrigir isso, você pode mudar para usar contêineres Linux [clicando no ícone Docker na bandeja do Sistema Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); quando o menu Do Docker Desktop for aberto, selecione **'Alternar para contêineres Linux'.**
 
### <a name="deploy-hdp-sandbox"></a>Implantar caixa de areia HDP

Nesta seção, você implantará o HDP Sandbox e, em seguida, também acessará o HDP Sandbox usando o navegador.

1. Certifique-se de que você instalou [o Git Bash](https://gitforwindows.org/) conforme listado na [seção Pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) do guia, uma vez que isso é recomendado para completar as próximas etapas.

1. Usando [o Guia de Implantação e Instalação do Cloudera para Docker,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)complete as etapas nas seguintes seções:
   
   -    Implantar caixa de areia HDP
   -    Verifique a caixa de areia HDP

    > [!WARNING] 
    > Ao baixar o arquivo .zip mais recente para HDP, certifique-se de que você *não* salve o arquivo .zip em um caminho de diretório que inclua espaço em branco.

    > [!NOTE] 
    > Se você receber uma exceção durante a implantação afirmando **que a Unidade não foi compartilhada,** você precisa compartilhar sua unidade C com o Docker para que os contêineres Linux do HDP possam acessar arquivos locais do Windows.  Para corrigir isso, [clique no ícone Docker na bandeja do Sistema Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) para abrir o menu Dono de área de trabalho e selecionar **Configurações**.  Quando a caixa de diálogo **Configurações do Docker for** aberta, selecione **Recursos > Compartilhamento de arquivos** e verifique a unidade **C.**  Em seguida, você pode repetir as etapas para implantar o HDP Sandbox.

1. Uma vez que os contêineres Docker para HDP Sandbox sejam implantados e executados, você pode acessar o ambiente lançando seu navegador e seguindo as instruções da Cloudera para abrir a [Página de Boas-Vindas do Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e iniciar o Painel HDP.

    > [!NOTE] 
    > Essas instruções supõem que você mapeou pela primeira vez o endereço IP local do ambiente sandbox para o sandbox-hdp.hortonworks.com no arquivo host em sua VM modelo.  Se você **não** fizer esse mapeamento, você pode acessar a [http://localhost:8080](http://localhost:8080)página de boas-vindas do Sandbox navegando para .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Inicie automaticamente os contêineres Docker quando os alunos fizerem login

Para proporcionar uma experiência fácil de usar para os alunos, usaremos um script PowerShell que automaticamente:
  - Inicia os contêineres HDP Sandbox Docker quando um aluno inicia e se conecta à VM do laboratório.
  - Lança o navegador e navega para a Página de Boas-Vindas do Sandbox.
Também usaremos o Agendador de Tarefas do Windows para executar automaticamente esse script quando um aluno fizer login em sua VM.
Para configurar isso, siga estas etapas: [Scripting do Big Data Analytics](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Estimativa de custos

Se você quiser estimar o custo deste laboratório, você pode usar o seguinte exemplo.

Para uma turma de 25 alunos com 20 horas de aula programadas e 10 horas de cota para lição de casa ou tarefas, o preço para o laboratório seria:
  - 25 alunos * (20 + 10) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,50 USD

Mais detalhes sobre preços, consulte [Preços de Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo caminhou você através das etapas necessárias para criar um laboratório para uma classe de análise de big data que usando hortonworks Data Platform implantado com Docker.  A configuração para este tipo de classe pode ser usada para classes de análise de dados semelhantes.  Essa configuração também pode ser aplicável a outros tipos de classes que usam docker para implantação.

## <a name="next-steps"></a>Próximas etapas

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
