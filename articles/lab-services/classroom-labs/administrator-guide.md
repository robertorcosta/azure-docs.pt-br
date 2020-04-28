---
title: Guia do Azure Lab Services do administrador | Microsoft Docs
description: Este guia ajuda os administradores que criam e gerenciam contas de laboratório usando Azure Lab Services.
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
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 307ca08e733417efc9496415a09a0898fe10393e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183459"
---
# <a name="azure-lab-services---administrator-guide"></a>Guia do Azure Lab Services do administrador
Os administradores de ti (tecnologia da informação) que gerenciam os recursos de nuvem de uma Universidade normalmente são responsáveis por configurar a conta de laboratório para sua escola. Quando uma conta de laboratório é configurada, os administradores ou educadores criam laboratórios de sala de aula que estão contidos na conta do laboratório. Este artigo fornece uma visão geral de alto nível dos recursos do Azure envolvidos e as diretrizes para criá-los.

![Exibição de alto nível dos recursos do Azure em uma conta de laboratório](../media/administrator-guide/high-level-view.png)

- Os laboratórios de sala de aula são hospedados em uma assinatura do Azure de Propriedade do Azure Lab Services.
- As contas de laboratório, a Galeria de imagens compartilhadas e as versões de imagem são hospedadas em sua assinatura.
- Você pode ter sua conta de laboratório e a Galeria de imagens compartilhadas no mesmo grupo de recursos. Neste diagrama, eles estão em grupos de recursos diferentes. 

## <a name="subscription"></a>Subscription
Sua universidade tem uma ou mais assinaturas do Azure. Uma assinatura é usada para gerenciar a cobrança e a segurança de todas as resources\services do Azure que são usadas dentro dela, incluindo contas de laboratório.

A relação entre uma conta de laboratório e sua assinatura é importante porque:

- A cobrança é relatada por meio da assinatura que contém a conta do laboratório.
- Você pode conceder aos usuários o acesso de locatário de Azure Active Directory (AD) da assinatura a Azure Lab Services. Você pode adicionar um usuário como uma conta de laboratório owner\contributor, um criador de laboratório de sala de aula ou um proprietário de laboratório de sala de aula.

Os laboratórios de sala de aula e suas VMs (máquinas virtuais) são gerenciados e hospedados para você em uma assinatura de propriedade de Azure Lab Services.

## <a name="resource-group"></a>Resource group
Uma assinatura contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos do Azure que são usados juntos na mesma solução.  

Ao criar uma conta de laboratório, você deve configurar o grupo de recursos que contém a conta do laboratório. 

Um grupo de recursos também é necessário ao criar uma [Galeria de imagens compartilhada](#shared-image-gallery). Você pode optar por colocar sua conta de laboratório e a Galeria de imagens compartilhadas em dois grupos de recursos separados, o que é típico se você planeja compartilhar a Galeria de imagens em diferentes soluções. Ou, você pode optar por colocá-los no mesmo grupo de recursos.

Ao criar uma conta de laboratório, você pode criar e anexar automaticamente uma galeria de imagens compartilhada ao mesmo tempo.  Essa opção resulta na conta de laboratório e na Galeria de imagens compartilhada que está sendo criada em grupos de recursos separados. Você verá esse comportamento ao usar as etapas descritas neste tutorial: [Configurar a Galeria de imagens compartilhadas no momento da criação da conta do laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A imagem na parte superior deste artigo também usa essa configuração. 

É recomendável investir tempo de investimento antecipadamente para planejar a estrutura de seus grupos de recursos, pois *não* é possível alterar o grupo de recursos de uma galeria de imagens compartilhada ou de uma conta de laboratório após sua criação. Se você precisar alterar o grupo de recursos para esses recursos, será necessário excluir e recriar sua conta de laboratório e/ou galeria de imagens compartilhadas.

## <a name="lab-account"></a>Conta de laboratório

Uma conta de laboratório serve como um contêiner para um ou mais laboratórios de sala de aula. Ao começar a usar o Azure Lab Services, é comum ter apenas uma única conta de laboratório. À medida que o uso do laboratório for dimensionado, você poderá optar por criar mais contas de laboratório.

A lista a seguir realça os cenários em que mais de uma conta de laboratório pode ser benéfica:

- **Gerenciar requisitos de política diferentes em laboratórios de sala de aula**

    Ao configurar uma conta de laboratório, você define as políticas que se aplicam a *todos os* laboratórios de sala de aula na conta do laboratório, como:
    - A rede virtual do Azure com recursos compartilhados que o laboratório da sala de aula pode acessar. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso a um conjunto de dados compartilhado em uma rede virtual.
    - As imagens de VM (máquina virtual) que os laboratórios de sala de aula podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam acessar o [VM de ciência de dados para a imagem do Marketplace do Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) .

    Se você tiver laboratórios de sala de aula com requisitos de política exclusivos uns dos outros, poderá ser benéfico criar contas de laboratório separadas para gerenciar esses laboratórios de sala de aula separadamente.

- **Orçamento separado por conta de laboratório**
  
    Em vez de relatar todos os custos de laboratório de sala de aula por meio de uma única conta de laboratório, talvez seja necessário um orçamento mais claramente separado. Por exemplo, você pode criar contas de laboratório para o departamento de matemática da sua universidade, o departamento de ciência da computação e assim por diante, para separar o orçamento entre os departamentos.  Em seguida, você pode exibir o custo de cada conta de laboratório individual usando o [Gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isole os laboratórios piloto do active\production Labs**
  
    Você pode ter casos em que deseja fazer alterações de política do piloto para uma conta de laboratório sem afetar potencialmente os active\production Labs. Nesse tipo de cenário, a criação de uma conta de laboratório separada para fins de piloto permite isolar as alterações. 

## <a name="classroom-lab"></a>Laboratório de sala de aula

Um laboratório de sala de aula contém VMs (máquinas virtuais) que são atribuídas a um único aluno.  Em geral, você pode esperar:

- Ter um laboratório de sala de aula para cada classe.
- Crie um novo conjunto de laboratórios de sala de aula a cada semestre (ou para cada período de tempo em que sua classe é oferecida). Normalmente, para as classes que têm as mesmas necessidades de imagem, você deve usar uma [Galeria de imagens compartilhadas](#shared-image-gallery) para reutilizar imagens em laboratórios e semestrees.

Considere os seguintes pontos ao determinar como estruturar seus laboratórios de sala de aula:

- **Todas as VMs em um laboratório de sala de aula são implantadas com a mesma imagem que é publicada**

    Como resultado, se você tiver uma classe que exija que imagens de laboratório diferentes sejam publicadas ao mesmo tempo, os laboratórios de sala de aula separados deverão ser criados para cada um.
  
- **A cota de uso é definida no nível de laboratório e se aplica a todos os usuários no laboratório**

    Para definir cotas diferentes para usuários, você deve criar laboratórios de sala de aula separados. No entanto, é possível adicionar mais horas a um usuário específico depois de definir a cota.
  
- **A agenda de inicialização ou desligamento é definida no nível de laboratório e se aplica a todas as VMs no laboratório**

    Semelhante ao ponto anterior, se você precisar definir agendamentos diferentes para os usuários, precisará criar laboratórios de sala de aula separados.

Por padrão, cada laboratório de sala de aula terá sua própria rede virtual.  Se você tiver o emparelhamento vnet habilitado, cada laboratório da sala de aula terá sua própria sub-rede emparelhada com a rede virtual especificada.

## <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

Uma galeria de imagens compartilhada é anexada a uma conta de laboratório e serve como um repositório central para armazenar imagens. Uma imagem é salva na Galeria quando um professor opta por exportar de uma VM (máquina virtual) de modelo de um laboratório de sala de aula. Cada vez que um professor faz alterações na VM de modelo e exporta, novas versões da imagem são salvas mantendo as versões anteriores.

Os instrutores podem publicar uma versão de imagem da Galeria de imagens compartilhadas ao criar um novo laboratório de sala de aula. Embora a Galeria armazene várias versões de uma imagem, educadores só podem selecionar a versão mais recente durante a criação do laboratório.

A Galeria de imagens compartilhadas é um recurso opcional que talvez não seja necessário imediatamente ao começar com apenas alguns laboratórios de sala de aula. No entanto, usar a Galeria de imagens compartilhadas tem muitos benefícios que são úteis à medida que você dimensiona para ter mais laboratórios de sala de aula:

- **Permite salvar e gerenciar versões de uma imagem de VM de modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração e assim por diante) em uma imagem da galeria do Marketplace público.  Por exemplo, é comum que educadores exijam que software\tooling diferentes sejam instalados. Em vez de exigir que os alunos instalem manualmente esses pré-requisitos por conta própria, diferentes versões da imagem de VM do modelo podem ser exportadas para uma galeria de imagens compartilhadas. Essas versões de imagem podem ser usadas ao criar novos laboratórios de sala de aula.
- **Habilita sharing\reuse de imagens de VM de modelo em laboratórios de sala de aula**

    Você pode salvar e reutilizar uma imagem para que não precise configurar a imagem do zero toda vez que criar um novo laboratório de sala de aula. Por exemplo, se várias classes estiverem sendo oferecidas que precisam da mesma imagem, essa imagem só precisará ser criada uma vez e exportada para a Galeria de imagens compartilhadas para que possa ser compartilhada entre os laboratórios da sala de aula.
- **Garante a disponibilidade da imagem por meio da replicação**

    Quando você salva na Galeria de imagens compartilhadas de um laboratório de sala de aula, sua imagem é replicada automaticamente para outras [regiões na mesma geografia](https://azure.microsoft.com/global-infrastructure/regions/). Caso haja uma interrupção para uma região, a publicação da imagem em seu laboratório de sala de aula não será afetada, pois uma réplica de imagem de outra região pode ser usada.  A publicação de VMs de várias réplicas também pode ajudar com o desempenho.

Para agrupar logicamente as imagens compartilhadas, você tem algumas opções:

- Criar várias galerias de imagens compartilhadas. Cada conta de laboratório só pode se conectar a uma galeria de imagens compartilhadas, portanto, essa opção também exigirá que você crie várias contas de laboratório.
- Ou, você pode usar uma única Galeria de imagens compartilhadas que é compartilhada por várias contas de laboratório. Nesse caso, cada conta de laboratório pode habilitar apenas as imagens aplicáveis aos laboratórios de sala de aula que ela contém.

## <a name="naming"></a>Nomenclatura

Ao começar a usar o Azure Lab Services, recomendamos que você estabeleça convenções de nomenclatura para grupos de recursos, contas de laboratório, laboratórios de sala de aula e a Galeria de imagens compartilhadas. Embora as convenções de nomenclatura estabelecidas sejam exclusivas às necessidades da sua organização, a tabela a seguir descreve as diretrizes gerais.

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens compartilhadas | \<\>-\<ambiente\>de nome curto da organização-RG<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte</li><li>O **ambiente** identifica o ambiente para o recurso, como piloto ou produção</li><li>**RG** significa o tipo de recurso: grupo de recursos.</li></ul> | contosouniversitylabs-RG<br/>contosouniversitylabs-Pilot-RG<br/>contosouniversitylabs-prod-RG |
| Conta de laboratório | Contém um ou mais laboratórios | \<\>-\<ambiente\>de nome curto da organização-la<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte</li><li>O **ambiente** identifica o ambiente para o recurso, como piloto ou produção</li><li>**La** significa o tipo de recurso: conta de laboratório.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-piloto-la<br/>sciencedeptlabs-prod-la |
| Laboratório de sala de aula | Contém uma ou mais VMs |\<\>-nome\<da classe identificador\>-\<do professor do período de tempo\><ul><li>**Nome da classe** identifica o nome da classe à qual o laboratório dá suporte.</li><li>**Período de tempo** identifica o período de tempo no qual a classe é oferecida.</li>O **identificador de educação** identifica o educador que possui o laboratório.</li></ul> | CS1234-fall2019-davibarros<br/>CS1234-spring2019-davibarros |
| Galeria de imagens compartilhadas | Contém uma ou mais versões de imagem de VM | \<Galeria de nomes\>curtos da organização | contosouniversitylabsgallery |

Para obter mais informações sobre como nomear outros recursos do Azure, consulte [convenções de nomenclatura para recursos do Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Ao configurar os recursos do Azure Lab Services, você precisa fornecer uma região (ou local) do data center que hospedará o recurso. Aqui estão mais detalhes sobre como a região afeta cada uma das origens envolvidas na configuração de um laboratório.

### <a name="resource-group"></a>Resource group

A região especifica a data center em que as informações sobre o grupo de recursos são armazenadas. Os recursos do Azure contidos no grupo de recursos podem estar em regiões diferentes do seu pai.

### <a name="lab-account"></a>Conta de laboratório

O local de uma conta de laboratório indica a região em que esse recurso existe.  

### <a name="classroom-lab"></a>Laboratório de sala de aula

O local em que um laboratório de sala de aula existe varia com base nos seguintes fatores:

  - **A conta de laboratório está emparelhada com uma rede virtual (VNet)**
  
    Uma conta de laboratório pode ser [emparelhada com uma VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando estiverem na mesma região.  Quando uma conta de laboratório é emparelhada com uma VNet, os laboratórios de sala de aula são criados automaticamente na mesma região que a conta de laboratório e a VNet.

    > [!NOTE]
    > Quando uma conta de laboratório é emparelhada com uma VNet, a configuração para **permitir que o criador** do laboratório selecione o local do laboratório está desabilitada. Informações adicionais podem ser encontradas sobre essa configuração no artigo: [permitir que o criador do laboratório escolha o local do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nenhuma VNet está emparelhada ***e*** os criadores de laboratório não têm permissão para escolher o local do laboratório**
  
    Quando não há **nenhuma** VNet emparelhada com a conta de laboratório *e* os [criadores de laboratório **não** têm permissão para escolher o local do laboratório, os laboratórios da](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)sala de aula são criados automaticamente em uma região com capacidade de VM disponível.  Especificamente, Azure Lab Services procura disponibilidade em [regiões que estão na mesma geografia que a conta do laboratório](https://azure.microsoft.com/global-infrastructure/regions).

  - **Nenhuma VNet está emparelhada ***e*** os criadores de laboratório têm permissão para escolher o local do laboratório**
       
    Quando não há **uma** VNet emparelhada e os [criadores de laboratório têm permissão para escolher o local do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), os locais que podem ser selecionados pelo criador do laboratório se baseiam na capacidade disponível.

> [!NOTE]
> Para ajudar a garantir que haja capacidade de VM suficiente para uma região, é importante que você primeiro solicite a capacidade por meio da conta de laboratório ou ao criar o laboratório.

Uma regra geral é definir a região de um recurso para uma que seja mais próxima de seus usuários. Para laboratórios de sala de aula, isso significa criar o laboratório de sala de aula mais próximo de seus alunos. Para cursos online em que os alunos estão localizados em todo o mundo, você precisa usar o melhor julgamento para criar um laboratório de sala de aula que esteja localizado centralmente. Ou, dividir uma classe em vários laboratórios de sala de aula com base na região dos seus alunos.

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

A região indica a região de origem em que a primeira versão da imagem é armazenada antes de ser replicada automaticamente para as regiões de destino.

## <a name="vm-sizing"></a>Dimensionamento de VM

Quando os administradores ou os criadores de laboratório criam um laboratório de sala de aula, eles podem escolher entre os seguintes tamanhos de VM com base nas necessidades de sua sala de aula. Lembre-se de que os tamanhos de computação disponíveis dependem da região em que sua conta de laboratório está localizada:

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequena| <ul><li>2 núcleos</li><li>3,5 GB DE RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Médio | <ul><li>4 núcleos</li><li>7 GB DE RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. |
| Médio (virtualização aninhada) | <ul><li>4 núcleos</li><li>16 GB de RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise.  Esse tamanho também dá suporte à virtualização aninhada. |
| grande | <ul><li>8 núcleos</li><li>32 GB DE RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande.  Esse tamanho também dá suporte à virtualização aninhada. |
| GPU pequena (visualização) | <ul><li>6 núcleos</li><li>56 GB DE RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. |
| GPU pequena (computação) | <ul><li>6 núcleos</li><li>56 GB DE RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Esse tamanho é mais adequado para aplicativos com uso intensivo de computadores, como inteligência artificial e aprendizado profundo. |
| GPU média (visualização) | <ul><li>12 núcleos</li><li>112 GB DE RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerenciar identidade

Usando o [controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview), as seguintes funções podem ser atribuídas para conceder acesso a contas de laboratório e laboratórios de sala de aula:

- **Proprietário da conta do laboratório**

    O administrador que cria a conta de laboratório é adicionado automaticamente à função de **proprietário** da conta do laboratório.  Um administrador ao qual a função de **proprietário** foi atribuída pode:
     - Altere as configurações da conta do laboratório.
     - Conceder a outros administradores acesso à conta de laboratório como proprietários ou colaboradores.
     - Dê ao professor acesso a laboratórios de sala de aula como criadores, proprietários ou colaboradores.
     - Crie e gerencie todos os laboratórios de sala de aula dentro da conta do laboratório.

- **Colaborador de conta de laboratório**

    Um administrador ao qual a função **colaborador** foi atribuída pode:
    - Altere as configurações da conta do laboratório.
    - Crie e gerencie todos os laboratórios de sala de aula na conta do laboratório.

    No entanto, eles *não podem* conceder a outros usuários acesso a contas de laboratório ou laboratórios de sala de aula.

- **Criador de laboratório de sala de aula**

    Para criar laboratórios de sala de aula em uma conta de laboratório, um professor deve ser membro da função de **criador de laboratório** .  Quando um professor cria um laboratório de sala de aula, ele é adicionado automaticamente como um proprietário do laboratório.  Consulte o tutorial sobre como [Adicionar um usuário à função de **criador de laboratório** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Owner\contributor de laboratório de sala de aula**
  
    Um educador pode exibir e alterar as configurações de um laboratório de sala de aula quando eles são membros do **proprietário** ou da função de **colaborador** de um laboratório; Eles também devem ser membros da função de **leitor** da conta do laboratório.

    Uma diferença importante entre as funções de **proprietário** e **colaborador** de um laboratório é que um colaborador *não pode* conceder a outros usuários acesso para gerenciar os proprietários somente de laboratório que podem dar a outros usuários acesso para gerenciar o laboratório.

    Além disso, um professor *não pode* criar novos laboratórios de sala de aula, a menos que eles também sejam membros da função de **criador do laboratório** .

- **Galeria de imagens compartilhadas**

    Quando você anexa uma galeria de imagens compartilhada a uma conta de laboratório, a conta de laboratório owners\contributors e Lab creators\owners\contributors recebem automaticamente o acesso para exibir e salvar imagens na galeria.

Aqui estão algumas dicas para ajudar na atribuição de funções:
   - Normalmente, somente os administradores devem ser membros do **proprietário** ou das funções de **colaborador** de uma conta de laboratório; Você pode ter mais de um owner\contributor.
   - Para dar a um professor a capacidade de criar novos laboratórios de sala de aula e gerenciar os laboratórios que eles criam; Você só precisa atribuir acesso à função de **criador de laboratório** .
   - Para dar a um professor a capacidade de gerenciar laboratórios de sala de aula específicos, mas *não* a capacidade de criar novos laboratórios; Você deve atribuir acesso à função de **proprietário** ou **colaborador** para cada um dos laboratórios de sala de aula que eles irão gerenciar.  Por exemplo, talvez você queira permitir que um professor e um assistente de ensino coexistam um laboratório de sala de aula.  Consulte o guia sobre como [Adicionar um usuário como um proprietário a um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services

O preço para Azure Lab Services é descrito no seguinte artigo: [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Você também precisa considerar o preço da Galeria de imagens compartilhadas se planeja usá-la para armazenar e gerenciar versões de imagem. 

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

Criar uma galeria de imagens compartilhada e anexá-la à sua conta de laboratório é gratuita. Os custos não são incorridos até que você salve uma versão de imagem na galeria. Normalmente, o preço para usar uma galeria de imagens compartilhadas é razoavelmente insignificante, mas é importante entender como ele é calculado, pois não está incluído no preço de Azure Lab Services.  

#### <a name="storage-charges"></a>Encargos de armazenamento

Para armazenar versões de imagem, uma galeria de imagens compartilhadas usa discos gerenciados por HDD padrão. O tamanho do disco gerenciado por HDD que é usado depende do tamanho da versão da imagem que está sendo armazenada. Consulte o artigo a seguir para exibir o preço: [preços do Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Encargos de saída de rede e replicação

Quando você salva uma versão de imagem usando a VM (máquina virtual) de modelo de um laboratório de sala de aula, Azure Lab Services primeiro a armazena em uma região de origem e, em seguida, replica automaticamente a versão da imagem de origem para uma ou mais regiões de destino. É importante observar que Azure Lab Services replica automaticamente a versão da imagem de origem para todas as regiões de destino [na geografia](https://azure.microsoft.com/global-infrastructure/regions/) onde o laboratório da sala de aula está localizado. Por exemplo, se o laboratório da sala de aula estiver na geografia dos EUA, uma versão de imagem será replicada para cada uma das oito regiões existentes nos EUA.

Um encargo de egresso de rede ocorre quando uma versão de imagem é replicada da região de origem para outras regiões de destino. O valor cobrado é baseado no tamanho da versão da imagem quando os dados da imagem são transferidos inicialmente de saída da região de origem.  Para obter detalhes de preços, consulte o seguinte artigo: [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Os clientes de [soluções educacionais](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) podem ser renunciados ao pagamento de encargos de egresso. Fale com seu gerente de conta para saber mais.  Para obter mais informações, consulte **consulte a seção de perguntas frequentes** no documento vinculado, especificamente a pergunta "quais programas de transferência de dados existem para clientes acadêmicos e como posso me qualificar?".

#### <a name="pricing-example"></a>Exemplo de preço

Para recapitular o preço descrito acima, vejamos um exemplo de como salvar nossa imagem de VM de modelo na Galeria de imagens compartilhadas. Suponha os seguintes cenários:

- Você tem uma imagem de VM personalizada.
- Você está salvando duas versões da imagem.
- Seu laboratório está nos EUA, que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho; Como resultado, o preço do disco gerenciado por HDD é $1.54 por mês.

O custo total é estimado como:

Número de imagens × número de versões × número de réplicas × preço de disco gerenciado

Neste exemplo, o custo é:

1 imagem personalizada (32 GB) x 2 versões x 8 regiões dos EUA x $1.54 = $24.64 por mês

#### <a name="cost-management"></a>Gerenciamento de custo

É importante que o administrador da conta do laboratório gerencie os custos, excluindo rotineiramente as versões de imagem desnecessárias da galeria. 

Você não deve excluir a replicação para regiões específicas como uma maneira de reduzir os custos (essa opção existe na Galeria de imagens compartilhadas). As alterações de replicação podem ter efeitos adversos na capacidade do serviço de laboratório do Azure de publicar VMs de imagens salvas em uma galeria de imagens compartilhadas.

## <a name="next-steps"></a>Próximas etapas

Consulte o tutorial para obter instruções passo a passo para criar uma conta de laboratório e um laboratório: [Guia de configuração](tutorial-setup-lab-account.md)
