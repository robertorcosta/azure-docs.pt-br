---
title: Azure Lab Services - guia do administrador | Microsoft Docs
description: Este guia ajuda os administradores que criam e gerenciam contas de laboratório usando o Azure Lab Services.
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: ce9f8ee592c1fb2f7ac98339bbd14ce57440bc1a
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815699"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - guia do administrador
Os administradores de TI (tecnologia da informação) que gerenciam os recursos de nuvem de uma universidade normalmente são responsáveis por configurar a conta de laboratório da instituição. Quando uma conta de laboratório é configurada, os administradores ou educadores criam laboratórios de sala de aula que estão contidos na conta de laboratório. Este artigo fornece uma visão geral de alto nível dos recursos do Azure envolvidos e as diretrizes para criá-los.

![Exibição de alto nível dos recursos do Azure em uma conta de laboratório](../media/administrator-guide/high-level-view.png)

- Os laboratórios de sala de aula são hospedados em uma assinatura do Azure de propriedade do Azure Lab Services.
- As contas de laboratório, a galeria de imagens compartilhadas e as versões de imagem são hospedadas em sua assinatura.
- Você pode ter sua conta de laboratório e a galeria de imagens compartilhadas no mesmo grupo de recursos. Neste diagrama, elas estão em grupos de recursos diferentes. 

## <a name="subscription"></a>Subscription
Sua universidade tem uma ou mais assinaturas do Azure. Uma assinatura é usada para gerenciar a cobrança e a segurança de todos os recursos/serviços do Azure que são usados dentro dela, incluindo contas de laboratório.

A relação entre uma conta de laboratório e sua assinatura é importante porque:

- A cobrança é relatada por meio da assinatura que contém a conta de laboratório.
- Você pode conceder aos usuários o acesso de locatário do Azure Active Directory (AD) da assinatura ao Azure Lab Services. Você pode adicionar um usuário como proprietário/colaborador de uma conta de laboratório, um criador de laboratório de sala de aula ou um proprietário de laboratório de sala de aula.

Os laboratórios de sala de aula e suas VMs (máquinas virtuais) são gerenciados e hospedados para você em uma assinatura de propriedade do Azure Lab Services.

## <a name="resource-group"></a>Resource group
Uma assinatura contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos do Azure que são usados em conjunto na mesma solução.  

Ao criar uma conta de laboratório, você deve configurar o grupo de recursos que contém a conta de laboratório. 

Um grupo de recursos também é necessário ao criar uma [galeria de imagens compartilhadas](#shared-image-gallery). Você pode optar por colocar sua conta de laboratório e a galeria de imagens compartilhadas em dois grupos de recursos diferentes, o que é comum se você planeja compartilhar a galeria de imagens em diferentes soluções. Você também pode optar por colocá-las no mesmo grupo de recursos.

Ao criar uma conta de laboratório, você pode criar e anexar automaticamente uma galeria de imagens compartilhadas ao mesmo tempo.  Essa opção resulta na conta de laboratório e na galeria de imagens compartilhadas sendo criadas em grupos de recursos diferentes. Você verá esse comportamento ao usar as etapas descritas neste tutorial: [Configurar a galeria de imagens compartilhadas no momento da criação da conta de laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A imagem na parte superior deste artigo também usa essa configuração. 

É recomendável investir tempo para planejar a estrutura de seus grupos de recursos, já que *não* é possível alterar o grupo de recursos de uma conta de laboratório ou da galeria de imagens compartilhadas após sua criação. Se você precisar alterar o grupo de recursos para esses recursos, será necessário excluir e recriar sua conta de laboratório e/ou galeria de imagens compartilhadas.

## <a name="lab-account"></a>Conta de laboratório

Uma conta de laboratório serve como um contêiner para um ou mais laboratórios de sala de aula. Ao começar a usar o Azure Lab Services, é comum ter apenas uma única conta de laboratório. À medida que o uso do laboratório for ampliado, você poderá optar por criar mais contas de laboratório.

A lista a seguir destaca os cenários em que ter mais de uma conta de laboratório pode ser vantajoso:

- **Gerenciar requisitos de política diferentes em laboratórios de sala de aula**

    Ao configurar uma conta de laboratório, você define políticas que se aplicam a *todos* os laboratórios de sala de aula na conta de laboratório, como:
    - A rede virtual do Azure com recursos compartilhados que o laboratório de sala de aula pode acessar. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso a um conjunto de dados compartilhado em uma rede virtual.
    - As imagens de VM (máquina virtual) que os laboratórios de sala de aula podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam acessar a imagem da [Máquina Virtual de Ciência de Dados para Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) do Marketplace.

    Se você tiver laboratórios de sala de aula com requisitos de política diferentes uns dos outros, poderá ser vantajoso criar contas de laboratório diferentes para gerenciar esses laboratórios de sala de aula separadamente.

- **Separar orçamento por conta de laboratório**
  
    Em vez de relatar todos os custos do laboratório de sala de aula por meio de uma única conta de laboratório, talvez seja necessário um orçamento mais claramente separado. Por exemplo, você pode criar contas de laboratório para o departamento de matemática da sua universidade, para o departamento de ciência da computação e assim por diante, para separar o orçamento de cada departamento.  Em seguida, você pode exibir o custo de cada conta de laboratório individual usando o [Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isolar laboratórios piloto de laboratórios ativos/em produção**
  
    Você pode ter casos em que deseja fazer alterações de política do piloto para uma conta de laboratório sem potencialmente afetar os laboratórios ativos/em produção. Nesse tipo de cenário, a criação de uma conta de laboratório separada para fins de piloto permite isolar as alterações. 

## <a name="classroom-lab"></a>Laboratório de sala de aula

Um laboratório de sala de aula contém VMs (máquinas virtuais) que são atribuídas a um único aluno.  Em geral, você pode esperar o seguinte:

- Ter um laboratório de sala de aula para cada classe.
- Criar um novo conjunto de laboratórios de sala de aula a cada semestre (ou para cada período de tempo em que sua classe é oferecida). Normalmente, para classes que têm as mesmas necessidades de imagem, você deve usar uma [Galeria de imagens compartilhada](#shared-image-gallery) para reutilizar imagens em laboratórios e semestres diferentes.

Considere os pontos a seguir ao determinar como estruturar seus laboratórios de sala de aula:

- **Todas as VMs em um laboratório de sala de aula são implantadas com a mesma imagem que é publicada**

    Consequentemente, se você tiver uma classe que exija que imagens de laboratório diferentes sejam publicadas ao mesmo tempo, laboratórios de sala de aula diferentes deverão ser criados para cada uma.
  
- **A cota de uso é definida no nível do laboratório e se aplica a todos os usuários no laboratório**

    Para definir cotas diferentes para usuários, você deve criar laboratórios de sala de aula distintos. No entanto, é possível adicionar mais horas a um usuário específico depois de definir a cota.
  
- **A agenda de inicialização ou desligamento é definida no nível do laboratório e se aplica a todas as VMs no laboratório**

    Semelhante ao ponto anterior, se você precisar definir agendas diferentes para os usuários, precisará criar laboratórios de sala de aula separados.

Por padrão, cada laboratório de sala de aula terá sua própria rede virtual.  Se você tiver o emparelhamento de vnet habilitado, cada laboratório da sala de aula terá sua própria sub-rede emparelhada com a rede virtual especificada.

## <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

Uma galeria de imagens compartilhadas é anexada a uma conta de laboratório e funciona como um repositório central para armazenar imagens. Uma imagem é salva na galeria quando um professor opta por exportar de uma VM (máquina virtual) de modelo de um laboratório de sala de aula. Cada vez que um professor faz alterações na VM de modelo e a exporta, novas versões da imagem são salvas mantendo as versões anteriores.

Os instrutores podem publicar uma versão de imagem da galeria de imagens compartilhadas ao criar um novo laboratório de sala de aula. Embora a galeria armazene várias versões de uma imagem, educadores só podem selecionar a versão mais recente durante a criação do laboratório.

A galeria de imagens compartilhadas é um recurso opcional que talvez não seja necessário imediatamente ao começar com apenas alguns laboratórios de sala de aula. No entanto, usar a galeria de imagens compartilhadas tem muitos benefícios que são úteis à medida que você amplia seu uso ao criar mais laboratórios de sala de aula:

- **Permite salvar e gerenciar versões de uma imagem de VM de modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração e assim por diante) em uma imagem da galeria do Marketplace público.  Por exemplo, é comum que educadores precisem que diferentes softwares/ferramentas sejam instalados. Em vez de exigir que os alunos instalem manualmente esses pré-requisitos por conta própria, diferentes versões da imagem da VM de modelo podem ser exportadas para uma galeria de imagens compartilhadas. Essas versões da imagem podem ser usadas ao criar novos laboratórios de sala de aula.
- **Permite o compartilhamento/reutilização de imagens da VM de modelo em laboratórios de sala de aula**

    Você pode salvar e reutilizar uma imagem para que não precise configurá-la do zero sempre que for criar um novo laboratório de sala de aula. Por exemplo, se várias classes oferecidas precisarem da mesma imagem, essa imagem só precisará ser criada uma vez e exportada para a galeria de imagens compartilhadas para que possa ser compartilhada em diferentes laboratórios de sala de aula.
- **Garante a disponibilidade da imagem por meio da replicação**

    Quando você salva na galeria de imagens compartilhadas de um laboratório de sala de aula, sua imagem é replicada automaticamente para outras [regiões na mesma área geográfica](https://azure.microsoft.com/global-infrastructure/regions/). Caso haja uma interrupção de serviço em uma região, a publicação da imagem em seu laboratório de sala de aula não será afetada, pois uma réplica da imagem de outra região poderá ser usada.  A publicação de VMs de várias réplicas também pode ajudar no desempenho.

Para agrupar logicamente as imagens compartilhadas, você tem algumas opções:

- Criar várias galerias de imagens compartilhadas. Cada conta de laboratório só pode se conectar a uma galeria de imagens compartilhadas, portanto, essa opção também exigirá que você crie várias contas de laboratório.
- Ou, você pode usar uma única galeria de imagens compartilhadas que seja compartilhada por várias contas de laboratório. Nesse caso, cada conta de laboratório poderá habilitar apenas as imagens aplicáveis aos laboratórios de sala de aula que ela contém.

## <a name="naming"></a>Nomenclatura

Ao começar a usar o Azure Lab Services, recomendamos que você estabeleça convenções de nomenclatura para grupos de recursos, contas de laboratório, laboratórios de sala de aula e a galeria de imagens compartilhadas. Embora as convenções de nomenclatura estabelecidas sejam exclusivas às necessidades da sua organização, a tabela a seguir descreve as diretrizes gerais.

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens compartilhadas | \<nome curto da organização\>-\<ambiente\>-rg<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte</li><li>**Ambiente** identifica o ambiente para o recurso, como piloto ou produção</li><li>**Rg** significa o tipo de recurso: grupo de recursos.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Conta de laboratório | Contém um ou mais laboratórios | \<nome curto da organização\>-\<ambiente\>-la<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte</li><li>**Ambiente** identifica o ambiente para o recurso, como piloto ou produção</li><li>**La** significa o tipo de recurso: conta de laboratório.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratório de sala de aula | Contém uma ou mais VMs |\<nome da classe\>-\<período de tempo\>-\<identificador do educador\><ul><li>**Nome da classe** identifica o nome da classe à qual o laboratório dá suporte.</li><li>**Período de tempo** identifica o período no qual a classe é oferecida.</li>**Identificador do educador** identifica o educador que possui o laboratório.</li></ul> | CS1234-fall2019-diogomartins<br/>CS1234-spring2019-diogomartins |
| Galeria de imagens compartilhadas | Contém uma ou mais versões da imagem da VM | \<nome curto da organização\>gallery | contosouniversitylabsgallery |

Para saber mais sobre a nomenclatura de outros recursos do Azure, confira [Convenções de nomenclatura para os recursos do Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regiões\locais

Ao configurar os recursos do Azure Lab Services, você precisa fornecer uma região (ou localização) do data center que hospedará o recurso. Apresentamos abaixo mais detalhes sobre como a região afeta cada recurso envolvido na configuração de um laboratório.

### <a name="resource-group"></a>Resource group

A região especifica o data center no qual as informações sobre o grupo de recursos são armazenadas. Os recursos do Azure contidos no grupo de recursos podem estar em regiões diferentes do recurso pai.

### <a name="lab-account"></a>Conta de laboratório

A localização de uma conta de laboratório indica a região em que esse recurso existe.  

### <a name="classroom-lab"></a>Laboratório de sala de aula

A localização em que um laboratório de sala de aula existe varia com base nos seguintes fatores:

  - **A conta de laboratório está emparelhada com uma rede virtual (VNet)**
  
    Uma conta de laboratório pode ser [emparelhada com uma VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando estão na mesma região.  Quando uma conta de laboratório é emparelhada com uma VNet, os laboratórios de sala de aula são criados automaticamente na mesma região da conta de laboratório e da VNet.

    > [!NOTE]
    > Quando uma conta de laboratório é emparelhada com uma VNet, a configuração para **Permitir que o criador do laboratório escolha a localização do laboratório** é desabilitada. Informações adicionais podem ser encontradas sobre essa configuração no artigo: [Permitir que o criador do laboratório escolha a localização do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nenhuma VNet está emparelhada***e***criadores de laboratórios não têm permissão para escolher a localização do laboratório**
  
    Quando **nenhuma** VNet está emparelhada com a conta de laboratório *e* [os criadores de laboratórios **não** têm permissão para escolher a localização do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), os laboratórios de sala de aula serão criados automaticamente em uma região com capacidade de VM disponível.  Especificamente, o Azure Lab Services buscará disponibilidade em regiões [que estejam na mesma área geográfica que a conta de laboratório](https://azure.microsoft.com/global-infrastructure/regions).

  - **Nenhuma VNet está emparelhada ***e*** criadores de laboratórios têm permissão para escolher a localização do laboratório**
       
    Quando **nenhuma** VNet está emparelhada e [os criadores de laboratório têm permissão para escolher a localização do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), os locais que podem ser selecionados pelo criador do laboratório se baseiam na capacidade disponível.

> [!NOTE]
> Para ajudar a garantir que haja capacidade de VM suficiente para uma região, é importante que você primeiro solicite a capacidade por meio da conta de laboratório ou ao criar o laboratório.

Uma regra geral é definir a região de um recurso para uma que seja mais próxima de seus usuários. Para laboratórios de sala de aula, isso significa criar o laboratório de sala de aula mais próximo de seus alunos. Para cursos online em que os alunos estão localizados em todo o mundo, você precisa usar seu bom senso para criar um laboratório de sala de aula em uma localização central. Também é possível dividir uma classe em vários laboratórios de sala de aula com base na região dos seus alunos.

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

A região indica a região de origem em que a primeira versão da imagem é armazenada antes de ser replicada automaticamente para as regiões de destino.

## <a name="vm-sizing"></a>Dimensionamento da VM

Quando os administradores ou os criadores de laboratórios criam um laboratório de sala de aula, eles podem escolher entre os seguintes tamanhos de VM com base nas necessidades de sua sala de aula. Lembre-se de que os tamanhos de computação disponíveis dependem da região em que sua conta de laboratório está localizada:

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequena| <ul><li>2 núcleos</li><li>3,5 GB de RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado a linha de comando, abertura de navegador da Web, servidores Web de baixo tráfego e bancos de dados pequenos a médios. |
| Médio | <ul><li>4 núcleos</li><li>7 GB de RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado a bancos de dados relacionais, cache na memória e análise. |
| Médio (Virtualização aninhada) | <ul><li>4 núcleos</li><li>16 GB de RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Este tamanho é mais adequado a bancos de dados relacionais, cache na memória e análise.
| grande | <ul><li>8 núcleos</li><li>16 GB de RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Este tamanho é mais adequado a aplicativos que precisam de CPUs mais rápidas, melhor desempenho do disco local, bancos de dados grandes, caches de memória grandes.  Este tamanho também dá suporte à virtualização aninhada. |
| Grande (Virtualização Aninhada) | <ul><li>8 núcleos</li><li>16 GB de RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Este tamanho é mais adequado a aplicativos que precisam de CPUs mais rápidas, melhor desempenho do disco local, bancos de dados grandes, caches de memória grandes. |
| GPU Pequena (Visualização) | <ul><li>6 núcleos</li><li>56 GB de RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Este tamanho é mais adequado a visualização remota, streaming, jogos e codificação usando estruturas como OpenGL e DirectX. |
| GPU Pequena (Computação) | <ul><li>6 núcleos</li><li>56 GB de RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Esse tamanho é mais adequado para aplicativos com uso intensivo de computação, como Inteligência Artificial e Aprendizado Profundo. |
| GPU Média (Visualização) | <ul><li>12 núcleos</li><li>112 GB de RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado a visualização remota, streaming, jogos e codificação usando estruturas como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerenciar identidade

Ao [Usar o controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview), as seguintes funções podem ser atribuídas para conceder acesso a contas de laboratório e laboratórios de sala de aula:

- **Proprietário da conta do laboratório**

    O administrador que cria a conta de laboratório é adicionado automaticamente à função de **Proprietário** da conta.  Um administrador atribuído à função de **Proprietário** pode:
     - Alterar as configurações da conta de laboratório.
     - Conceder a outros administradores acesso à conta de laboratório como proprietários ou colaboradores.
     - Dar a professores acesso a laboratórios de sala de aula como criadores, proprietários ou colaboradores.
     - Criar e gerenciar todos os laboratórios de sala de aula dentro da conta de laboratório.

- **Colaborador da conta de laboratório**

    Um administrador atribuído à função de **Colaborador** pode:
    - Alterar as configurações da conta de laboratório.
    - Criar e gerenciar todos os laboratórios de sala de aula dentro da conta de laboratório.

    No entanto, eles *não podem* conceder a outros usuários acesso a contas de laboratório ou laboratórios de sala de aula.

- **Criador de laboratório de sala de aula**

    Para criar laboratórios de sala de aula em uma conta de laboratório, um professor deve ter a função de **Criador do Laboratório**.  Quando um professor cria um laboratório de sala de aula, ele é adicionado automaticamente como um proprietário do laboratório.  Consulte o tutorial sobre como [adicionar um usuário à função **Criador do Laboratório**](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Proprietário\colaborador do laboratório de sala de aula**
  
    Um professor pode exibir e alterar as configurações de um laboratório de sala de aula quando ele tem a função **Proprietário** ou **Colaborador** de um laboratório; ele também deve ter a função de **Leitor** da conta do laboratório.

    Uma diferença importante entre as funções de **Proprietário** e **Colaborador** de um laboratório é que um colaborador *não pode* fornecer a outros usuários acesso para gerenciar o laboratório. Apenas os proprietários de um laboratório podem dar a outros usuários acesso para gerenciar o laboratório.

    Além disso, um professor *não pode* criar novos laboratórios de sala de aula, a menos que ele também tenha a função **Criador de Laboratório**.

- **Galeria de imagens compartilhadas**

    Quando você anexa uma galeria de imagens compartilhadas a uma conta de laboratório, os proprietários/colaboradores da conta e os criadores/proprietários/colaboradores do laboratório automaticamente recebem acesso para exibir e salvar imagens na galeria.

Aqui estão algumas dicas para ajudar na atribuição de funções:
   - Normalmente, somente os administradores devem ter as funções **Proprietário** ou **Colaborador** de uma conta de laboratório; você pode ter mais de um proprietário/colaborador.
   - Para dar a um professor a capacidade de criar novos laboratórios de sala de aula e gerenciar os laboratórios que eles criam, basta atribuir a ele acesso à função **Criador de Laboratório**.
   - Para dar a um professor a capacidade de gerenciar laboratórios de sala de aula específicos, mas *não* a capacidade de criar novos laboratórios, você deve atribuir a ele acesso à função **Proprietário** ou **Colaborador** para cada um dos laboratórios de sala de aula que ele deverá gerenciar.  Por exemplo, talvez você queira permitir que um professor e um estagiário sejam proprietários de um laboratório de sala de aula.  Consulte o guia sobre como [Adicionar um usuário como proprietário de um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services

Os preços para o Azure Lab Services são descritos no seguinte artigo: [Preços do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Você também precisa considerar o preço da galeria de imagens compartilhadas se planeja usá-la para armazenar e gerenciar versões de imagem. 

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

Criar uma galeria de imagens compartilhadas e anexá-la à sua conta de laboratório é gratuito. Os custos não são incorridos até que você salve uma versão de imagem na galeria. Normalmente, o preço para usar uma galeria de imagens compartilhadas é razoavelmente insignificante, mas é importante entender como ele é calculado, pois ele não está incluído no preço do Azure Lab Services.  

#### <a name="storage-charges"></a>Encargos de armazenamento

Para armazenar versões de imagem, uma galeria de imagens compartilhadas usa discos gerenciados por HDD padrão. O tamanho do disco gerenciado por HDD que é usado depende do tamanho da versão da imagem que está sendo armazenada. Confira o seguinte artigo para consultar os preços: [Preços de discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Encargos de saída de rede e replicação

Quando você salva uma versão de imagem usando a VM (máquina virtual) de modelo de um laboratório de sala de aula, o Azure Lab Services primeiro a armazena em uma região de origem e, em seguida, replica automaticamente a versão da imagem de origem para uma ou mais regiões de destino. É importante observar que o Azure Lab Services replica automaticamente a versão da imagem de origem para todas as [regiões de destino dentro da região geográfica](https://azure.microsoft.com/global-infrastructure/regions/) na qual o laboratório da sala de aula está localizado. Por exemplo, se o laboratório da sala de aula estiver nos EUA, uma versão da imagem será replicada para cada uma das oito regiões existentes nos EUA.

Um encargo de saída de rede ocorre quando uma versão de imagem é replicada da região de origem para regiões de destino adicionais. O valor cobrado é baseado no tamanho da versão da imagem quando os dados são transferidos inicialmente da região de origem.  Para obter detalhes sobre preços, confira o seguinte artigo: [Detalhes sobre os preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Os clientes de [soluções educacionais](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) podem ser dispensados de pagar encargos de saída. Fale com seu gerente de conta para saber mais.  Para saber mais, confira **a seção de perguntas frequentes** no documento vinculado, especificamente a pergunta "Quais programas de transferência de dados existem para clientes acadêmicos e como posso me qualificar?".

#### <a name="pricing-example"></a>Exemplo de preço

Para recapitular a estrutura de preço descrita acima, vejamos um exemplo de como salvar nossa imagem de VM de modelo na galeria de imagens compartilhadas. Considere os seguintes cenários:

- Você tem uma imagem de VM personalizada.
- Você está salvando duas versões da imagem.
- Seu laboratório está nos EUA, que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho. Consequentemente, o preço do disco gerenciado por HDD é de US$ 1,54 por mês.

O custo total é estimado como:

Número de imagens × número de versões × número de réplicas × preço do disco gerenciado

Neste exemplo, o custo é:

1 imagem personalizada (32 GB) x 2 versões x 8 regiões dos EUA x US$ 1,54 = US$ 24,64 por mês

#### <a name="cost-management"></a>Gerenciamento de custo

É importante que o administrador da conta de laboratório gerencie os custos, excluindo rotineiramente as versões de imagens desnecessárias da galeria. 

Você não deve excluir a replicação para regiões específicas como uma maneira de reduzir os custos (essa opção existe na galeria de imagens compartilhadas). Alterações na replicação podem ter efeitos adversos na capacidade do Azure Lab Service de publicar VMs de imagens salvas em uma galeria de imagens compartilhadas.

## <a name="next-steps"></a>Próximas etapas

Confira o tutorial para obter instruções passo a passo para criar uma conta de laboratório e um laboratório: [Guia de configuração](tutorial-setup-lab-account.md)
