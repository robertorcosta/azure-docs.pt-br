---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/31/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e87b6ee4739818e25ee069986e299f8205d44a2a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343296"
---
Este artigo responde a algumas perguntas frequentes sobre o Azure Managed Disks e os discos Azure Premium SSD.

## <a name="managed-disks"></a>Managed Disks

**O que é o Azure Managed Disks?**

Managed Disks é um recurso que simplifica o gerenciamento de disco para VMs IaaS do Azure manipulando o gerenciamento de conta de armazenamento para você. Para saber mais, veja [Visão geral dos Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se eu criar um disco gerenciado standard com base em um VHD existente que tem 80 GB, quanto isso custará?**

Um disco gerenciado standard criado com base em um VHD de 80 GB é tratado como o próximo tamanho de disco standard disponível, um disco S10. Você será cobrado de acordo com o preço do disco S10. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage).

**Existem custos de transação de discos gerenciados standard?**

Sim. Você é cobrado por cada transação. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerenciado standard, eu serei cobrado pelo tamanho real dos dados no disco ou pela capacidade provisionada do disco?**

Você é cobrado com base na capacidade provisionada do disco. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage).

**O preço dos discos premium gerenciados é diferente do preço dos discos não gerenciados?**

O preço dos discos premium gerenciados é o mesmo que os discos premium não gerenciados.

**Posso alterar o tipo de conta de armazenamento (Standard ou Premium) de meus discos gerenciados?**

Sim. Você pode alterar o tipo de conta de armazenamento de seus discos gerenciados usando o portal do Azure, PowerShell ou a CLI do Azure.

**Posso usar um arquivo VHD em uma conta de armazenamento do Azure para criar um disco gerenciado com uma assinatura diferente?**

Sim.

**Posso usar um arquivo VHD em uma conta de armazenamento do Azure para criar um disco gerenciado em uma região diferente?**

Não.

**Existem limitações de escala para clientes que usam discos gerenciados?**

O Managed Disks elimina os limites associados a contas de armazenamento. Porém, o limite máximo é de 50.000 discos gerenciados por região e por tipo de disco para uma assinatura.

**As VMs em um conjunto de disponibilidade podem consistir de uma combinação de discos gerenciados e não gerenciados?**

Não. As VMs em um conjunto de disponibilidade devem usar todos os discos gerenciados ou não gerenciados. Ao criar um conjunto de disponibilidade, você pode escolher qual tipo de discos que deseja usar.

**O Managed Disks é a opção padrão no portal do Azure?**

Sim.

**É possível criar um disco gerenciado vazio?**

Sim. Você pode criar um disco vazio. Um disco gerenciado pode ser criado independentemente de uma VM, por exemplo, sem anexá-lo a uma VM.

**O que é a contagem de domínios de falha com suporte para um conjunto de disponibilidade que usa o Managed Disks?**

Dependendo da região em que o conjunto de disponibilidade que usa o Managed Disks está localizado, a contagem de domínios de falha com suporte é 2 ou 3.

**Como é conta de armazenamento standard para a configuração de diagnóstico?**

Você configura uma conta de armazenamento privado para diagnóstico da VM.

**O tipo de suporte ao Controle de Acesso Baseado em Função está disponível para o Managed Disks?**

O Managed Disks oferece suporte a três funções principais padrão:

* Proprietário: pode gerenciar tudo, incluindo o acesso
* Colaborador: pode gerenciar tudo, exceto o acesso
* Leitor: pode ver tudo, mas não pode fazer alterações

**É possível copiar ou exportar um disco gerenciado para uma conta de armazenamento privado?**

Você pode gerar uma assinatura de acesso compartilhado somente leitura URI (SAS) para o disco gerenciado e usá-la para copiar o conteúdo para uma conta de armazenamento privado ou armazenamento local. Você pode usar o URI de SAS no Portal do Azure, no Azure PowerShell, na CLI do Azure ou no [AzCopy](../articles/storage/common/storage-use-azcopy.md).

**Posso criar uma cópia do meu disco gerenciado?**

Os clientes podem tirar um instantâneo de seus discos gerenciados e, em seguida, usar o instantâneo para criar outro disco gerenciado.

**Ainda há suporte para discos não gerenciados?**

Sim, há suporte para discos gerenciados e não gerenciados. Recomendamos que você use discos gerenciados para novas cargas de trabalho e migre suas cargas de trabalho atuais para discos gerenciados.

**Posso posicionar discos gerenciados e não gerenciados na mesma VM?**

Não.

**Se eu criar um disco de 128 GB e, em seguida, aumentar o tamanho para 130 Gibibytes (GiB), serei cobrado pelo próximo tamanho de disco (256 GiB)?**

Sim.

**Posso criar armazenamento com redundância local, armazenamento com redundância geográfica e discos de armazenamento com redundância de zona gerenciados?**

O Azure Managed Disks atualmente dá suporte apenas a discos gerenciados de armazenamento com redundância local.

**Posso reduzir ou diminuir o tamanho de meus discos gerenciados?**

Não. Não há suporte para esse recurso no momento.

**Posso interromper uma concessão no disco?**

Não. Isso não é compatível no momento porque uma concessão está presente para impedir a exclusão acidental quando o disco está sendo usado.

**Posso alterar a propriedade de nome do computador quando um disco do sistema operacional especializado (não criado usando a ferramenta de Preparação do Sistema ou generalizado) é usado para provisionar uma máquina virtual?**

Não. Não é possível atualizar a propriedade de nome do computador. A nova VM a herda do pai, que foi usado para criar o disco do sistema operacional. 

**Onde posso encontrar modelos do Azure Resource Manager de exemplo para criar VMs com discos gerenciados?**
* [Lista de modelos que usam o Managed Disks](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Ao criar um disco a partir de um blob, existe alguma relação existente com esse blob de origem?**

Não, quando o novo disco é criado é uma cópia autônoma completa do blob no momento e não há nenhuma conexão entre os dois. Se você quiser, depois de criar o disco, o blob de origem pode ser excluído sem afetar o disco recém-criado de alguma forma.

**Posso renomear um disco gerenciado ou não gerenciado depois que ele foi criado?**

Para discos gerenciados, você não pode renomeá-los. No entanto, você pode renomear um disco não gerenciado, desde que ele não está anexado a uma VM ou VHD.

**Posso usar o particionamento de GPT em um Disco do Azure?**

As imagens de geração 1 só podem usar o particionamento GPT em discos de dados, não OS discos do sistema operacional. Os discos do sistema operacional devem usar o estilo de partição MBR.

As [imagens de geração 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) podem usar o particionamento GPT no disco do sistema operacional, bem como os discos de dados.

**Quais tipos de disco dão suporte a instantâneos?**

Os instantâneos de suporte a SSD Premium, SSD padrão e HDD padrão. Para esses três tipos de disco, os instantâneos têm suporte para todos os tamanhos de disco (incluindo discos de até 32 TiB de tamanho). Ultra discos não oferecem suporte a instantâneos.

**O que são as reservas de disco do Azure?**
A reserva de disco é a opção de comprar um ano de armazenamento em disco com antecedência, reduzindo o custo total. Para obter detalhes sobre as reservas de disco do Azure, consulte nosso artigo sobre o assunto: [entender como seu desconto de reserva é aplicado ao disco do Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Quais opções a reserva de disco do Azure oferece?**    
A reserva de disco do Azure fornece a opção de comprar o SSDs Premium nas SKUs especificadas de p30 (1 TiB) até P80 (32 TiB) por um termo de um ano. Não há nenhuma limitação na quantidade mínima de discos necessários para comprar uma reserva de disco. Além disso, você pode optar por pagar com um único pagamento antecipado ou pagamentos mensais. Não há nenhum custo transacional adicional aplicado para SSD Premium Managed Disks.    

As reservas são feitas na forma de discos, não na capacidade. Em outras palavras, ao reservar um disco P80 (32 TiB), você obtém um único disco P80, não é possível dividir essa reserva específica em dois discos menores de P70 (16 TiB). É claro que você pode reservar tantos discos quanto desejar, incluindo dois discos P70 (16 TiB) separados.

**Como a reserva de disco do Azure é aplicada?**    
A reserva de discos segue um modelo semelhante às instâncias de VM (máquina virtual) reservadas. A diferença é que uma reserva de disco não pode ser aplicada a SKUs diferentes, enquanto uma instância de VM pode. Confira [salvar custos com instâncias de VM reservadas do Azure](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) para obter mais informações sobre instâncias de VM.     

**Posso usar meu armazenamento de dados adquirido por meio da reserva de discos do Azure em várias regiões?**    
A reserva de discos do Azure é adquirida para uma região e SKU específicos (como p30 no leste dos EUA 2) e, portanto, não pode ser usada fora dessas construções. Você sempre pode comprar uma reserva adicional de discos do Azure para suas necessidades de armazenamento em disco em outras regiões ou SKUs.    

**O que acontece quando minha reserva de discos do Azure expira?**    
Você receberá notificações por email 30 dias antes da expiração e novamente na data de expiração. Depois que a reserva expirar, os discos implantados continuarão a ser executados e serão cobrados com as [tarifas pagas pelo uso](https://azure.microsoft.com/pricing/details/managed-disks/)mais recentes.

### <a name="azure-shared-disks"></a>Discos compartilhados do Azure

**O recurso de discos compartilhados é compatível com discos não gerenciados ou BLOBs de páginas?**

Não, só há suporte para discos gerenciados do SSD Premium.

**Quais regiões dão suporte a discos compartilhados?**

Atualmente, apenas EUA Central oeste.

**OS discos compartilhados podem ser usados como um disco do sistema operacional?**

Não, os discos compartilhados só têm suporte para discos de dados.

**Quais tamanhos de disco dão suporte a discos compartilhados?**

Somente SSDs Premium que são P15 ou superior dão suporte a discos compartilhados.

**Se eu tiver um SSD Premium existente, posso habilitar discos compartilhados nele?**

Todos os discos gerenciados criados com a versão de API 2019-07-01 ou superior podem habilitar discos compartilhados. Para fazer isso, você precisa desmontar o disco de todas as VMs às quais ele está anexado. Em seguida, edite a `maxShares` propriedade no disco.

**Se eu não quiser mais usar um disco no modo compartilhado, como posso desabilitá-lo?**

Desmonte o disco de todas as VMs às quais ele está anexado. Em seguida, edite a propriedade maxShare no disco como 1.

**Você pode redimensionar um disco compartilhado?**

Sim.

**Posso habilitar o acelerador de gravação em um disco que também tem discos compartilhados habilitados?**

Não.

**Posso habilitar o cache de host para um disco com disco compartilhado habilitado?**

A única opção de cache de host com suporte é ' none '.

## <a name="ultra-disks"></a>Discos Ultra

**Para que devo definir a taxa de transferência de ultra Disk?**
Se você não tiver certeza sobre o que definir a taxa de transferência do disco como, recomendamos que comece supondo um tamanho de e/s de 16 KiB e ajuste o desempenho a partir daí, enquanto monitora seu aplicativo. A fórmula é: taxa de transferência em MBps = # de IOPS * 16/1000.

**Configurei meu disco para 40000 IOPS, mas estou vendo apenas 12800 IOPS, por que não estou vendo o desempenho do disco?**
Além do acelerador de disco, há um acelerador de e/s que é imposto no nível da VM. Verifique se o tamanho da VM que você está usando pode dar suporte aos níveis que estão configurados em seus discos. Para obter detalhes sobre os limites de e/s impostos pela sua VM, consulte [tamanhos de máquinas virtuais do Windows no Azure](../articles/virtual-machines/windows/sizes.md).

**Posso usar níveis de cache com um ultra Disk?**
Não, ultra discos não dão suporte aos diferentes métodos de cache que têm suporte em outros tipos de disco. Defina o cache de disco como nenhum.

**Posso anexar um ultra Disk à minha VM existente?**
Talvez sua VM tenha que estar em um par de regiões e de zona de disponibilidade que dá suporte a ultra discos. Consulte [introdução aos ultra discos](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) para obter detalhes.

**Posso usar um ultra Disk como o disco do sistema operacional para minha VM?**
Não, ultra discos só têm suporte como discos de dados e só têm suporte como discos nativos de 4K.

**Posso converter um disco existente em um ultra Disk?**
Não, mas você pode migrar os dados de um disco existente para um ultra Disk. Para migrar um disco existente para um ultra Disk, anexe ambos os discos à mesma VM e copie os dados do disco de um disco para outro ou aproveite uma solução de terceiros para a migração de dados.

**Posso criar instantâneos para ultra disks?**
Não, os instantâneos ainda não estão disponíveis.

**O backup do Azure está disponível para ultra discos?**
Não, o suporte do backup do Azure ainda não está disponível.

**Posso anexar um ultra Disk a uma VM em execução em um conjunto de disponibilidade?**
Não, ainda não há suporte para isso.

**Posso habilitar Azure Site Recovery para VMs usando ultra discos?**
Não, o Azure Site Recovery ainda não tem suporte para ultra discos.

## <a name="uploading-to-a-managed-disk"></a>Carregando para um disco gerenciado

**Posso carregar dados em um disco gerenciado existente?**

Não, o carregamento só pode ser usado durante a criação de um novo disco vazio com o estado **ReadyToUpload** .

**Como fazer carregar em um disco gerenciado?**

Criar um disco gerenciado com a propriedade [createoption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) de [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) definida como "upload", você pode carregar dados nele.

**Posso anexar um disco a uma VM enquanto ele estiver em um estado de carregamento?**

Não.

**Posso tirar um instantâneo de um disco de gerenciada em um estado de carregamento?**

Não.

## <a name="standard-ssd-disks"></a>Discos SSD Standard

**Quais são os discos SSD Standard do Microsoft Azure?**
Os discos SSD padrão são discos padrão com o apoio de mídia de estado sólida, otimizada como armazenamento econômico para cargas de trabalho que precisam de desempenho consistente em níveis inferiores de IOPS.

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões atualmente suportadas para discos SSD padrão?**
Todas as regiões do Azure agora oferecem suporte a discos SSD Standard.

**O Backup do Azure está disponível ao usar SSDs Standard?**
Sim, o Backup do Azure agora está disponível.

**O que é a vantagem de usar discos SSD padrão em vez de HDD?**
SSD Standard discos oferecem melhor latência, consistência, disponibilidade e confiabilidade em comparação com discos de HDD. As cargas de trabalho de aplicativos são executadas muito mais suavemente no SSD padrão por causa disso. Observe que os discos SSD premium são a solução recomendada para a maioria das cargas de trabalho de produção com uso intenso de I / O.

**Posso usar o padrão de SSDs como discos não gerenciados?**
Não, os discos SSDs Padrão somente estão disponíveis como discos gerenciados.

**Os discos SSD padrão têm suporte para "SLA de VM de Instância Única"?**
Não. Os discos SSD padrão não são compatíveis com SLA de VM de Instância Única. Use discos SSD premium para SLA de VM de Instância Única.

## <a name="migrate-to-managed-disks"></a>Como migrar para Managed Disks

**Há algum impacto da migração sobre o desempenho de Discos Gerenciados?**

Migração envolve a movimentação do disco de um local de armazenamento para outro. Isso é orquestrado por meio da cópia em segundo plano dos dados, o que pode levar várias horas para ser concluído, normalmente menos de 24 horas, dependendo da quantidade de dados nos discos. Durante esse tempo seu aplicativo pode apresentar latência de leitura maior do que o normal uma vez que as leituras podem ser redirecionadas para o local original e podem demorar para serem concluídas. Não há nenhum impacto na latência de gravação durante esse período.  

**Quais alterações são necessárias em uma já existente configuração de serviço do Backup do Azure antes/depois da migração para os Managed Disks?**

Nenhuma alteração é necessária.

**Os meus backups de VM criados pelo Serviço de Backup do Azure continuarão a funcionar antes da migração?**

Sim, os backups funcionam perfeitamente.

**Quais alterações são necessárias em uma já existente configuração de criptografia de discos do Azure antes/depois da migração para os Managed Disks?**

Nenhuma alteração é necessária.

**A migração automatizada de um conjunto de dimensionamento de máquinas virtuais existente de discos não gerenciados para Managed Disks tem suporte?**

Não. Você pode criar um novo conjunto de dimensionamento com os Managed Disks usando a imagem do seu antigo conjunto de dimensionamento com discos não gerenciados.

**Posso criar um disco gerenciado de um instantâneo de blob de páginas tirado antes da migração para os Managed Disks?**

Não. Você pode exportar um instantâneo de blob de páginas como um blob de páginas e, em seguida, criar um disco gerenciado a partir do blob de páginas exportado.

**Posso fazer failover de meus computadores locais protegidos pelo Azure Site Recovery em uma VM com os Managed Disks?**

Sim, você pode optar por fazer failover para uma VM com os Managed Disks.

**A migração tem algum impacto sobre as VMs do Azure protegidas pelo Azure Site Recovery por meio da replicação do Azure para o Azure?**

Não. Azure Site Recovery proteção do Azure para o Azure para VMs com Managed Disks está disponível.

**Posso migrar VMs com discos não gerenciados localizados em contas de armazenamento ou criptografados anteriormente em discos gerenciados?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e Criptografia de Serviço de Armazenamento

**A criptografia do lado do servidor é habilitada por padrão quando eu crio um disco gerenciado?**

Sim. Managed Disks são criptografadas com criptografia do lado do servidor com chaves gerenciadas pela plataforma. 

**O volume de inicialização é criptografado por padrão em um disco gerenciado?**

Sim. Por padrão, todos os discos gerenciados são criptografados, incluindo o disco do sistema operacional.

**Quem gerencia as chaves de criptografia?**

As chaves gerenciadas pela plataforma são gerenciadas pela Microsoft. Você também pode usar e gerenciar suas próprias chaves armazenadas no Azure Key Vault. 

**Posso desabilitar a criptografia do lado do servidor para meus discos gerenciados?**

Não.

**A criptografia do lado do servidor está disponível somente em regiões específicas?**

Não. A criptografia do lado do servidor com a plataforma e as chaves gerenciadas pelo cliente estão disponíveis em todas as regiões nas quais Managed Disks estão disponíveis. 

**O Azure Site Recovery oferece suporte à criptografia do lado do servidor com chave gerenciada pelo cliente para o Azure e cenários de recuperação de desastre do Azure para o Azure?**

Sim. 

**Posso fazer backup Managed Disks criptografado com a criptografia do lado do servidor com a chave gerenciada pelo cliente usando o serviço de backup do Azure?**

Sim.

**Os instantâneos gerenciados e as imagens são criptografados?**

Sim. Todos os instantâneos e imagens gerenciados são criptografados automaticamente. 

**Posso converter máquinas virtuais com discos não gerenciados que estão localizados em contas de armazenamento ou criptografados anteriormente em discos gerenciados?**

Sim

**Um VHD exportado de um disco gerenciado ou instantâneo também será criptografado?**

Não. Mas se você exportar um VHD para uma conta de armazenamento criptografada de um disco gerenciado ou instantâneo criptografado, ele estará criptografado. 

## <a name="premium-disks-managed-and-unmanaged"></a>Discos Premium: gerenciados e não gerenciados

**Se uma VM usa uma série de tamanho que dá suporte aos discos Premium SSD, como um DSv2, é possível anexar discos de dados standard e premium?** 

Sim.

**É possível anexar discos de dados standard e premium a uma série de tamanho que não oferece suporte a discos Premium SSD, como D, Dv2, G ou F?**

Não. Você só pode anexar discos de dados standard às VMs que não usam uma série de tamanho que dá suporte aos discos Premium SSD.

**Se criar um disco de dados premium com base em um VHD existente que tinha 80 GB, quanto isso custará?**

Um disco de dados premium criado com base em um VHD de 80 GB é tratado como o próximo tamanho de disco premium disponível, um disco P10. Você será cobrado de acordo com o preço do disco P10.

**Existem custos de transação para usar os Discos Premium SSD?**

Há um custo fixo para cada tamanho de disco, que vem provisionado com limites específicos de IOPS e taxa de transferência. Os outros custos são largura de banda de saída e recurso de instantâneos, caso aplicável. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites de IOPS e taxa de transferência que posso obter do cache de disco?**

Os limites combinados para cache e SSD local para um item da série DS são 4.000 IOPS por núcleo e 33 MiB por segundo por núcleo. A série GS oferece 5.000 IOPS por núcleo e 50 MiB por segundo por núcleo.

**O SSD local tem suporte para uma VM do Managed Disks?**

O SSD local é um armazenamento temporário fornecido com uma VM do Managed Disks. Não há custo adicional para esse armazenamento temporário. Recomendamos que você não use esse SSD local para armazenar os dados do aplicativo porque ele não é mantido no Armazenamento de Blobs do Azure.

**Existem repercussões pelo uso de TRIM em discos premium?**

Não há nenhuma desvantagem em usar CORTE nos Discos do Azure Premium ou Standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos de disco: gerenciados e não gerenciados

**Quais regiões dão suporte à capacidade de intermitência para o tamanho de disco SSD Premium aplicável?**

Atualmente, há suporte para a capacidade de intermitência em todas as regiões na nuvem pública do Azure, com suporte para nuvens soberanas em breve. 

**Quais regiões são 4/8/16 GiB de tamanhos de disco gerenciado (P1/P2/P3, E1/E2/E3) com suporte no?**

Atualmente, esses novos tamanhos de disco têm suporte em todas as regiões na nuvem pública do Azure, com suporte para nuvens soberanas em breve. 

**Os tamanhos de disco P1/P2/P3 têm suporte para discos não gerenciados ou BLOBs de página?**

Não, só há suporte para os discos gerenciados do SSD Premium. 

**Os tamanhos de disco E1/E2/E3 têm suporte para discos não gerenciados ou BLOBs de páginas?**

Não, os discos gerenciados do SSD Standard de qualquer tamanho não podem ser usados com discos não gerenciados ou BLOBs de páginas.

**Qual é o maior tamanho de disco gerenciado com suporte para o sistema operacional e os discos de dados?**

O tipo de partição a que o Azure dá suporte para um disco do sistema operacional é o MBR (registro mestre de inicialização). O formato do MBR dá suporte a tamanho de disco de até 2 TiB. O maior tamanho a que o Azure dá suporte para um disco do sistema operacional é 2 TiB. O Azure dá suporte a até 32 TB em discos de dados.

**Qual é o maior tamanho de disco não gerenciado com suporte para o sistema operacional e os discos de dados?**

O tipo de partição a que o Azure dá suporte para um disco do sistema operacional é o MBR (registro mestre de inicialização). O formato do MBR dá suporte a tamanho de disco de até 2 TiB. O maior tamanho a que o Azure dá suporte para um disco do sistema operacional é 2 TiB. O Azure dá suporte a até 4 TiB em discos de dados não gerenciados.

**Qual é o maior tamanho de blob de página com suporte?**

O maior tamanho de blob de página a que o Azure dá suporte é 8 TiB (8.191 GiB). O tamanho máximo do blob da página quando conectado a uma VM como dados ou discos do sistema operacional é de 4 TiB (4.095 GiB).

**Preciso usar uma nova versão das ferramentas do Azure para criar, anexar, redimensionar e carregar discos maiores que 1 TiB?**

Você não precisa atualizar as ferramentas existentes do Azure para criar, anexar ou redimensionar discos maiores que 1 TiB. Para carregar o arquivo VHD local diretamente no Azure como um blob de página ou disco não gerenciado, você precisará usar os conjuntos de ferramentas mais recentes, listadas abaixo. Só há suporte para carregamentos VHD de até 8 TiB.

|Ferramentas do Azure      | Versões com suporte                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Número de versão 4.1.0: versão de junho de 2017 ou posterior|
|CLI do Azure v1     | Número de versão 0.10.13: versão de maio de 2017 ou posterior|
|CLI do Azure v2     | Número de versão 2.0.12: versão de julho de 2017 ou posterior|
|AzCopy              | Número de versão 6.1.0: versão de junho de 2017 ou posterior|

**Os tamanhos de disco P4 e P6 têm suporte para discos não gerenciados ou blobs de página?**

Tamanhos de disco P4 (32 GiB) e P6 (64 GiB) não têm suporte como as camadas de disco padrão para discos não gerenciados e blobs de página. Você precisa explicitamente [definir a camada de Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) para P4 e P6 ter seu disco mapeado para essas camadas. Se você implantar um blob de página ou disco não gerenciado com o tamanho do disco ou o comprimento de conteúdo de menor que 32 GiB ou entre 32 GiB para 64 GiB sem definir a camada de Blob, você continuará a chegar em P10 com 500 IOPS e 100 MiB/s e o nível de preço mapeado.

**Se o meu disco gerenciado premium existente com menos de 64 GiB foi criado antes da habilitação da pequeno disco (por volta de 15 de junho de 2017), como ele é cobrado?**

Os discos pequenos premium com menos de 64 GiB continuam a ser cobrados de acordo com o tipo de preços P10.

**Como alternar a camada de disco dos discos premium pequenos com menos de 64 GiB de P10 para P4 ou P6?**

Você pode tirar um instantâneo dos discos pequenos e criar um disco para alternar automaticamente o tipo de preço para P4 ou P6 com base no tamanho provisionado.

**Você pode redimensionar Managed Disks existentes de tamanhos menos de 4 tebibytes (TiB) para novos tamanhos de disco recentemente introduzidos até 32 TiB?**

Sim.

**Quais são os maiores tamanhos de disco com suporte pelo serviço de backup e Azure Site Recovery do Azure?**

O maior tamanho de disco com suporte do backup do Azure é 32 TiB (4 TiB para discos criptografados). O maior tamanho de disco com suporte do Azure Site Recovery é 8 TiB. O suporte para discos maiores até 32 TiB ainda não está disponível no Azure Site Recovery.

**Quais são os tamanhos de VM recomendados para tamanhos de disco maiores (>4 TiB) para SSD Standard e HDD Standard discos para alcançar IOPS e largura de banda de disco otimizados?**

Para obter a taxa de transferência de disco de SSD Standard e HDD Standard tamanhos de disco grandes (>4 TiB) Além de 500 IOPS e 60 MiB/s, recomendamos que você implante uma nova VM de um dos seguintes tamanhos de VM para otimizar o desempenho: série B, série DSv2, série Dsv3, série ESv3, série FS, série Fsv2, série M, séries GS, NCv2, série NCv3 ou VMs da série ls... e 2. Anexar discos grandes a VMs ou VMS existentes que não usam os tamanhos recomendados acima pode apresentar um desempenho inferior.

**Como posso atualizar meus discos (>4 TiB) que foram implantados durante a visualização de tamanhos de disco maiores para obter o IOPS mais alto & largura de banda em GA?**

Você pode parar e iniciar a VM na qual o disco está anexado ou desanexar e anexar novamente o disco. Os destinos de desempenho de tamanhos de disco maiores foram aumentados para SSDs Premium e SSDs padrão na GA.

**Quais regiões são os tamanhos de disco gerenciado de 8 TiB, 16 TiB e 32 TiB com suporte no?**

As SKUs de disco 8 TiB, 16 TiB e 32 TiB têm suporte em todas as regiões em global Azure, Microsoft Azure Governamental e Azure China 21Vianet.

**Há suporte para habilitar o cache de host em todos os tamanhos de disco?**

Damos suporte ao cache de host de ReadOnly e leitura/gravação em tamanhos de disco inferiores a 4 TiB. Para tamanhos de disco mais de 4 TiB, não há suporte para a definição da opção de cache diferente de nenhum. É recomendável aproveitar o cache para tamanhos menores de disco em que você pode esperar para observar o aumento de desempenho melhor com dados armazenados em cache para a máquina virtual.

## <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?

Se sua pergunta não estiver listada aqui, fale conosco e nós ajudaremos a encontrar uma resposta. Você pode postar uma pergunta no final deste artigo nos comentários. Para se comunicar com a equipe do Armazenamento do Azure e outros membros da comunidade sobre este artigo, use o [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) no MSDN.

Para solicitar recursos, envie suas solicitações e ideias para o [Fórum de comentários do Armazenamento do Azure](https://feedback.azure.com/forums/217298-storage).
