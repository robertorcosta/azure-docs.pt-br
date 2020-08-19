---
title: Guia de gerenciamento de custos para Azure Lab Services
description: Entenda as diferentes maneiras de exibir os custos dos serviços de laboratório.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98ce4d5e82d65d911984dc45615253ddcae33ae1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589836"
---
# <a name="cost-management-for-azure-lab-services"></a>Gerenciamento de custos para Azure Lab Services

O gerenciamento de custos pode ser dividido em duas áreas distintas: estimativa de custo e análise de custo.  A estimativa de custo ocorre ao configurar o laboratório para garantir que a estrutura inicial do laboratório se ajuste no orçamento esperado.  A análise de custo geralmente ocorre no final do mês para analisar os custos e determinar as ações necessárias para o próximo mês.

## <a name="estimate-the-lab-costs"></a>Estimar os custos do laboratório

Cada painel de laboratório tem uma seção **custos & cobrança** que apresenta uma estimativa aproximada do custo do laboratório para o mês.  A estimativa de custo resume o uso de horas com o número máximo de usuários pelo custo estimado por horas.  Para obter a estimativa mais precisa, configure o laboratório, incluindo a [agenda](how-to-create-schedules.md), e o painel refletirá o custo estimado.  

Essa estimativa pode não ser todos os custos possíveis, há alguns recursos que não estão incluídos.  O custo de preparação do modelo não é acrescentado à estimativa de custo.  Ele pode variar significativamente na quantidade de tempo necessária para criar o modelo. O custo para executar o modelo é o mesmo que o custo geral do laboratório por hora. Os custos da [Galeria de imagens compartilhadas](how-to-use-shared-image-gallery.md) não são incluídos no painel do laboratório, pois uma galeria pode ser compartilhada entre vários laboratórios.  Por fim, as horas incorridas quando o criador do laboratório inicia um computador são excluídas dessa estimativa.

> [!div class="mx-imgBorder"]
> ![Estimativa de custo do painel](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Analisar o uso de meses anteriores

A análise de custo é para revisar o uso de meses anteriores para ajudar a determinar qualquer ajuste para o laboratório.  A divisão dos custos no passado pode ser encontrada na análise de [custo da assinatura](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  No portal do Azure, você pode digitar "assinaturas" no campo de pesquisa superior e, em seguida, selecionar a opção assinaturas.  

> [!div class="mx-imgBorder"]
> ![Pesquisa de assinatura](./media/cost-management-guide/subscription-search.png)

Selecione a assinatura específica a ser revisada.

> [!div class="mx-imgBorder"]
> ![Seleção de assinatura](./media/cost-management-guide/subscription-select.png)

 Selecione "análise de custo" no painel esquerdo em gerenciamento de **custos**.

> [!div class="mx-imgBorder"]
> ![Análise de custo de assinatura](./media/cost-management-guide/subscription-cost-analysis.png)

Esse painel permitirá uma análise de custo detalhada, incluindo a capacidade de exportar para diferentes tipos de arquivo em uma agenda.  O gerenciamento de custos tem vários recursos para obter mais informações, consulte [visão geral da cobrança de gerenciamento de custos](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtragem pelo tipo de recurso: `microsoft.labservices/labaccounts` mostrará apenas o custo associado aos serviços de laboratório.

## <a name="understand-the-usage"></a>Entender o uso

Veja abaixo um exemplo da análise de custo.

> [!div class="mx-imgBorder"]
> ![Análise de custo de assinatura](./media/cost-management-guide/cost-analysis.png)

Por padrão, há seis colunas: recurso, tipo de recurso, local, nome do grupo de recursos, marcas, custo.  A coluna recurso contém as informações sobre a conta do laboratório, o nome do laboratório e a VM.  As linhas com conta do laboratório/nome do laboratório/padrão são o custo do laboratório, que pode ser visto na segunda e terceira linhas.  As VMs usadas terão um custo sob a conta do laboratório/nome do laboratório/padrão/nome da VM.  Neste exemplo, a adição da primeira linha com a segunda linha, começando com "aaalab/dockerlab", fornecerá o custo total para o laboratório "dockerlab" na conta do laboratório "aaalab".

Para obter informações da Galeria de imagens compartilhadas, altere o tipo de recurso para `Microsoft.Compute/Galleries` , que fornecerá o custo geral da Galeria de imagens.  As galerias de imagem de compartilhamento podem não aparecer nos custos, dependendo de onde a galeria está armazenada.

> [!NOTE]
> A Galeria de imagens compartilhadas está conectada à conta do laboratório.  Isso significa que vários laboratórios podem usar a mesma imagem.

## <a name="separating-costs"></a>Separando custos

Algumas universidades usaram a conta de laboratório e o grupo de recursos como maneiras de separar as diferentes classes.  Cada classe terá sua própria conta de laboratório e grupo de recursos. No painel análise de custo, adicione um filtro com base no nome do grupo de recursos com o nome do grupo de recursos apropriado para a classe e somente os custos dessa classe ficarão visíveis.  Isso permite uma delineação mais clara entre as diferentes classes ao exibir os custos.  O recurso de [exportação agendada](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) da análise de custo permite que os custos de cada classe sejam baixados em arquivos separados.

## <a name="managing-costs"></a>Gerenciando os custos

Dependendo do tipo de classe, há maneiras de gerenciar os custos para reduzir a execução das VMs sem um aluno usando a máquina.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Maximizar o controle de custo com as configurações de desligamento automático

Os recursos de controle de custos de desligamento automático permitem que você impeça o desperdício de horas de uso da máquina virtual dentro dos laboratórios. A combinação dos seguintes três recursos automáticos de desligamento e desconexão captura a maioria dos casos em que os usuários deixam acidentalmente suas máquinas virtuais em execução:

> [!div class="mx-imgBorder"]
> ![Análise de custo de assinatura](./media/cost-management-guide/auto-shutdown-disconnect.png)

Essas configurações podem ser definidas no nível da conta do laboratório e no nível do laboratório. Se as configurações estiverem habilitadas no nível da conta do laboratório, elas serão aplicadas a todos os laboratórios dentro da conta do laboratório. Para todas as novas contas de laboratório, essas configurações são ativadas por padrão. 

#### <a name="details-about-auto-shutdown-settings"></a>Detalhes sobre as configurações de desligamento automático

* Desconectar automaticamente os usuários de máquinas virtuais que o sistema operacional considera ocioso (somente Windows).

    > [!NOTE]
    > Essa configuração só está disponível para máquinas virtuais do Windows.

    Quando a configuração é ativada, o usuário é desconectado de qualquer computador no laboratório quando o sistema operacional Windows considera a sessão como ociosa (incluindo as máquinas virtuais de modelo). A [definição de ociosidade do sistema operacional Windows](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) usa dois critérios: 

    * Ausência do usuário – sem entrada de teclado ou mouse.
    * Falta de consumo de recursos – todos os processadores e todos os discos estavam ociosos por um determinado% do tempo

    Os usuários verão uma mensagem como esta dentro da máquina virtual antes de serem desconectadas: 

    > [!div class="mx-imgBorder"]
    > ![Análise de custo de assinatura](./media/cost-management-guide/idle-timer-expired.png)
    
    A máquina virtual ainda está em execução quando o usuário está desconectado. Se o usuário se reconectar à máquina virtual entrando, o Windows ou os arquivos que estavam abertos ou não salvos no trabalho anterior à desconexão ainda estarão lá. Nesse estado, como a máquina virtual está em execução, ela ainda conta como ativa e acumula o custo. 
    
    Para desligar automaticamente as máquinas virtuais ociosas do Windows que estão desconectadas, use a combinação de **usuários desconectados quando as máquinas virtuais estiverem ociosas** e **desligar as máquinas virtuais quando os usuários desconectarem** as configurações.

    Por exemplo, se você definir as configurações da seguinte maneira:
    
    * Desconectar usuários quando as máquinas virtuais estiverem ociosas – 15 minutos após o estado ocioso ser detectado.
    * Desligue as máquinas virtuais quando os usuários desconectarem – 5 minutos após a desconexão do usuário.
    
    As máquinas virtuais do Windows serão desligadas automaticamente 20 minutos depois que o usuário parar de usá-las. 
    
    > [!div class="mx-imgBorder"]
    > ![Análise de custo de assinatura](./media/cost-management-guide/vm-idle-diagram.png)
* Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem (Windows & Linux).
    
    Essa configuração dá suporte a máquinas virtuais Windows e Linux. Quando essa configuração estiver ativada, o desligamento automático ocorrerá quando:
    
    * Para Windows, a conexão Área de Trabalho Remota (RDP) é desconectada.
    * Para o Linux, a conexão SSH é desconectada.
    
    > [!NOTE]
    > Há suporte apenas para [distribuições e versões específicas do Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) .
    
    Você pode especificar por quanto tempo as máquinas virtuais devem aguardar até que o usuário se reconecte antes de desligar automaticamente. 
* Desligar automaticamente as máquinas virtuais que são iniciadas, mas os usuários não se conectam.
     
    Dentro de um laboratório, um usuário pode iniciar uma máquina virtual, mas nunca se conectar a ela. Por exemplo:
    
    * Uma agenda no laboratório inicia todas as máquinas virtuais para uma sessão de classe, mas alguns alunos não aparecem e não se conectam a seus computadores.  
    * Um usuário inicia uma máquina virtual, mas se esquece de conectar. 
    
    A configuração "desligar máquinas virtuais quando os usuários não se conectam" detectará esses casos e desligará automaticamente as máquinas virtuais.  
    
Para obter informações sobre como configurar e habilitar o desligamento automático de VMs na desconexão, consulte estes artigos:

* [Configurar o desligamento automático de VMs na configuração de desconexão para uma conta de laboratório](how-to-configure-lab-accounts.md)
* [Habilitar desligamento automático de VMs ao desconectar](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Cota vs. hora agendada

Entender o [tempo de cota](classroom-labs-concepts.md#quota) vs [agendado](classroom-labs-concepts.md#schedules) , permitirá que o proprietário do laboratório configure o laboratório para se adequar melhor às necessidades do professor e dos alunos.  A hora agendada é uma hora definida em que todas as VMs de estudante foram iniciadas e estão disponíveis para conexão.  Normalmente, o agendamento é usado na situação em que todos os alunos terão sua própria VM e estão seguindo as instruções do professor em um determinado momento durante o dia, como as horas da classe.  A desvantagem é que todas as VMs dos alunos são iniciadas e estão acumulando custos, mesmo se um aluno não fizer logon na VM.  O tempo de cota é o tempo alocado para cada aluno que ele pode usar a seu critério e, muitas vezes, é usado para estudar de forma independente. As VMs não são iniciadas até que o aluno inicie a VM.  

Um laboratório pode usar o tempo de cota, o horário agendado ou uma combinação de ambos. Se uma classe não precisar da hora agendada, use apenas o tempo de cota para o uso mais eficaz das VMs.

### <a name="scheduled-event---stop-only"></a>Evento agendado-parar somente

No agendamento, você pode adicionar um tipo de evento de parada somente, o que irá parar todos os computadores em um momento específico.  Alguns proprietários de laboratório definiram um evento de parada somente para todos os dias à meia-noite para reduzir o custo e o uso de cota quando um aluno esquecer de desligar a VM que está usando.  A desvantagem desse tipo de evento é que todas as VMs serão desligadas mesmo se o aluno estiver usando a VM.

### <a name="other-costs-related-to-labs"></a>Outros custos relacionados a laboratórios 

Há custos que não são lançados nos serviços de laboratório, mas podem ser vinculados a um serviço de laboratório.  Uma galeria de imagens compartilhadas pode ser conectada a laboratórios, mas não é mostrada nos custos dos serviços de laboratório e tem custos.  Para ajudar a manter os custos gerais baixos, você deve remover qualquer imagem não utilizada da galeria, pois as imagens têm um custo de armazenamento herdado.  Os laboratórios podem ter conexões com outros recursos do Azure por uma rede virtual (VNet) quando um laboratório é removido, você deve remover a VNet e os outros recursos.

## <a name="conclusion"></a>Conclusão

Espero que as informações acima forneçam uma melhor compreensão dos custos de uso e de como usar as ferramentas para reduzir os custos em excesso.
