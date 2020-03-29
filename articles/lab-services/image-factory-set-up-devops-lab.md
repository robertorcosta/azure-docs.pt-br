---
title: Execute uma fábrica de imagens do Azure DevOps em Azure DevTest Labs
description: Este artigo abrange todos os preparativos necessários para executar a fábrica de imagens do Azure DevOps (anteriormente Visual Studio Team Services).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758675"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Executar uma fábrica de imagem do Azure DevOps
Este artigo abrange todos os preparativos necessários para executar a fábrica de imagens do Azure DevOps (anteriormente Visual Studio Team Services).

> [!NOTE]
> Qualquer motor de orquestração funcionará! Azure DevOps não é obrigatório. A fábrica de imagens é executada usando scripts Azure PowerShell, para que possa ser executado manualmente, usando o Agendador de Tarefas do Windows, outros sistemas de CI/CD e assim por diante.

## <a name="create-a-lab-for-the-image-factory"></a>Crie um laboratório para a fábrica de imagens
O primeiro passo para montar a fábrica de imagens é criar um laboratório no Azure DevTest Labs. Este laboratório é o laboratório de fábrica de imagens onde criamos as máquinas virtuais e salvamos imagens personalizadas. Este laboratório é considerado como parte do processo geral da fábrica de imagens. Depois de criar um laboratório, certifique-se de salvar o nome, já que precisará dele mais tarde.

## <a name="scripts-and-templates"></a>Scripts e modelos
O próximo passo na adoção da fábrica de imagens para sua equipe é entender o que está disponível. Os scripts e modelos de fábrica de imagem estão disponíveis publicamente no [DevTest Labs GitHub Repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Aqui está um esboço das peças:

- Fábrica de Imagens. É a pasta raiz.
    - Configuração. As entradas para a fábrica de imagens
        - Imagens douradas. Esta pasta contém arquivos JSON que representam as definições de imagens personalizadas.
        - Labs.json. Arquivo onde as equipes se inscrevem para receber imagens personalizadas específicas.
- Scripts. O motor da fábrica de imagens.

Os artigos desta seção fornecem mais detalhes sobre esses scripts e modelos.

## <a name="create-an-azure-devops-team-project"></a>Crie um projeto de equipe do Azure DevOps
O Azure DevOps permite que você armazene o código-fonte, execute o Azure PowerShell em um só lugar. Você pode agendar corridas recorrentes para manter as imagens atualizadas. Existem boas facilidades para registrar os resultados para diagnosticar quaisquer problemas.  O uso do Azure DevOps não é um requisito, no entanto, você pode usar qualquer arreio/motor que possa se conectar ao Azure e executar o Azure PowerShell.

Se você tiver uma conta ou projeto DevOps existente, você gostaria de usar em vez disso, pule essa etapa.

Para começar, crie uma conta gratuita no Azure DevOps. Visite https://www.visualstudio.com/ e **selecione Comece gratuitamente** com **o Azure DevOps** (anteriormente VSTS). Você precisará escolher um nome de conta único e certifique-se de escolher gerenciar o código usando o Git. Uma vez criado, salve a URL para o projeto da sua equipe. Aqui está uma `https://<accountname>.visualstudio.com/MyFirstProject`URL de exemplo: .

## <a name="check-in-the-image-factory-to-git"></a>Verifique na fábrica de imagens para Git
Todos os PowerShell, modelos e configuração para a fábrica de imagens estão localizados no [repo público do DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). A maneira mais rápida de colocar o código em seu novo projeto de equipe é importar um repositório. Isso puxa todo o repositório de DevTest Labs (para que você obtenha docs extras e amostras).

1. Visite o projeto Azure DevOps que você criou na etapa anterior (URL parece **\//\<https: accountname>.visualstudio.com/MyFirstProject**).
2. Selecione **Importar um Repositório**.
3. Digite a **URL do clone** para `https://github.com/Azure/azure-devtestlab`o Rejogo DevTest Labs: .
4. Selecione **Importar**.

    ![Importar git repo](./media/set-up-devops-lab/import-git-repo.png)

Se você decidir apenas verificar exatamente o que é necessário (os arquivos de fábrica de imagens), siga os passos [aqui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) para clonar o repo do Git e pressione apenas os arquivos localizados no diretório **scripts/ImageFactory.**

## <a name="create-a-build-and-connect-to-azure"></a>Crie uma compilação e conecte-se ao Azure
Neste ponto, você tem os arquivos de origem armazenados em um repo Git no Azure DevOps. Agora, você precisa configurar um pipeline para executar o Azure PowerShell. Há muitas opções para fazer essas etapas. Neste artigo, você usa a definição de compilação para simplicidade, mas funciona com DevOps Build, DevOps Release (ambientes únicos ou múltiplos), outros mecanismos de execução como o Windows Task Scheduler ou qualquer outro arreio que possa executar o Azure PowerShell.

> [!NOTE]
> Um ponto importante a ter em mente que alguns dos arquivos PowerShell levam muito tempo para serem executados quando há muitas (10+) imagens personalizadas para criar. Os agentes de compilação/lançamento de DevOps hospedados gratuitos têm um tempo de 30 min, então você não pode usar o agente hospedado gratuito quando começar a construir muitas imagens. Este desafio de tempo out se aplica a qualquer arreio que você decidir usar, é bom verificar antecipadamente que você pode estender os tempos típicos para scripts Azure PowerShell de longa duração. No caso do Azure DevOps, você pode usar agentes hospedados pagos ou usar seu próprio agente de compilação.

1. Para começar, **selecione Configurar 'Configurar'** na página inicial do projeto DevOps:

    ![Botão de compilação de configuração](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique um **nome** para a compilação (por exemplo: Construir e entregar imagens para devTest Labs).
3. Selecione uma definição de compilação **vazia** e **selecione Aplicar** para criar sua compilação.
4. Nesta fase, você pode escolher **Hosted** para o agente de construção.
5. **Salve** a definição de compilação.

    ![Definição da compilação](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configure as variáveis de compilação
Para simplificar os parâmetros de linha de comando, encapsule os valores-chave que conduzem a fábrica de imagens a um conjunto de variáveis de compilação. Selecione a guia **Variáveis** e você verá uma lista de várias variáveis padrão. Aqui está a lista de variáveis para entrar no Azure DevOps:


| Nome de variável | Valor | Observações |
| ------------- | ----- | ----- |
| Configurationlocation | /Scripts/ImageFactory/Configuration | Este é o caminho completo no repositório para a pasta **Configuração.** Se você importou todo o repo acima, o valor à esquerda está correto. Caso contrário, atualize para apontar para o local de configuração. |
| DevTestLabName | MyImageFactory | O nome do laboratório no Azure DevTest Labs usado como fábrica para produzir imagens. Se você não tem um, crie um. Certifique-se de que o Laboratório está na mesma assinatura a que o ponto final do serviço tem acesso. |
| Retenção de imagens | 1 | O número de imagens que você deseja salvar de cada tipo. Defina o valor padrão como 1. |
| MachinePassword | ******* | A senha da conta de administração incorporada para as máquinas virtuais. Esta é uma conta passageira, então certifique-se de que é segura. Selecione o pequeno ícone de bloqueio à direita para garantir que é uma seqüência segura. |
| Nome do usuário da máquina | ImageFactoryUser | O nome de usuário da conta de administração incorporada para as máquinas virtuais. Esta é uma conta passageira. |
| StandardTimeoutMinutes | 30 | O tempo que devemos esperar pelas operações regulares do Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | O ID da assinatura onde o laboratório existe e que o ponto final do serviço tem acesso. |
| VMSize | Standard_A3 | O tamanho da máquina virtual para usar para a **etapa Criar.** As VMs criadas são transitórias. O tamanho deve ser o que está [habilitado para o laboratório.](devtest-lab-set-lab-policy.md) Confirme se há cota suficiente de núcleos de [assinatura.](../azure-resource-manager/management/azure-subscription-service-limits.md)

![Construir variáveis](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Conectar-se ao Azure
O próximo passo é configurar o diretor de serviço. Esta é uma identidade no Azure Active Directory que permite que o agente de compilação DevOps opere no Azure em nome do usuário. Para configurá-lo, comece a adicionar o primeiro Passo de Compilação do Azure PowerShell.

1. Selecione **Adicionar tarefa**.
2. Procure **o Azure PowerShell**.
3. Depois de encontrá-lo, **selecione Adicionar** para adicionar a tarefa à compilação. Quando você fizer isso, você verá a tarefa aparecer no lado esquerdo como adicionada.

![Configure a etapa do PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

A maneira mais rápida de configurar um diretor de serviço é deixar o Azure DevOps fazer isso por nós.

1. Selecione a **tarefa** que você acabou de adicionar.
2. Para **o tipo de conexão Azure,** escolha o **Gerenciador de recursos do Azure**.
3. Selecione o link **Gerenciar** para configurar o principal de serviço.

Para mais informações, consulte este [post no blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando você selecionar o link **Gerenciar,** você pousará no lugar certo no DevOps (segunda captura de tela na postagem do blog) para configurar a conexão com o Azure. Certifique-se de escolher **o Ponto Final de Serviço do Gerenciador de Recursos do Azure** ao configurar isso.

## <a name="complete-the-build-task"></a>Complete a tarefa de construção
Se você selecionar a tarefa de compilação, verá todos os detalhes no painel direito que devem ser preenchidos.

1. Primeiro, nomeie a tarefa de compilação: **Crie máquinas virtuais**.
2. Escolha o **principal de serviço** que você criou escolhendo o **Azure Resource Manager**
3. Escolha o **ponto final**do serviço .
4. Para **caminho de script,** selecione **... (elipse)** à direita.
5. Navegue até **MakeGoldenImageVMs.ps1** script.
6. Os parâmetros de script devem ser assim:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Complete a definição de compilação](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Enfileirar a compilação
Vamos verificar se você tem tudo configurado corretamente, enfileirando uma nova compilação. Enquanto a compilação estiver em execução, mude para o [portal Azure](https://portal.azure.com) e selecione em **Todas as Máquinas Virtuais** em seu laboratório de fábrica de imagens para confirmar que tudo está funcionando corretamente. Você deve ver três máquinas virtuais ser criadas no laboratório.

![VMs no laboratório](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Próximas etapas
O primeiro passo para configurar a fábrica de imagens baseada no Azure DevTest Labs está completo. No próximo artigo da série, você tem essas VMs generalizadas e salvas em imagens personalizadas. Então, você os distribui para todos os seus outros laboratórios. Veja o próximo artigo da série: [Salve imagens personalizadas e distribua em vários laboratórios](image-factory-save-distribute-custom-images.md).
