---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 53bbee6dd75e045c2a7e95c88a0138c9859d12db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80374051"
---
Este artigo responde a algumas perguntas frequentes sobre o Azure Managed Disks e os discos Azure Premium SSD.

## <a name="managed-disks"></a>Managed Disks

**O que é o Azure Managed Disks?**

Managed Disks é um recurso que simplifica o gerenciamento de disco para VMs IaaS do Azure manipulando o gerenciamento de conta de armazenamento para você. Para saber mais, veja [Visão geral dos Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se eu criar um disco gerenciado standard com base em um VHD existente que tem 80 GB, quanto isso custará?**

Um disco gerenciado standard criado com base em um VHD de 80 GB é tratado como o próximo tamanho de disco standard disponível, um disco S10. Você será cobrado de acordo com o preço do disco S10. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Existem custos de transação de discos gerenciados standard?**

Sim. Você é cobrado por cada transação. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Para um disco gerenciado standard, eu serei cobrado pelo tamanho real dos dados no disco ou pela capacidade provisionada do disco?**

Você é cobrado com base na capacidade provisionada do disco. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage).

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

**Se eu criar um disco de 128 GB e aumentar o tamanho para 130 gibibytes (GiB), serei cobrado pelo próximo tamanho de disco (256 GiB)?**

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
* [Lista de modelos que usam o Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Ao criar um disco a partir de um blob, existe alguma relação existente com esse blob de origem?**

Não, quando o novo disco é criado é uma cópia autônoma completa do blob no momento e não há nenhuma conexão entre os dois. Se você quiser, depois de criar o disco, o blob de origem pode ser excluído sem afetar o disco recém-criado de alguma forma.

**Posso renomear um disco gerenciado ou não gerenciado depois que ele foi criado?**

Para discos gerenciados, você não pode renomeá-los. No entanto, você pode renomear um disco não gerenciado, desde que ele não está anexado a uma VM ou VHD.

**Posso usar o particionamento de GPT em um Disco do Azure?**

As imagens da geração 1 só podem usar particionamento GPT em discos de dados, não em discos do SISTEMA OPERACIONAL. Os discos do sistema operacional devem usar o estilo de partição MBR.

[As imagens da geração 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) podem usar particionamento GPT no disco do sistema operacional, bem como nos discos de dados.

**Quais tipos de disco suportam instantâneos?**

SSD premium, SSD padrão e instantâneos de suporte hdd padrão. Para esses três tipos de disco, os snapshots são suportados para todos os tamanhos de disco (incluindo discos de até 32 TiB de tamanho). Os discos ultra não suportam instantâneos.

**O que são reservas de disco do Azure?**
A reserva de disco é a opção de comprar um ano de armazenamento em disco com antecedência, reduzindo seu custo total. Para obter detalhes sobre as reservas de disco do Azure, consulte nosso artigo sobre o assunto: [Entenda como seu desconto de reserva é aplicado ao Azure Disk](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Quais opções oferece reserva de disco do Azure?**    
A reserva de disco do Azure oferece a opção de comprar SSDs Premium nas SKUs especificadas de P30 (1 TiB) até P80 (32 TiB) por um período de um ano. Não há limitação na quantidade mínima de discos necessários para comprar uma reserva de disco. Além disso, você pode optar por pagar com um único pagamento antecipado ou pagamentos mensais. Não há custo transacional adicional aplicado para discos gerenciados Premium SSD.    

As reservas são feitas na forma de discos, não de capacidade. Em outras palavras, quando você reserva um disco P80 (32 TiB), você recebe um único disco P80, você não pode dividir essa reserva específica em dois discos P70 (16 TiB) menores. Você pode, é claro, reservar quantos ou poucos discos quiser, incluindo dois discos P70 (16 TiB) separados.

**Como é aplicada a reserva de disco do Azure?**    
A reserva de discos segue um modelo semelhante às instâncias de máquina virtual reservada (VM). A diferença é que uma reserva de disco não pode ser aplicada a Diferentes SKUs, enquanto uma instância de VM pode. Consulte [Salvar custos com a VM Reservada do Azure para](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) obter mais informações sobre instâncias de VM.     

**Posso usar meu armazenamento de dados comprado através da reserva de discos Do Zure em várias regiões?**    
A reserva de discos Azure é comprada para uma região específica e SKU (como P30 no Leste dos EUA 2), e, portanto, não pode ser usada fora desses construtos. Você sempre pode comprar uma reserva adicional de discos azure para suas necessidades de armazenamento de disco em outras regiões ou SKUs.    

**O que acontece quando minha reserva de discos Do Azure expirar?**    
Você receberá notificações de e-mail 30 dias antes do vencimento e novamente na data de validade. Uma vez que a reserva expirar, os discos implantados continuarão a ser executados e serão cobrados com as [últimas taxas de pagamento](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="azure-shared-disks"></a>Discos compartilhados do Azure

**O recurso de discos compartilhados é suportado para discos não gerenciados ou bolhas de página?**

Não, ele só é suportado para discos gerenciados premium SSD.

**Quais regiões suportam discos compartilhados?**

Atualmente, apenas o Centro-Oeste dos EUA.

**Os discos compartilhados podem ser usados como um disco do SISTEMA OPERACIONAL?**

Não, discos compartilhados são suportados apenas para discos de dados.

**Quais tamanhos de disco suportam discos compartilhados?**

Apenas SSDs premium que são P15 ou mais suportam discos compartilhados.

**Se eu tiver um SSD premium existente, posso habilitar discos compartilhados nele?**

Todos os discos gerenciados criados com a versão API 2019-07-01 ou superior podem habilitar discos compartilhados. Para fazer isso, você precisa desmontar o disco de todas as VMs a que ele está conectado. Em seguida, `maxShares` edite a propriedade no disco.

**Se eu não quiser mais usar um disco no modo compartilhado, como desativá-lo?**

Desmonte o disco de todas as VMs a que ele está conectado. Em seguida, edite a propriedade maxShare no disco para 1.

**Você pode redimensionar um disco compartilhado?**

Sim.

**Posso ativar o acelerador de gravação em um disco que também tenha discos compartilhados ativados?**

Não.

**Posso habilitar o cache do host para um disco que tenha o disco compartilhado ativado?**

A única opção de cache de host suportada é 'Nenhum'.

## <a name="ultra-disks"></a>Discos Ultra

**Para que devo definir meu throughput de ultra disco?**
Se você não tem certeza do que definir o rendimento do seu disco, recomendamos que você comece assumindo um tamanho de IO de 16 KiB e ajuste o desempenho a partir daí enquanto monitora sua aplicação. A fórmula é: Throughput in MBps = # of IOPS * 16 / 1000.

**Configurei meu disco para 40000 IOPS mas estou vendo apenas 12800 IOPS, por que não estou vendo o desempenho do disco?**
Além do acelerador de disco, há um acelerador IO que é imposto ao nível de VM. Certifique-se de que o tamanho da VM que você está usando pode suportar os níveis configurados em seus discos. Para obter detalhes sobre os limites de IO impostos pela VM, consulte [Tamanhos para máquinas virtuais windows no Azure](../articles/virtual-machines/windows/sizes.md).

**Posso usar níveis de cache com um disco ultra?**
Não, os discos ultra não suportam os diferentes métodos de cache que são suportados em outros tipos de disco. Defina o cache de disco como Nenhum.

**Posso anexar um disco ultra à minha VM existente?**
Talvez, sua VM tenha que estar em um par de região e zona de disponibilidade que suporte discos Ultra. Consulte como começar com os discos ultra para obter [detalhes.](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md)

**Posso usar um disco ultra como disco do SO para a minha VM?**
Não, os discos ultra são suportados apenas como discos de dados e são suportados apenas como discos nativos 4K.

**Posso converter um disco existente em um disco ultra?**
Não, mas você pode migrar os dados de um disco existente para um disco ultra. Para migrar um disco existente para um disco ultra, conecte ambos os discos à mesma VM e copie os dados do disco de um disco para o outro ou aproveite uma solução de terceiros para migração de dados.

**Posso criar instantâneos para ultra discos?**
Não, os instantâneos ainda não estão disponíveis.

**O Backup do Azure está disponível para ultra discos?**
Não, o suporte ao Azure Backup ainda não está disponível.

**Posso anexar um disco ultra a uma VM em execução em um conjunto de disponibilidade?**
Não, isso ainda não está suportado.

**Posso ativar a recuperação do site do Azure para VMs usando discos ultra?**
Não, o Azure Site Recovery ainda não tem suporte para ultra discos.

## <a name="uploading-to-a-managed-disk"></a>Carregando para um disco gerenciado

**Posso carregar dados para um disco gerenciado existente?**

Não, o upload só pode ser usado durante a criação de um novo disco vazio com o estado **ReadyToUpload.**

**Como faço o upload para um disco gerenciado?**

Crie um disco gerenciado com a [criaçãoPropriedade option](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) de [criaçãoData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) definida como "Upload", então você pode carregar dados para ele.

**Posso anexar um disco a uma VM enquanto ele estiver em um estado de upload?**

Não.

**Posso tirar uma foto de um disco manged em um estado de upload?**

Não.

## <a name="standard-ssd-disks"></a>Discos SSD Standard

**Quais são os discos SSD Standard do Microsoft Azure?**
Os discos SSD padrão são discos padrão com o apoio de mídia de estado sólida, otimizada como armazenamento econômico para cargas de trabalho que precisam de desempenho consistente em níveis inferiores de IOPS.

<a id="standard-ssds-azure-regions"></a>**Quais são as regiões atualmente suportadas para discos SSD padrão?**
Todas as regiões do Azure agora oferecem suporte a discos SSD Standard.

**O Backup do Azure está disponível ao usar SSDs Standard?**
Sim, o Backup do Azure agora está disponível.

**Como criar discos SSD padrão?**
Você pode criar discos SSD padrão usando modelos do Azure Resource Manager, SDK, PowerShell ou CLI. Abaixo estão os parâmetros necessários no modelo do Gerenciador de Recursos para criar discos SSD padrão:

* *apiVersion* para Microsoft.Compute deve ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk.storageAccountType* como `StandardSSD_LRS`

A exemplo a seguir mostra a seção *properties.storageProfile.osDisk* de uma VM que usa discos SSD padrão:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Para obter um exemplo de modelo completo de como criar um disco SSD padrão com um modelo, consulte [Criar uma máquina virtual a partir de uma imagem do Windows com discos de dados padrão SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Posso converter discos existentes em SDD padrão?**
 Sim, pode. Para obter as diretrizes gerais de conversão do serviço Managed Disks, confira o artigo [Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Use o valor a seguir para atualizar o tipo de disco como SDD padrão.
-AccountType StandardSSD_LRS

**O que é a vantagem de usar discos SSD padrão em vez de HDD?**
Os discos SSD padrão oferecem melhor latência, consistência, disponibilidade e confiabilidade em comparação com discos HDD. As cargas de trabalho de aplicativos são executadas muito mais suavemente no SSD padrão por causa disso. Observe que os discos SSD premium são a solução recomendada para a maioria das cargas de trabalho de produção com uso intenso de I / O.

**Posso usar o padrão de SSDs como discos não gerenciados?**
Não, os discos SSDs Padrão somente estão disponíveis como discos gerenciados.

**Os discos SSD padrão têm suporte para "SLA de VM de Instância Única"?**
Não. Os discos SSD padrão não são compatíveis com SLA de VM de Instância Única. Use discos SSD premium para SLA de VM de Instância Única.

## <a name="migrate-to-managed-disks"></a>Como migrar para Managed Disks

**Há algum impacto da migração sobre o desempenho de Discos Gerenciados?**

Migração envolve a movimentação do disco de um local de armazenamento para outro. Isso é orquestrado através de uma cópia de fundo de dados, que pode levar várias horas para ser concluída, normalmente menos de 24 Horas, dependendo da quantidade de dados nos discos. Durante esse tempo seu aplicativo pode apresentar latência de leitura maior do que o normal uma vez que as leituras podem ser redirecionadas para o local original e podem demorar para serem concluídas. Não há nenhum impacto na latência de gravação durante esse período.  

**Quais alterações são necessárias em uma já existente configuração de serviço do Backup do Azure antes/depois da migração para os Managed Disks?**

Nenhuma alteração é necessária.

**Os meus backups de VM criados pelo Serviço de Backup do Azure continuarão a funcionar antes da migração?**

Sim, os backups funcionam perfeitamente.

**Quais alterações são necessárias em uma já existente configuração de criptografia de discos do Azure antes/depois da migração para os Managed Disks?**

Nenhuma alteração é necessária.

**A migração automatizada de uma escala de máquina virtual existente é suportada a partir de discos não gerenciados para discos gerenciados?**

Não. Você pode criar um novo conjunto de dimensionamento com os Managed Disks usando a imagem do seu antigo conjunto de dimensionamento com discos não gerenciados.

**Posso criar um disco gerenciado de um instantâneo de blob de páginas tirado antes da migração para os Managed Disks?**

Não. Você pode exportar um instantâneo de blob de páginas como um blob de páginas e, em seguida, criar um disco gerenciado a partir do blob de páginas exportado.

**Posso fazer failover de meus computadores locais protegidos pelo Azure Site Recovery em uma VM com os Managed Disks?**

Sim, você pode optar por fazer failover para uma VM com os Managed Disks.

**A migração tem algum impacto sobre as VMs do Azure protegidas pelo Azure Site Recovery por meio da replicação do Azure para o Azure?**

Não. A proteção azure Site Recovery Azure para Azure para VMs com Discos Gerenciados está disponível.

**Posso migrar VMs com discos não gerenciados localizados em contas de armazenamento ou criptografados anteriormente em discos gerenciados?**

Sim

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e Criptografia de Serviço de Armazenamento

**A Criptografia do Serviço de Armazenamento do Azure fica habilitada por padrão quando crio um disco gerenciado?**

Sim.

**O volume de inicialização é criptografado por padrão em um disco gerenciado?**

Sim. Por padrão, todos os discos gerenciados são criptografados, incluindo o disco do SISTEMA OPERACIONAL.

**Quem gerencia as chaves de criptografia?**

A Microsoft gerencia as chaves de criptografia.

**Posso desabilitar a Criptografia do Serviço de Armazenamento para meus discos gerenciados?**

Não.

**A Criptografia do Serviço de Armazenamento só está disponível em regiões específicas?**

Não. Ele está disponível em todas as regiões em que os Discos Gerenciados estão disponíveis. O Managed Disks está disponível em todas as regiões públicas e na Alemanha. O serviço está disponível também na China, no entanto apenas para Chaves Gerenciadas da Microsoft, mas não para Chaves Gerenciadas do Cliente.

**Como posso descobrir se o disco gerenciado está criptografado?**

Você pode descobrir a data em que um disco gerenciado foi criado no portal do Azure, na CLI do Azure e no PowerShell. Se a data é posterior a 9 de junho de 2017, o disco está criptografado.

**Como faço para criptografar meus discos existentes que foram criados antes de 10 de junho de 2017?**

A partir de 10 de junho de 2017, os novos dados gravados em discos gerenciados existentes serão criptografados automaticamente. Nós também estamos planejando criptografar os dados existentes e a criptografia ocorrerá assincronamente em segundo plano. Se você precisar criptografar os dados existentes agora, crie uma cópia do disco. Os novos discos serão criptografados.

* [Copiar discos gerenciados usando a CLI do Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiar discos gerenciados usando o PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Os instantâneos gerenciados e as imagens são criptografados?**

Sim. Todos os instantâneos e imagens criados após 9 de junho de 2017 são criptografados automaticamente. 

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

Há um custo fixo para cada tamanho de disco, que vem provisionado com limites específicos de IOPS e taxa de transferência. Os outros custos são largura de banda de saída e recurso de instantâneos, caso aplicável. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage).

**Quais são os limites de IOPS e taxa de transferência que posso obter do cache de disco?**

Os limites combinados para cache e SSD local para um item da série DS são 4.000 IOPS por núcleo e 33 MiB por segundo por núcleo. A série GS oferece 5.000 IOPS por núcleo e 50 MiB por segundo por núcleo.

**O SSD local tem suporte para uma VM do Managed Disks?**

O SSD local é um armazenamento temporário fornecido com uma VM do Managed Disks. Não há custo adicional para esse armazenamento temporário. Recomendamos que você não use esse SSD local para armazenar os dados do aplicativo porque ele não é mantido no Armazenamento de Blobs do Azure.

**Existem repercussões pelo uso de TRIM em discos premium?**

Não há nenhuma desvantagem em usar CORTE nos Discos do Azure Premium ou Standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Novos tamanhos de disco: gerenciados e não gerenciados

**Quais regiões suportam a capacidade de estouro do tamanho de disco SSD premium aplicável?**

A capacidade de estouro é atualmente suportada no Azure West Central EUA.

**Em quais regiões são suportadas em 4/8/16 tamanhos de disco gerenciados GiB (P1/P2/P3, E1/E2/E3) suportados?**

Esses novos tamanhos de disco são atualmente suportados no Azure West Central US.

**Os tamanhos de disco P1/P2/P3 são suportados para discos não gerenciados ou bolhas de página?**

Não, ele só é suportado em discos gerenciados premium SSD. 

**Os tamanhos de disco E1/E2/E3 são suportados para discos não gerenciados ou bolhas de página?**

Não, discos gerenciados padrão de SSD de qualquer tamanho não podem ser usados com discos não gerenciados ou bolhas de página.

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

**Você pode redimensionar discos gerenciados existentes de tamanhos inferiores a 4 tebibytes (TiB) para novos tamanhos de disco recém-introduzidos até 32 TiB?**

Sim.

**Quais são os maiores tamanhos de disco suportados pelo serviço de backup do Azure e do Azure Site Recovery?**

O maior tamanho de disco suportado pelo Azure Backup é 32 TiB (4 TiB para discos criptografados). O maior tamanho de disco suportado pelo Azure Site Recovery é 8 TiB. O suporte para discos maiores até 32 TiB ainda não está disponível no Azure Site Recovery.

**Quais são os tamanhos de VM recomendados para tamanhos de disco maiores (>4 TiB) para discos HdD padrão e Padrão para obter iOPS de disco otimizado e largura de banda?**

Para obter o throughput de disco de tamanhos de disco grandes SSD padrão e HDD padrão (>4 TiB) além de 500 IOPS e 60 MiB/s, recomendamos que você implante uma nova VM de um dos seguintes tamanhos de VM para otimizar seu desempenho: Série B, Série DSv2, Série DSv3, Série ESv3, Série Fsv2, Série M, Série M, Série GS, Série NCv2, Série NCv3 ou VMs da série LS. Anexar discos grandes a VMs ou VMs existentes que não estão usando os tamanhos recomendados acima pode ter um desempenho menor.

**Como posso atualizar meus discos (>4 TiB) que foram implantados durante a visualização de tamanhos de disco maiores, a fim de obter a maior largura de banda de & IOPS na GA?**

Você pode parar e iniciar a VM à que o disco está conectado ou, desprender e reconectar seu disco. As metas de desempenho de tamanhos de disco maiores foram aumentadas tanto para SSDs premium quanto para SSDs padrão na GA.

**Em quais regiões são suportadas os tamanhos de disco gerenciados de 8 TiB, 16 TiB e 32 TiB?**

Os 8 TiB, 16 TiB e 32 SKUs de disco TiB são suportados em todas as regiões o global Azure, Microsoft Azure Government e Azure China 21Vianet.

**Suportamos ativar o cache do Host em todos os tamanhos de disco?**

Nós suportamos cache host de ReadOnly e Read/Write em tamanhos de disco inferiores a 4 TiB. Para tamanhos de disco superiores a 4 TiB, não suportamos opção de cache de configuração diferente de Nenhuma. É recomendável aproveitar o cache para tamanhos menores de disco em que você pode esperar para observar o aumento de desempenho melhor com dados armazenados em cache para a máquina virtual.

## <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?

Se sua pergunta não estiver listada aqui, fale conosco e nós ajudaremos a encontrar uma resposta. Você pode postar uma pergunta no final deste artigo nos comentários. Para se comunicar com a equipe do Armazenamento do Azure e outros membros da comunidade sobre este artigo, use o [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) no MSDN.

Para solicitar recursos, envie suas solicitações e ideias para o [Fórum de comentários do Armazenamento do Azure](https://feedback.azure.com/forums/217298-storage).
