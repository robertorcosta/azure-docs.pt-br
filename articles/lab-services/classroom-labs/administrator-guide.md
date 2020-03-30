---
title: Azure Lab Services - Guia de administrador | Microsoft Docs
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
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 8608aaab7bb8b6d10e67f27678c17f20a6c243da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370845"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Guia de administrador
Os administradores de Tecnologia da Informação (TI) que gerenciam os recursos em nuvem de uma universidade são normalmente responsáveis pela criação da conta de laboratório para sua escola. Uma vez que uma conta de laboratório é criada, administradores ou educadores criam laboratórios em sala de aula que estão contidos na conta do laboratório. Este artigo fornece uma visão geral de alto nível dos recursos do Azure envolvidos e a orientação para criá-los.

![Visão de alto nível dos recursos do Azure em uma conta de laboratório](../media/administrator-guide/high-level-view.png)

- Os laboratórios de sala de aula estão hospedados dentro de uma assinatura do Azure de propriedade do Azure Lab Services.
- Contas de laboratório, galeria de imagens compartilhadas e versões de imagem estão hospedadas em sua assinatura.
- Você pode ter sua conta de laboratório e a galeria de imagens compartilhadas no mesmo grupo de recursos. Neste diagrama, eles estão em diferentes grupos de recursos. 

## <a name="subscription"></a>Subscription
Sua universidade tem uma ou mais assinaturas do Azure. Uma assinatura é usada para gerenciar o faturamento e a segurança de todos os recursos do Azure\serviços que são usados dentro dele, incluindo contas de laboratório.

A relação entre uma conta de laboratório e sua assinatura é importante porque:

- O faturamento é relatado através da assinatura que contém a conta do laboratório.
- Você pode dar aos usuários do Azure Active Directory (AD) acesso ao Azure Lab Services. Você pode adicionar um usuário como um proprietário de conta de laboratório\contribuinte, criador de laboratório de sala de aula ou proprietário de laboratório em sala de aula.

Os laboratórios de sala de aula e suas máquinas virtuais (VMs) são gerenciados e hospedados para você dentro de uma assinatura de propriedade do Azure Lab Services.

## <a name="resource-group"></a>Resource group
Uma assinatura contém um ou mais grupos de recursos. Grupos de recursos são usados para criar agrupamentos lógicos de recursos do Azure que são usados juntos dentro da mesma solução.  

Quando você cria uma conta de laboratório, você deve configurar o grupo de recursos que contém a conta do laboratório. 

Um grupo de recursos também é necessário ao criar uma [galeria de imagens compartilhadas](#shared-image-gallery). Você pode optar por colocar sua conta de laboratório e galeria de imagens compartilhadas em dois grupos de recursos separados, o que é típico se você planeja compartilhar a galeria de imagens em diferentes soluções. Ou, você pode optar por colocá-los no mesmo grupo de recursos.

Quando você cria uma conta de laboratório, você pode criar e anexar automaticamente uma galeria de imagens compartilhadas ao mesmo tempo.  Essa opção resulta na conta de laboratório e na galeria de imagens compartilhadas sendo criadas em grupos de recursos separados. Você verá esse comportamento ao usar as etapas descritas neste tutorial: [Configure a galeria de imagens compartilhadas no momento da criação da conta de laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A imagem na parte superior deste artigo também usa essa configuração. 

Recomendamos investir tempo antecipadamente para planejar a estrutura de seus grupos de recursos, uma vez que *não* é possível alterar o grupo de recursos de uma conta de laboratório ou de uma galeria de imagens compartilhadas uma vez que ele é criado. Se você precisar alterar o grupo de recursos para esses recursos, você precisará excluir e recriar sua conta de laboratório e\ou galeria de imagens compartilhadas.

## <a name="lab-account"></a>Conta de laboratório
Uma conta de laboratório serve como um recipiente para um ou mais laboratórios de sala de aula. Quando se começa com o Azure Lab Services, é comum ter apenas uma única conta de laboratório. À medida que seu uso de laboratório escala, você pode mais tarde optar por criar mais contas de laboratório.

A lista a seguir destaca cenários onde mais de uma conta de laboratório pode ser benéfica:

- **Gerencie diferentes requisitos de políticas em laboratórios de sala de aula** 
    
    Quando você configura uma conta de laboratório, você define políticas que se aplicam a *todos os* laboratórios de sala de aula a conta do laboratório, tais como:
    - A rede virtual Do Azure com recursos compartilhados que o laboratório de sala de aula pode acessar. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso a um conjunto de dados compartilhados dentro de uma rede virtual.
    - As imagens da máquina virtual (VM) que os laboratórios de sala de aula podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso à imagem [do Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace. 
    
    Se você tem laboratórios de sala de aula que têm requisitos de políticas únicos um do outro, pode ser benéfico criar contas de laboratório separadas para gerenciar esses laboratórios de sala de aula separadamente.

- **Orçamento separado por conta de laboratório**
  
    Em vez de relatar todos os custos do laboratório de sala de aula através de uma única conta de laboratório, você pode precisar de um orçamento mais claramente separado. Por exemplo, você pode criar contas de laboratório para o departamento de Matemática da sua universidade, departamento de Ciência da Computação, e assim por diante, para separar o orçamento entre os departamentos.  Em seguida, você pode visualizar o custo de cada conta de laboratório individual usando [o Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).
    
- **Isolar laboratórios piloto de laboratórios de produção ativos\**
  
    Você pode ter casos em que deseja pilotar mudanças de políticas para uma conta de laboratório sem impactar potencialmente os laboratórios de produção ativa\. Neste tipo de cenário, a criação de uma conta de laboratório separada para fins de pilotagem permite isolar as alterações. 

## <a name="classroom-lab"></a>Laboratório de sala de aula
Um laboratório de sala de aula contém máquinas virtuais (VMs) que são cada uma atribuída a um único aluno. Em geral, você pode esperar:

- Tenha um laboratório de sala de aula para cada aula.
- Crie um novo conjunto de laboratórios de sala de aula a cada semestre (ou para cada período de tempo que sua aula é oferecida). Normalmente, para aulas que têm as mesmas necessidades de imagem, você deve usar uma [galeria de imagens compartilhadas](#shared-image-gallery) para reutilizar imagens em laboratórios e semestres.

Considere os seguintes pontos ao determinar como estruturar seus laboratórios de sala de aula:

- **Todas as VMs dentro de um laboratório de sala de aula são implantadas com a mesma imagem que é publicada**

    Como resultado, se você tem uma aula que exige que diferentes imagens de laboratório sejam publicadas ao mesmo tempo, laboratórios de sala de aula separados devem ser criados para cada um.
  
- **A cota de uso é definida no nível de laboratório e se aplica a todos os usuários dentro do laboratório**
    
    Para definir diferentes cotas para os usuários, você deve criar laboratórios de sala de aula separados. No entanto, é possível adicionar mais horas a um usuário específico depois de definir a cota.
  
- **O cronograma de inicialização ou desligamento é definido no nível do laboratório e se aplica a todas as VMs dentro do laboratório**

    Semelhante ao ponto anterior, se você precisar definir horários diferentes para os usuários, você precisa criar laboratórios de sala de aula separados. 

## <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas
Uma galeria de imagens compartilhadas é anexada a uma conta de laboratório e serve como um repositório central para armazenar imagens. Uma imagem é salva na galeria quando um educador escolhe exportar a partir de uma máquina virtual de modelo de laboratório de sala de aula (VM). Cada vez que um educador faz alterações no modelo VM e exporta, novas versões da imagem são salvas mantendo versões anteriores.

Os instrutores podem publicar uma versão de imagem da galeria de imagens compartilhadas quando criam um novo laboratório de sala de aula. Embora a galeria possa armazenar várias versões de uma imagem, os educadores só podem selecionar a versão mais recente durante a criação do laboratório.

A galeria de imagens compartilhadas é um recurso opcional que você pode não precisar imediatamente ao começar com apenas alguns laboratórios de sala de aula. No entanto, o uso da galeria de imagens compartilhadas tem muitos benefícios que são úteis à medida que você escala para ter mais laboratórios em sala de aula:

- **Permite que você salve e gerencie versões de uma imagem VM de modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração e assim por diante) para uma imagem da galeria pública do Marketplace.  Por exemplo, é comum que educadores exijam que diferentes ferramentas\de software sejam instaladas. Em vez de exigir que os alunos instalem manualmente esses pré-requisitos por conta própria, diferentes versões da imagem VM do modelo podem ser exportadas para uma galeria de imagens compartilhadas. Essas versões de imagem podem ser usadas ao criar novos laboratórios de sala de aula.
- **Permite o compartilhamento\reutilização de imagens VM de modelo em laboratórios de sala de aula**

    Você pode salvar e reutilizar uma imagem para que você não tenha que configurar a imagem do zero cada vez que você criar um novo laboratório de sala de aula. Por exemplo, se várias classes estão sendo oferecidas que precisam da mesma imagem, essa imagem só precisa ser criada uma vez e exportada para a galeria de imagens compartilhadas para que possa ser compartilhada em laboratórios de sala de aula.
- **Garante a disponibilidade de imagem através da replicação**

    Quando você salva na galeria de imagens compartilhadas de um laboratório de sala de aula, sua imagem é automaticamente replicada para [outras regiões dentro da mesma geografia](https://azure.microsoft.com/global-infrastructure/regions/). No caso de haver uma paralisação para uma região, publicar a imagem no laboratório da sala de aula não é afetado, uma vez que uma réplica de imagem de outra região pode ser usada.  Publicar VMs de várias réplicas também pode ajudar no desempenho.

Para agrupar logicamente imagens compartilhadas, você tem algumas opções:

- Crie várias galerias de imagens compartilhadas. Cada conta de laboratório só pode se conectar a uma galeria de imagens compartilhadas, então essa opção também exigirá que você crie várias contas de laboratório.
- Ou, você pode usar uma única galeria de imagens compartilhadas que é compartilhada por várias contas de laboratório. Neste caso, cada conta de laboratório pode habilitar apenas as imagens aplicáveis aos laboratórios de sala de aula que ele contém.

## <a name="naming"></a>Nomenclatura
À medida que você começa com o Azure Lab Services, recomendamos que você estabeleça convenções de nomeação para grupos de recursos, contas de laboratório, laboratórios de sala de aula e a galeria de imagens compartilhadas. Embora as convenções de nomeação que você estabelece serão exclusivas para as necessidades de sua organização, a tabela a seguir descreve diretrizes gerais.

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens compartilhadas | \<ambiente de\>-\<\>nome curto da organização -rg<ul><li>**Nome curto** da organização identifica o nome da organização que o grupo de recursos suporta</li><li>**O ambiente** identifica o ambiente para o recurso, como Piloto ou Produção</li><li>**RG** significa o tipo de recurso: grupo de recursos.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-piloto-rg<br/>contosouniversitylabs-prod-rg |
| Conta de laboratório | Contém um ou mais laboratórios | \<ambiente nome\>-\<\>curto organização -la<ul><li>**Nome curto** da organização identifica o nome da organização que o grupo de recursos suporta</li><li>**O ambiente** identifica o ambiente para o recurso, como Piloto ou Produção</li><li>**La** significa o tipo de recurso: conta de laboratório.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-piloto-la<br/>sciencedeptlabs-prod-la |
| Laboratório de sala de aula | Contém uma ou mais VMs |\<nome\>-\<de\>-\<classe tempo tempo educador identificador\><ul><li>**O nome** da classe identifica o nome da classe que o laboratório suporta.</li><li>**O prazo** identifica o prazo em que a classe é oferecida.</li>**O identificador educacional** identifica o educador dono do laboratório.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Galeria de imagens compartilhadas | Contém uma ou mais versões de imagem VM | \<galeria nome\>curto organização | contosouniversitylabsgallery |

Para obter mais informações sobre a nomeação de outros recursos do Azure, consulte [convenções de nomeação para recursos do Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regiões\localizações

Ao configurar os recursos do Azure Lab Services, você é obrigado a fornecer uma região (ou localização) do data center que hospedará o recurso. Aqui estão mais detalhes sobre como a região impacta cada um dos recursos envolvidos na criação de um laboratório.

### <a name="resource-group"></a>Resource group

A região especifica o data center onde as informações sobre o grupo de recursos são armazenadas. Os recursos do Azure contidos no grupo de recursos podem estar em diferentes regiões de seus pais.

### <a name="lab-account"></a>Conta de laboratório

A localização de uma conta de laboratório indica a região em que esse recurso existe.  

### <a name="classroom-lab"></a>Laboratório de sala de aula
    
A localização em que um laboratório de sala de aula existe varia de acordo com os seguintes fatores:

  - **A conta de laboratório é correspondente a uma rede virtual (VNet)**
  
    Uma conta de laboratório pode ser [pesquisada com um VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando eles estão na mesma região.  Quando uma conta de laboratório é acompanhada por um VNet, os laboratórios de sala de aula são criados automaticamente na mesma região que a conta de laboratório e o VNet.

    > [!NOTE]
    > Quando uma conta de laboratório é espiada com um VNet, a configuração para permitir que o **criador do laboratório escolha a localização do laboratório** é desativada. Podem ser encontradas informações adicionais sobre essa configuração no artigo: Permita que o [criador do laboratório escolha o local para o laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nenhum VNet é peered ***e*** os criadores de laboratório não têm permissão para escolher a localização do laboratório**
  
    Quando **não** há VNet com a conta do laboratório *e* [os criadores de laboratório **não** têm permissão para escolher a localização do laboratório, os](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)laboratórios de sala de aula são criados automaticamente em uma região que tem capacidade de VM disponível.  Especificamente, o Azure Lab Services procura disponibilidade em [regiões que estão dentro da mesma geografia que a conta do laboratório.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Nenhum VNet é peered ***e*** os criadores de laboratório são autorizados a escolher a localização do laboratório**
       
    Quando **não** há VNet peered e [os criadores de laboratório são autorizados a escolher a localização do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), os locais que podem ser selecionados pelo criador do laboratório são baseados na capacidade disponível.

Uma regra geral é definir a região de um recurso para uma que esteja mais próxima de seus usuários. Para laboratórios de sala de aula, isso significa criar o laboratório de sala de aula mais próximo de seus alunos. Para cursos online onde os alunos estão localizados em todo o mundo, você precisa usar o seu melhor julgamento para criar um laboratório de sala de aula que está localizado centralmente. Ou, divida uma aula em vários laboratórios de sala de aula com base na região de seus alunos.

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

A região indica a região de origem onde a primeira versão de imagem é armazenada antes de ser replicada automaticamente para regiões alvo.

## <a name="vm-sizing"></a>Dimensionamento de VM
Quando administradores ou criadores de laboratório criam um laboratório de sala de aula, eles podem escolher entre os seguintes tamanhos de VM com base nas necessidades de sua sala de aula. Lembre-se que os tamanhos de computação disponíveis dependem da região em que sua conta de laboratório está localizada:

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequena| <ul><li>2 núcleos</li><li>3,5 GB DE RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores web de baixo tráfego, bancos de dados pequenos a médios. |
| Médio | <ul><li>4 Núcleos</li><li>7 GB de RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para bancos de dados relacionais, cache na memória e análises. |
| Média (virtualização aninhada) | <ul><li>4 Núcleos</li><li>16 GB de RAM</li></ul> | [Standard_DC4s_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para bancos de dados relacionais, cache na memória e análises.  Esse tamanho também suporta virtualização aninhada. |
| grande | <ul><li>8 núcleos</li><li>32 GB DE RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, grandes bancos de dados, grandes caches de memória.  Esse tamanho também suporta virtualização aninhada. |
| Pequena GPU (Visualização) | <ul><li>6 Núcleos</li><li>56 GB DE RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando frameworks como OpenGL e DirectX. |
| GPU pequena (Computação) | <ul><li>6 Núcleos</li><li>56 GB DE RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Este tamanho é mais adequado para aplicações intensivas em computadores como Inteligência Artificial e Deep Learning. |
| GPU média (Visualização) | <ul><li>12 Núcleos</li><li>112 GB DE RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando frameworks como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerenciar identidade
Usando [o controle de acesso baseado em função do Azure,](https://docs.microsoft.com/azure/role-based-access-control/overview)as seguintes funções podem ser atribuídas para dar acesso a contas de laboratório e laboratórios de sala de aula:

- **Proprietário da conta do laboratório**

    O administrador que cria a conta de laboratório é automaticamente adicionado à função **de Proprietário** da conta do laboratório.  Um administrador designado para a função **Proprietário** pode:
     - Mude as configurações da conta do laboratório.
     - Dar a outros administradores acesso à conta do laboratório como proprietários ou contribuintes. 
     - Dar aos educadores acesso a laboratórios de sala de aula como criadores, proprietários ou colaboradores.
     - Crie e gerencie todos os laboratórios de sala de aula na conta do laboratório.

- **Contribuinte de conta de laboratório**

    Um administrador designado para a função **Contribuinte** pode:
    - Mude as configurações da conta do laboratório.
    - Crie e gerencie todos os laboratórios de sala de aula dentro da conta do laboratório.
    
    No entanto, eles *não podem* dar a outros usuários acesso a contas de laboratório ou laboratórios de sala de aula.

- **Criador de laboratório de sala de aula**

    Para criar laboratórios de sala de aula dentro de uma conta de laboratório, um educador deve ser um membro do papel de Criador do **Laboratório.**  Quando um educador cria um laboratório de sala de aula, eles são automaticamente adicionados como proprietários do laboratório.  Consulte o tutorial sobre como [adicionar um usuário à função De Criador do **Laboratório** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Proprietário do laboratório de sala de aula\contribuinte**
  
    Um educador pode visualizar e alterar as configurações de um laboratório de sala de aula quando é membro da função **de Proprietário** ou **Contribuinte** de um laboratório; eles também devem ser um membro do **papel** leitor da conta do laboratório.

    Uma diferença fundamental entre **as** funções de Proprietário e **Contribuinte** de um laboratório é que um colaborador *não pode* dar a outros usuários acesso para gerenciar o laboratório - apenas os proprietários podem dar a outros usuários acesso para gerenciar o laboratório.
    
    Além disso, um educador *não pode* criar novos laboratórios de sala de aula, a menos que eles também sejam um membro do papel de Criador do **Laboratório.**

- **Galeria de imagens compartilhadas**
    
    Quando você anexa uma galeria de imagens compartilhadas a uma conta de laboratório, os proprietários de contas de laboratório\colaboradores e criadores de laboratório\proprietários\colaboradores têm acesso automaticamente à visualização e salvar imagens na galeria. 

Aqui estão algumas dicas para ajudar na atribuição de funções:
   - Normalmente, apenas os administradores devem ser membros das funções de **Proprietário** ou **Contribuinte** de uma conta de laboratório; você pode ter mais de um proprietário\contribuinte.

   - Dar a um educador a capacidade de criar novos laboratórios de sala de aula e gerenciar os laboratórios que eles criam; você só precisa atribuir acesso à função **de Criador do Laboratório.**
   
   - Dar a um educador a capacidade de gerenciar laboratórios específicos de sala de aula, mas *não* a capacidade de criar novos laboratórios; você deve atribuir acesso à função **Proprietário** ou **Contribuinte** para cada um dos laboratórios de sala de aula que eles gerenciarão.  Por exemplo, você pode querer permitir que um professor e um assistente de ensino co-possuam um laboratório de sala de aula.  Consulte o guia sobre como [adicionar um usuário como proprietário a um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services
Os preços para o Azure Lab Services estão descritos no seguinte artigo: [Preços do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Você também precisa considerar os preços da galeria de imagens compartilhadas se você planeja usá-la para armazenar e gerenciar versões de imagem. 

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas
Criar uma galeria de imagens compartilhadas e anexá-la à sua conta de laboratório é gratuito. Os custos não são incorridos até que você salve uma versão de imagem na galeria. Normalmente, o preço para usar uma galeria de imagens compartilhadas é bastante insignificante, mas é importante entender como ele é calculado, uma vez que não está incluído nos preços do Azure Lab Services.  

#### <a name="storage-charges"></a>Taxas de armazenamento
Para armazenar versões de imagem, uma galeria de imagens compartilhadas usa discos gerenciados por HDD padrão. O tamanho do disco gerenciado pelo HDD que é usado depende do tamanho da versão de imagem que está sendo armazenada. Veja o artigo a seguir para ver os preços: [Preços gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/)de discos .


#### <a name="replication-and-network-egress-charges"></a>Cargas de replicação e saída de rede
Quando você salva uma versão de imagem usando a máquina virtual de modelo de laboratório de sala de aula (VM), o Azure Lab Services primeiro a armazena em uma região de origem e, em seguida, replica automaticamente a versão de imagem de origem para uma ou mais regiões-alvo. É importante notar que o Azure Lab Services replica automaticamente a versão de imagem de origem para todas as [regiões-alvo dentro da geografia](https://azure.microsoft.com/global-infrastructure/regions/) onde o laboratório de sala de aula está localizado. Por exemplo, se o seu laboratório de sala de aula está na geografia dos EUA, uma versão de imagem é replicada para cada uma das oito regiões que existem dentro dos EUA.

Uma carga de saída de rede ocorre quando uma versão de imagem é replicada da região de origem para regiões de destino adicionais. O valor cobrado é baseado no tamanho da versão da imagem quando os dados da imagem são inicialmente transferidos para fora da região de origem.  Para obter detalhes sobre preços, consulte o seguinte artigo: [Detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Os clientes [de soluções](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) educacionais podem ser dispensados de pagar taxas de saída. Fale com seu gerente de conta para saber mais.  Para obter mais informações, consulte consulte a seção **FAQ** no documento vinculado, especificamente a pergunta "Quais programas de transferência de dados existem para clientes acadêmicos e como eu me qualifico?".

#### <a name="pricing-example"></a>Exemplo de preços
Para recapitular os preços descritos acima, vamos olhar para um exemplo de salvar nossa imagem VM modelo para galeria de imagens compartilhadas. Assuma os seguintes cenários:

- Você tem uma imagem VM personalizada.
- Você está salvando duas versões da imagem.
- Seu laboratório é nos EUA, que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho; como resultado, o preço do disco gerenciado pelo HDD é de US$ 1,54 por mês.

O custo total é estimado em:

Número de imagens × número de versões × número de réplicas × preço de disco gerenciado

Neste exemplo, o custo é:

1 imagem personalizada (32 GB) x 2 versões x 8 regiões dos EUA x $1,54 = $24,64 por mês

#### <a name="cost-management"></a>Gerenciamento de custo
É importante que o administrador de contas de laboratório gerencie custos excluindo rotineiramente versões de imagem desnecessárias da galeria. 

Você não deve excluir a replicação para regiões específicas como forma de reduzir os custos (essa opção existe na galeria de imagens compartilhadas). As alterações de replicação podem ter efeitos adversos na capacidade do Azure Lab Service de publicar VMs a partir de imagens salvas em uma galeria de imagens compartilhadas.

## <a name="next-steps"></a>Próximas etapas
Veja o tutorial para instruções passo a passo para criar uma conta de laboratório e um laboratório: [Configure o Guia](tutorial-setup-lab-account.md)
