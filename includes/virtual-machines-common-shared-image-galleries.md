---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117178"
---
A Galeria de Imagens Compartilhadas é um serviço que ajuda você a construir estrutura e organização em torno de suas imagens gerenciadas. As Galerias de Imagens Compartilhadas fornecem:

- Gestão de replicação global de imagens.
- Versão e agrupamento de imagens para facilitar o gerenciamento.
- Imagens altamente disponíveis com zrs (Zone Redundant Storage, armazenamento redundante de zona) em regiões que suportam Zonas de disponibilidade. A ZRS oferece melhor resiliência contra falhas zonais.
- Compartilhando entre assinaturas e até mesmo entre inquilinos do Active Directory (AD), usando RBAC.
- Dimensionar suas implantações com réplicas de imagem em cada região.

Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização. As imagens compartilhadas podem ser replicadas para várias regiões para dimensionar suas implantações mais rápido.

Uma imagem gerenciada é uma cópia de uma VM completa (incluindo quaisquer discos de dados anexados) ou apenas o disco do SO, dependendo de como você cria a imagem. Quando você cria uma VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos da nova VM. A imagem gerenciada permanece no armazenamento e pode ser usada repetidamente para criar novas VMs.

Se você tem um grande número de imagens gerenciadas que você precisa manter e gostaria de disponibilizá-las em toda a sua empresa, você pode usar uma Galeria de Imagens Compartilhadas como um repositório que facilita o compartilhamento de suas imagens. 

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerenciada** | Uma imagem básica que pode ser usada sozinha ou usada para criar uma **versão de imagem** em uma galeria de imagens. As imagens gerenciadas são criadas a partir de VMs [generalizadas.](#generalized-and-specialized-images) Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para criar várias VMs e agora pode ser usado para criar versões de imagem compartilhada. |
| **Instantâneo** | Uma cópia de um VHD que pode ser usado para fazer uma **versão de imagem**. Os instantâneos podem ser retirados de uma VM [especializada](#generalized-and-specialized-images) (uma que não foi generalizada) e depois usadas sozinhas ou com instantâneos de discos de dados, para criar uma versão de imagem especializada.
| **Galeria de imagens** | Como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição da imagem** | As imagens são definidas dentro de uma galeria e carregam informações sobre a imagem e os requisitos para usá-la dentro de sua organização. Você pode incluir informações como se a imagem é generalizada ou especializada, o sistema operacional, requisitos mínimos e máximos de memória e notas de lançamento. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é usada para criar uma VM ao usar uma galeria. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes. |

<br>

![Gráfico mostrando como você pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições de imagem

Definições de imagem são um agrupamento lógico para versões de uma imagem. A definição de imagem contém informações sobre por que a imagem foi criada, para que é o SO e informações sobre o uso da imagem. Uma definição de imagem é como um plano para todos os detalhes em torno da criação de uma imagem específica. Você não implanta uma VM a partir de uma definição de imagem, mas a partir da versão de imagem criada a partir da definição.

Existem três parâmetros para cada definição de imagem que são usados em combinação - **Publisher**, **Offer** e **SKU**. Estes são usados para encontrar uma definição de imagem específica. Você pode ter versões de imagem que compartilham um ou dois, mas não todos os três valores.  Por exemplo, aqui estão três definições de imagem e seus valores:

|Definição de imagem|Publicador|Oferta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|Testes|Finanças|Front-end|

Todos os três têm conjuntos exclusivos de valores. O formato é semelhante ao de como você pode atualmente especificar publisher, offer e SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) no Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem precisa ter um conjunto único desses valores.

A seguir estão outros parâmetros que podem ser definidos na definição da sua imagem para que você possa rastrear mais facilmente seus recursos:

* Estado do sistema operacional - Você pode definir o estado do Sistema Operacional como [generalizado ou especializado.](#generalized-and-specialized-images)
* Sistema operacional - pode ser windows ou Linux.
* Descrição - use a descrição para dar informações mais detalhadas sobre por que a definição de imagem existe. Por exemplo, você pode ter uma definição de imagem para o servidor front-end que tem o aplicativo pré-instalado.
* Eula - pode ser usado para apontar para um contrato de licença de usuário final específico para a definição de imagem.
* Notas de declaração de privacidade e lançamento - armazenar notas de lançamento e declarações de privacidade no armazenamento Do Zure e fornecer um URI para acessá-las como parte da definição de imagem.
* Data de fim de vida útil - anexe uma data de fim de vida à sua definição de imagem para poder usar a automação para excluir definições antigas de imagem.
* Tag - você pode adicionar tags quando criar sua definição de imagem. Para obter mais informações sobre tags, consulte [Usando tags para organizar seus recursos](../articles/azure-resource-manager/management/tag-resources.md)
* Recomendações mínimas e máximas de vCPU e memória - se sua imagem tiver recomendações de vCPU e memória, você pode anexar essas informações à definição de sua imagem.
* Tipos de disco proibidos - você pode fornecer informações sobre as necessidades de armazenamento de sua VM. Por exemplo, se a imagem não for adequada para discos HDD padrão, adicione-os à lista de não-indecatórios.

## <a name="generalized-and-specialized-images"></a>Imagens generalizadas e especializadas

Existem dois estados do sistema operacional suportados pela Galeria de Imagens Compartilhadas. Normalmente, as imagens exigem que o VM usado para criar a imagem tenha sido generalizado antes de tirar a imagem. Generalizar é um processo que remove informações específicas da máquina e do usuário da VM. Para windows, o Sysprep também é usado. Para linux, você pode `-deprovision+user` usar [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou parâmetros.

As VMs especializadas não passaram por um processo para remover informações e contas específicas da máquina. Além disso, as VMs criadas a `osProfile` partir de imagens especializadas não têm uma associação com elas. Isso significa que imagens especializadas terão algumas limitações.

- Contas que poderiam ser usadas para entrar na VM também podem ser usadas em qualquer VM criada usando a imagem especializada que é criada a partir dessa VM.
- VMs terão o nome do **computador** da VM da onde a imagem foi tirada. Você deve mudar o nome do computador para evitar colisões.
- É `osProfile` assim que algumas informações confidenciais são `secrets`passadas para a VM, usando . Isso pode causar problemas usando KeyVault, WinRM `secrets` e `osProfile`outras funcionalidades que são usados no . Em alguns casos, você pode usar identidades de serviço gerenciadas (MSI) para contornar essas limitações.

> [!IMPORTANT]
> Imagens especializadas estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitações de visualização conhecidas** VMs só podem ser criados a partir de imagens especializadas usando o portal ou API. Não há suporte a CLI ou PowerShell para a visualização.


## <a name="regional-support"></a>Suporte regional

As regiões de origem estão listadas na tabela abaixo. Todas as regiões públicas podem ser regiões-alvo, mas para replicar para a Austrália Central e Austrália Central 2 você precisa ter sua assinatura listada em branco. Para solicitar a inclusão na lista de permissões, acesse: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regiões de origem        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Austrália Central     | Leste da China        | Sul da Índia        | Europa Ocidental        |
| Austrália Central 2   | Leste da China 2      | Sudeste Asiático     | Sul do Reino Unido           |
| Leste da Austrália        | Norte da China       | Leste do Japão         | Oeste do Reino Unido            |
| Sudeste da Austrália   | Norte da China 2     | Oeste do Japão         | DoD Central dos EUA     |
| Sul do Brasil          | Leste da Ásia         | Coreia Central      | DoD do Leste dos EUA        |
| Canadá Central        | Leste dos EUA           | Sul da Coreia        | Governo dos EUA do Arizona     |
| Leste do Canadá           | Leste dos EUA 2         | Centro-Norte dos EUA   | Governo dos EUA do Texas       |
| Índia Central         | Leste dos EUA 2 EUAP    | Norte da Europa       | Gov. dos EUA – Virgínia    |
| Centro dos EUA            | França Central    | Centro-Sul dos Estados Unidos   | Oeste da Índia         |
| EUA Central EUAP       | Sul da França      | Centro-Oeste dos EUA    | Oeste dos EUA            |
|                       |                   |                    | Oeste dos EUA 2          |



## <a name="limits"></a>limites 

Há limites, por assinatura, para a implantação de recursos usando galerias de imagens compartilhadas:
- 100 galerias de imagens compartilhadas, por assinatura, por região
- 1.000 definições de imagem, por assinatura, por região
- 10.000 versões de imagem, por assinatura, por região
- Qualquer disco conectado à imagem deve ser menor ou igual a 1 TB de tamanho

Para obter mais informações, consulte [Verifique o uso de recursos contra limites,](https://docs.microsoft.com/azure/networking/check-usage-against-limits) para obter exemplos sobre como verificar seu uso atual.
 
## <a name="scaling"></a>Scaling
A Galeria de Pesquisa de Imagem permite que você especifique o número de réplicas que você deseja que o Azure mantenha das imagens. Isso ajuda em cenários de implantação de várias VMs, já que as implantações de VM podem ser distribuídas para diferentes réplicas, reduzindo a chance de o processamento de criação de instância ser limitado devido à sobrecarga de uma única réplica.

Com a Galeria de Imagens Compartilhadas, agora você pode implantar até 1.000 instâncias de VM em uma configuração de escala de máquina virtual (acima de 600 com imagens gerenciadas). As réplicas de imagem proporcionam melhor desempenho de implantação, confiabilidade e consistência. Você pode definir uma contagem de réplicas diferente em cada região alvo, com base nas necessidades de escala para a região. Como cada réplica é uma cópia profunda da sua imagem, isso ajuda a dimensionar suas implantações linearmente a cada réplica extra. Embora entendamos que duas imagens ou regiões são iguais, aqui está nossa diretriz geral sobre como usar réplicas em uma região:

- Para implantações de VMSS (Non-Virtual Machine Scale Set, conjunto de escala de máquinanão virtual) - Para cada 20 VMs que você cria simultaneamente, recomendamos que você mantenha uma réplica. Por exemplo, se você estiver criando 120 VMs simultaneamente usando a mesma imagem em uma região, sugerimos que você mantenha pelo menos 6 réplicas de sua imagem. 
- Para implantações do Virtual Machine Scale Set (VMSS) - Para cada implantação de conjunto de escala com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando 5 conjuntos de escala simultaneamente, cada um com 600 instâncias de VM usando a mesma imagem em uma única região, sugerimos que você mantenha pelo menos 5 réplicas de sua imagem. 

Sempre recomendamos que você superprovisione o número de réplicas devido a fatores como tamanho da imagem, conteúdo e tipo de Sistema operacional.

![Gráfico mostrando como você pode dimensionar imagens](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Torne suas imagens altamente disponíveis

[O ZRS (Azure Zone Redundant Storage, armazenamento redundante da zona azul)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) fornece resiliência contra uma falha na Zona de Disponibilidade na região. Com a disponibilidade geral da Galeria de Imagens Compartilhadas, você pode optar por armazenar suas imagens em contas ZRS em regiões com Zonas de Disponibilidade. 

Você também pode escolher o tipo de conta para cada uma das regiões-alvo. O tipo de conta de armazenamento padrão é Standard_LRS, mas você pode escolher Standard_ZRS para regiões com Zonas de Disponibilidade. Verifique [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)a disponibilidade regional do ZRS .

![Gráfico mostrando ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicação
A Galeria de Imagens Compartilhadas também permite replicar imagens para outras regiões do Azure automaticamente. Cada versão de imagem compartilhada pode ser replicada para diferentes regiões, dependendo do que faz sentido para sua organização. Um exemplo é sempre replicar a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em uma região. Isso pode ajudar a economizar nos custos de armazenamento das versões de imagem compartilhada. 

As regiões para as quais uma versão de Imagem compartilhada é replicada podem ser atualizadas após o horário de criação. O tempo necessário para replicar em diferentes regiões depende da quantidade de dados copiados e do número de regiões para as quais a versão é replicada. Isso pode levar algumas horas em alguns casos. Enquanto a replicação está em andamento, você pode exibir o status da replicação por região. Uma vez que a replicação de imagem esteja concluída em uma região, você pode implantar uma VM ou um conjunto de escala usando essa versão de imagem na região.

![Gráfico mostrando como você pode replicar imagens](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Acesso

Como a Galeria de Imagens Compartilhadas, a Definição de Imagem e a versão de imagem são todos os recursos, eles podem ser compartilhados usando os controles Azure RBAC nativos incorporados. Usando o RBAC, você pode compartilhar esses recursos com outros usuários, diretores de serviços e grupos. Você pode até compartilhar o acesso a indivíduos fora do inquilino que foram criados dentro. Uma vez que um usuário tenha acesso à versão de Imagem Compartilhada, ele pode implantar uma VM ou um Conjunto de Escala de Máquina Virtual.  Aqui está a matriz de compartilhamento que ajuda a entender ao que o usuário obtém acesso:

| Compartilhado com o usuário     | Galeria de imagens compartilhadas | Definição de imagem | Versão da imagem |
|----------------------|----------------------|--------------|----------------------|
| Galeria de imagens compartilhadas | Sim                  | Sim          | Sim                  |
| Definição de imagem     | Não                   | Sim          | Sim                  |

Recomendamos compartilhar no nível da Galeria para obter a melhor experiência. Não recomendamos o compartilhamento de versões individuais de imagem. Para obter mais informações sobre o RBAC, consulte [Gerenciar o acesso aos recursos do Azure usando o RBAC](../articles/role-based-access-control/role-assignments-portal.md).

As imagens também podem ser compartilhadas, em escala, até mesmo entre os inquilinos usando um registro de aplicativo de vários locatários. Para obter mais informações sobre o compartilhamento de imagens entre os inquilinos, consulte [Compartilhar imagens de VM da galeria entre os inquilinos do Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Cobrança
Não há custo adicional para usar o serviço de Galeria de Imagens Compartilhadas. Você será cobrado pelos seguintes recursos:
- Custos de armazenamento do armazenamento das versões de imagem compartilhada. O custo depende do número de réplicas da versão da imagem e do número de regiões para as qual a versão é replicada. Por exemplo, se você tiver 2 imagens e ambas forem replicadas para 3 regiões, então você será cobrado por 6 discos gerenciados com base no seu tamanho. Para obter mais informações, consulte [preços de Discos Gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/).
- Egress a rede cobra pela replicação da primeira versão de imagem da região de origem para as regiões replicadas. Réplicas subseqüentes são tratadas dentro da região, de modo que não há cargas adicionais. 

## <a name="updating-resources"></a>Atualização de recursos

Uma vez criado, você pode fazer algumas alterações nos recursos da galeria de imagens. Estes são limitados a:
 
Galeria de imagens compartilhadas:
- Descrição

definição da imagem:
- vCPUs recomendadas
- Memória recomendada
- Descrição
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Excluir das últimas
- Data de fim da vida útil

## <a name="sdk-support"></a>Suporte a SDK

Os seguintes SDKs dão suporte à criação de Galerias de Imagens Compartilhadas:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Ir](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelos

Você pode criar um recurso de Galeria de Imagens Compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

* [Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Posso mover minha imagem existente para a galeria de imagens compartilhadas?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Posso criar uma versão da imagem de um disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Posso mover o recurso da Galeria de Imagens Compartilhadas para uma assinatura diferente depois de criado?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Posso replicar minhas versões de imagem em nuvens como Azure China 21Vianet ou Azure Germany ou Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Posso replicar minhas versões de imagem entre assinaturas?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Posso compartilhar versões de imagens entre os locatários do Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual é a diferença entre região de origem e região de destino?](#what-is-the-difference-between-source-region-and-target-region)
* [Como faço para especificar a região de origem ao criar a versão da imagem?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Posso criar a galeria de imagens compartilhadas em um local diferente daquele para a definição de imagem e a versão da imagem?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quais são os encargos para usar a Galeria de Imagens Compartilhadas?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Qual versão de API devo usar para criar galeria de imagens compartilhadas e definição de imagem e versão de imagem?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Qual versão de API devo usar para criar VM compartilhado ou escala de máquina virtual definida fora da versão de imagem?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas?

Para listar todos os recursos da Galeria de Imagens Compartilhadas entre as assinaturas às quais você tem acesso no portal Dozure, siga os passos abaixo:

1. Abra o [portal Azure.](https://portal.azure.com)
1. Vá para **Todos os recursos**.
1. Selecione todas as assinaturas sob as quais você gostaria de listar todos os recursos.
1. Procure recursos do tipo **Galeria privada**.
 
   Para ver as definições de imagem e as versões da imagem, você também deve selecionar **Mostrar tipos ocultos**.
 
   Para listar todos os recursos da Galeria de Imagens Compartilhada entre assinaturas para as quais você tem permissões, use o seguinte comando na CLI do Azure:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Posso mover minha imagem existente para a galeria de imagens compartilhadas?
 
Sim. Há três cenários com base nos tipos de imagens que você pode ter.

 Cenário 1: Se você tiver uma imagem gerenciada na mesma assinatura do SIG, então você pode criar uma definição de imagem e uma versão de imagem a partir dela.

 Cenário 2: Se você tiver uma imagem não gerenciada na mesma assinatura do SIG, você pode criar uma imagem gerenciada a partir dela e, em seguida, criar uma definição de imagem e versão de imagem a partir dela. 

 Cenário 3: Se você tem um VHD em seu sistema de arquivos local, então você precisa carregar o VHD para uma imagem gerenciada, então você pode criar uma definição de imagem e versão de imagem a partir dele.

- Se o VHD é de uma VM Windows, consulte [Carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se o VHD for para uma VM do Linux, veja [Carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Posso criar uma versão da imagem de um disco especializado?

Sim, suporte para discos especializados como imagens está em visualização. Você só pode criar uma VM a partir de uma imagem especializada usando o portal ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) ou [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) e API. Não há suporte ao PowerShell para a visualização.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Posso mover o recurso da Galeria de Imagens Compartilhadas para uma assinatura diferente depois de criado?

Não, não é possível mover o recurso de Galeria de Imagens Compartilhadas para uma assinatura diferente. No entanto, você poderá replicar as versões da imagem na galeria para outras regiões conforme necessário.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Posso replicar minhas versões de imagem em nuvens como Azure China 21Vianet ou Azure Germany ou Azure Government Cloud?

Não, você não pode replicar as versões de imagem entre nuvens.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Posso replicar minhas versões de imagem entre assinaturas?

Não, você não pode replicar as versões de imagem entre regiões em uma assinatura e usá-las em outras assinaturas por meio de RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Posso compartilhar versões de imagens entre os locatários do Azure AD? 

Sim, você pode usar o RBAC para compartilhar com indivíduos entre inquilinos. Mas, para compartilhar em escala, consulte "Compartilhar imagens de galeria entre os inquilinos do Azure" usando [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?

O tempo de replicação de versão de imagem depende inteiramente do tamanho da imagem e do número de regiões para as quais ela está sendo replicada. No entanto, como uma melhor prática, é recomendável manter a imagem pequena e as regiões de origem e destino perto para melhores resultados. Você pode verificar o status da replicação usando o sinalizador -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual é a diferença entre região de origem e região de destino?

Região de origem é a região em que sua versão da imagem será criada e regiões de destino são as regiões em que uma cópia da sua versão da imagem será armazenada. Para cada versão de imagem, você pode ter apenas uma região de origem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Como faço para especificar a região de origem ao criar a versão da imagem?

Durante a criação de uma versão de imagem, você pode usar a marca **--location** na CLI e a marca **-Location** no PowerShell para especificar a região de origem. Verifique se a imagem gerenciada que você está usando como imagem base para criar a versão da imagem está no local em que você pretende criar a versão da imagem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?

Há duas maneiras de especificar o número de réplicas de versão da imagem a serem criadas em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que você deseja criar por região. 
2. A contagem de réplicas comuns, que é a contagem padrão por região caso a contagem de réplicas regionais não seja especificada. 

Para especificar a contagem de réplicas regionais, passe o local junto com o número de réplicas que deseja criar naquela região: "South Central US=2". 

Se a contagem de réplicas regionais não for especificada com cada local, o número de réplicas padrão será a contagem de réplicas comuns que você especificou. 

Para especificar a contagem de réplicas comuns na CLI, use o argumento **--replica-count** no comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Posso criar a galeria de imagens compartilhadas em um local diferente daquele para a definição de imagem e a versão da imagem?

Sim, isso é possível. Mas, como uma prática recomendada, encorajamos você a manter o grupo de recursos, galeria de imagens compartilhadas, definição de imagem e versão de imagem no mesmo local.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quais são os encargos para usar a Galeria de Imagens Compartilhadas?

Não há encargos para usar o serviço Galeria de Imagens Compartilhadas, exceto pelos encargos de armazenamento para armazenar as versões de imagem e os encargos de saída de rede para replicar as versões de imagem da região de origem para as regiões de destino.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Qual versão de API devo usar para criar galeria de imagens compartilhadas e definição de imagem e versão de imagem?

Para trabalhar com galerias de imagens compartilhadas, definições de imagem e versões de imagem, é recomendável usar a API versão 2018-06-01. Zone Redundant Storage (ZRS) requer a versão 2019-03-01 ou posterior.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Qual versão de API devo usar para criar VM compartilhado ou escala de máquina virtual definida fora da versão de imagem?

Para implantações de conjunto de dimensionamento de máquina virtual e VM usando uma versão de imagem, recomendamos usar a versão da API 2018-04-01 ou superior.
