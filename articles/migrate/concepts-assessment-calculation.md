---
title: Avaliações na avaliação do servidor azure migrate
description: Saiba mais sobre avaliações na avaliação do servidor azure migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d1f32eea0ec6a8a4877fd1dc134344cfe68dcaba
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537756"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Avaliações no Azure Migrate:Avaliação do servidor

Este artigo fornece uma visão geral das avaliações na ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) A ferramenta Avaliação do Servidor pode avaliar vMs VMware no local, VMs hyper-V e servidores físicos, para migração para o Azure.

## <a name="whats-an-assessment"></a>O que é uma avaliação?

Uma avaliação com a ferramenta Avaliação de Servidores mede a prontidão e estima o impacto da migração de servidores locais para o Azure.

> [!NOTE]
> No Governo Azure, revise os locais de avaliação [dos alvos apoiados.](migrate-support-matrix.md#supported-geographies-azure-government) Observe que as recomendações de tamanho de VM nas avaliações usarão a série VM especificamente para regiões de Nuvem de Governo. [Saiba mais](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) sobre os tipos de VM.

## <a name="types-of-assessments"></a>Tipos de avaliações

As avaliações criadas com a Avaliação do Servidor são um instantâneo pontual dos dados. A Avaliação do Servidor fornece dois tipos de avaliações.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho coletados | A recomendação de tamanho vm é baseada em dados de utilização de CPU e memória.<br/><br/> A recomendação do tipo de disco (HDD/SSD padrão ou discos gerenciados premium) é baseada no IOPS e no throughput dos discos on-premises.
**As-is on-premises** | Avaliações que não usam dados de desempenho para fazer recomendações. | A recomendação do tamanho da VM é baseada no tamanho da VM no local<br/><br> O tipo de disco recomendado é baseado no tipo de armazenamento selecionado para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?

Há algumas maneiras de fazer uma avaliação:

- Avalie as máquinas usando metadados de servidor coletados por um aparelho Leve Azure Migrate. O aparelho descobre máquinas no local e envia dados de metadados/desempenho da máquina para o Azure Migrate.
- Avalie as máquinas usando metadados de servidor importados em um formato CSV (Comma-separated Values).

## <a name="how-do-i-assess-with-the-appliance"></a>Como avaliar com o aparelho?

Se você estiver implantando um aparelho Azure Migrate para descobrir servidores no local, faça o seguinte:

1. Você configura o Azure e seu ambiente local para trabalhar com avaliação do servidor.
2. Para sua primeira avaliação, você cria um projeto Azure e adiciona a ferramenta Avaliação do servidor a ele.
3. Você implanta um aparelho Leve Azure Migrate. O aparelho descobre continuamente máquinas no local e envia metadados da máquina e dados de desempenho para o Azure Migrate. O aparelho é implantado como uma VM ou uma máquina física. Não há necessidade de instalar nada em máquinas que você queira avaliar.
4. Depois que o aparelho começa a ser descoberto, você pode reunir máquinas que deseja avaliar em um grupo e executar uma avaliação para o grupo.

Você pode seguir nossos tutoriais para [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)ou servidores físicos para testar essas [etapas.](tutorial-prepare-physical.md)

## <a name="how-do-i-assess-with-imported-data"></a>Como avaliar com dados importados?

Se você estiver avaliando servidores usando um arquivo CSV, você não precisa de um aparelho. Em vez disso, você faz o seguinte:

1. Você configurou o Azure para trabalhar com avaliação do servidor.
2. Para sua primeira avaliação, você cria um projeto Azure e adiciona a ferramenta Avaliação do servidor a ele.
3. Você baixa um modelo csv e adiciona dados do servidor a ele.
4. Você importa o modelo para avaliação do servidor.
5. Você descobre servidores adicionados com a importação, reúne-se em um grupo e executa uma avaliação para o grupo.

## <a name="what-data-does-the-appliance-collect"></a>Quais dados o aparelho coleta?

Se você estiver usando o aparelho Azure Migrate para avaliação, conheça os metadados e dados de desempenho coletados para [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como o aparelho calcula os dados de desempenho?

Se você usar o aparelho para detecção, os dados de desempenho para as configurações de computação são coletados da seguinte forma:

1. O aparelho coleta um ponto de amostra em tempo real:

    - **VMware VMs**: O aparelho coleta um ponto de amostra em tempo real a cada intervalo de 20 segundos.
    - **Hiper-VV :** O ponto de amostra em tempo real é coletado a cada intervalo de 30 segundos.
    - **Servidores físicos**: O ponto amostral em tempo real é coletado a cada intervalo de cinco minutos. 
    
2. O aparelho acumula os pontos de amostra (20 segundos, 30 segundos e cinco minutos) para criar um único ponto de dados a cada 10 minutos. Para criar o ponto único, o aparelho seleciona o valor de pico de todas as amostras e, em seguida, envia-o para o Azure.
3. A Avaliação do Servidor armazena todos os pontos de amostra de 10 minutos do último mês.
4. Quando você cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados apropriado para uso para o dimensionamento certo, com base nos valores de percentil para *histórico de desempenho* e utilização por *cento*.

    - Por exemplo, se o histórico de desempenho for definido para uma semana e a utilização por percentil for o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos para a última semana em ordem crescente e escolhe o valor do percentil 95 para o dimensionamento certo. 
    - O valor do percentil 95 garante que você ignore qualquer outliers, que podem ser incluídos se você escolher o percentil 99.
    - Se você quiser escolher o pico de uso para o período e não quiser perder nenhum outliers, você deve selecionar o percentil 99 para utilização por percentil.

5. Esse valor é multiplicado pelo fator de conforto para obter os dados efetivos de utilização de desempenho para cada métrica (utilização da CPU, utilização da memória, IOPS de disco (leitura e gravação), throughput de disco (leitura e gravação) e throughput de rede (dentro e fora) que o aparelho coleta.



## <a name="how-are-assessments-calculated"></a>Como são calculadas as avaliações? 

As avaliações na Avaliação do Servidor são calculadas usando metadados/dados de desempenho para as máquinas locais. Se você implantar o aparelho Azure Migrate, então faça a avaliação usando os dados coletados pelo aparelho. Se você executar uma avaliação para máquinas importadas usando um . Arquivo CSV, você fornece os metadados para o cálculo. Os cálculos ocorrem em três estágios:

1. **Calcular prontidão do Azure**: Avalie se as máquinas são adequadas para migração para o Azure.
2. **Calcular recomendações de dimensionamento**: Estimar cálculo, armazenamento e dimensionamento de rede. 
2. **Calcular custos mensais**: Calcule os custos mensais estimados de computação e armazenamento para executar as máquinas no Azure após a migração.

Os cálculos estão em ordem, e um servidor de máquina se move para um estágio posterior apenas se ele passar o anterior. Por exemplo, se um servidor falha na prontidão do Azure, ele é marcado como inadequado para o Azure, e o dimensionamento e o custo não são feitos para esse servidor.


## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

Aqui está o que incluiu em uma avaliação na Avaliação do Servidor.

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local para o qual você deseja migrar. A avaliação do servidor atualmente suporta essas regiões azure alvo:<br/><br/> Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Central dos EUA, China Leste, China Norte, Leste da Ásia, Leste dos EUA, Leste dos EUA2, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Ocidental, Coréia Central, Coréia do Sul, Norte central dos EUA, Europa do Norte, Centro-Sul dos EUA, Sudeste Asiático, Índia do Sul, Reino Unido, Reino Unido Ocidental, US Gov Arizona, US Gov Texas, US Gov Virginia , Centro-Oeste dos EUA, Europa Ocidental, Índia Ocidental, OESTE DOS EUA e US2 Oeste.
*Disco de armazenamento de destino (como o dimensionamento)** | O tipo de discos para armazenamento no Azure. <br/><br/> Especifique o disco de armazenamento de destino como gerenciado por SSD premium, gerenciado por SSD padrão ou HDD padrão.
**Disco de armazenamento de destino (dimensionamento baseado em desempenho)** | Especifique o tipo de disco de armazenamento de destino como ssd padrão, gerenciado por gerenciamento de HDD padrão ou padrão.<br/><br/> **Automático**: A recomendação do disco é baseada nos dados de desempenho dos discos (as operações de entrada/saída por segundo (IOPS) e throughput).<br/><br/>**Premium/padrão**: A avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se você quiser alcançar um SLA VM de uma única instância de 99,9%, considerando o uso de discos gerenciados premium. Isso garante que todos os discos da avaliação sejam recomendados como discos gerenciados premium.<br/><br/> As Migrações para Azure são compatíveis com discos gerenciados apenas para avaliação de migração.
**Instâncias reservadas (RIs)** | Especifique [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, de modo que as estimativas de custos na avaliação levem em conta os descontos de RI.<br/><br/> Atualmente, os RIs são suportados apenas para ofertas pay-as-you-go no Azure Migrate.
**Critérios de dimensionamento** | Usado para o tamanho certo da VM em Azure.<br/><br/> Use dimensionamento as-is dimensionamento ou dimensionamento baseado em desempenho.
**Histórico de desempenho** | Usado com dimensionamento baseado em desempenho. Especifique a duração usada ao avaliar dados de desempenho.
**Utilização de percentual** | Usado com dimensionamento baseado em desempenho. Especifica o valor percentil da amostra de desempenho a ser usada para o dimensionamento certo. 
**Série VM** | Especifique a série VM do Azure que você deseja considerar para o dimensionamento certo. Por exemplo, se você não tiver um ambiente de produção que precise de VMs da série A no Azure, você pode excluir séries A da lista ou série.
**Fator de conforto** | Buffer usado durante a avaliação. Aplicado em cima de dados de utilização de máquinas para VMs (CPU, memória, disco e rede). Ele explica questões como uso sazonal, histórico de desempenho curto e aumentos prováveis no uso futuro.<br/><br/> Por exemplo, uma VM de 10 núcleos com 20% de utilização normalmente resulta em uma VM de dois núcleos. Com um fator de conforto de 2,0x, o resultado é uma VM de quatro núcleos.
**Oferta** | Exibe a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está matriculado. A Avaliação do Servidor estima o custo em conformidade.
**Moeda** | Faturando dinheiro para sua conta.
**Desconto (%)** | Lista quaisquer descontos específicos de assinatura que você recebe em cima da oferta do Azure. A configuração padrão é 0%.
**Tempo de atividade da VM** | Se as VMs do Azure não funcionarem 24 horas por dia, 7 dias por semana, você poderá especificar a duração (dias por mês e horas por dia) que serão executadas. As estimativas de custos são tratadas em conformidade.<br/><br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se você tem garantia de software e é elegível para [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim (a configuração padrão), os preços do Azure não-Windows são considerados para VMs do Windows.

[Revise as melhores práticas](best-practices-assessment.md) para criar avaliação com avaliação do servidor.


## <a name="calculate-readiness"></a>Calcular prontidão

Nem todas as máquinas são adequadas para funcionar no Azure. A Avaliação do Servidor avalia cada máquina no local e atribui-a uma categoria de prontidão. 
- **Pronto para o Azure**: A máquina pode ser migrada como está para o Azure sem quaisquer alterações. Começará no Azure com suporte total ao Azure.
- **Condicionadamente pronto para Azure**: A máquina pode começar no Azure, mas pode não ter suporte completo do Azure. Por exemplo, uma máquina que está executando uma versão mais antiga do Windows Server não é suportada no Azure. Você deve ter cuidado antes de migrar essas máquinas para o Azure. Siga as orientações de remediação sugeridas na avaliação para corrigir os problemas de prontidão.
- **Não está pronto para o Azure**: A máquina não vai começar em Azure. Por exemplo, se um disco de máquina no local for superior a 64 TBs, ele não poderá ser hospedado no Azure. Siga a orientação de remediação para corrigir o problema antes da migração. 
- **Prontidão desconhecida**: O Azure Migrate não conseguiu determinar a prontidão de uma máquina, devido a metadados insuficientes.

Para calcular a prontidão, a Avaliação do Servidor analisa as propriedades da máquina e as configurações do sistema operacional resumidas nas tabelas a seguir. 

### <a name="machine-properties"></a>Propriedades do computador

A Avaliação do Servidor analisa as seguintes propriedades da VM no local para determinar se ela pode ser executada no Azure.

**Propriedade** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
**Tempo de inicialização** | O Azure suporta VMs com um tipo de inicialização de BIOS, não UEFI. | Esteja condicionado se o tipo de inicialização for UEFI.
**Núcleos** | O número de núcleos nas máquinas deve ser igual ou menor do que o número máximo de núcleos (128) suportados para uma VM Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados para comparação. Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, o Azure Migrate usa os núcleos alocados sem aplicar o fator de conforto. | Pronto, se for menor que ou igual aos limites.
**Memória** | O tamanho da memória da máquina deve ser igual ou menor do que a memória máxima (3892 gigabytes [GB] na série Azure M Standard_M128m&nbsp;<sup>2</sup>) permitida para um Azure VM. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão a memória utilizada para comparação. Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a memória alocada é usada sem aplicar o fator de conforto.<br/><br/> | Se estiver pronto dentro dos limites.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 32 TB ou menos. Embora o Azure suporte discos de 64 TB com discos Ultra SSD, o Azure Migrate: Server Assessment atualmente verifica para 32 TB como o tamanho do disco limita, pois ainda não suporta Ultra SSD. <br/><br/> O número de discos conectados à máquina deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Se estiver pronto dentro dos limites.
**Rede** | Uma máquina deve ter 32 ou menos interfaces de rede (NICs) conectadas a ela. | Se estiver pronto dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operacional convidado
Juntamente com as propriedades vm, o Server Assessment analisa o sistema operacional convidado das máquinas para determinar se ele pode ser executado no Azure.

> [!NOTE]
> Para VMs VMware, o Server Assessment usa o sistema operacional especificado para a VM no vCenter Server para lidar com a análise do sistema operacional convidado. Para VMs Linux em execução no VMware, atualmente não identifica a versão exata do kernel do sistema operacional convidado.

A lógica a seguir é usada pela Avaliação do Servidor para identificar a prontidão do Azure com base no sistema operacional.

**Sistema operacional** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte total.| Pronto para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2003, 2003 R2 | Esses sistemas operacionais passaram da data de fim de suporte e precisam de um [Contrato de Suporte Personalizado (CSA)](https://aka.ms/WSosstatement) para suporte no Azure. | Condicionalmente pronto para Azure. Considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Esses sistemas operacionais passaram da data de término do suporte. A máquina pode começar no Azure, mas o Azure não oferece suporte ao SO. | Condicionalmente pronto para Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Cliente do Windows 7, 8 e 10 | O Azure oferece suporte apenas [com a Assinatura do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente pronta para o Azure
Windows 10 Pro Desktop | O Azure oferece suporte com [Direitos de Hospedagem multilocatário.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente pronta para o Azure
Windows Vista, XP Professional | Esses sistemas operacionais passaram da data de término do suporte. A máquina pode começar no Azure, mas o Azure não oferece suporte ao SO. | Condicionalmente pronto para Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Linux | O Azure endossa esses [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas operacionais Linux podem começar no Azure, mas recomendamos que você atualize o Sistema Operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão é aprovada.<br/><br/>Condicionalmente pronto se a versão não é aprovada.
Outros sistemas operacionais<br/><br/> Por exemplo, Oracle Solaris, Apple macOS etc., FreeBSD, etc. | O Azure não endossa esses sistemas operacionais. A máquina pode começar no Azure, mas o Azure não oferece suporte ao SO. | Condicionalmente pronto para Azure. Recomendamos que você instale um sistema operacional suportado antes de migrar para o Azure.  
Sistema operacional especificado como **Outros** no vCenter Server | As Migrações para Azure não podem identificar o sistema operacional neste caso. | Preparação desconhecida. Certifique-se de que o sistema operacional em execução dentro da VM tem suporte no Azure.
Sistemas operacionais de 32 bits | A máquina pode começar no Azure, mas o Azure pode não fornecer suporte total. | Condicionalmente pronto para Azure. Considere atualizar o Sistema Operacional da máquina de 32 bits para o SO de 64 bits antes de migrar para o Azure.

## <a name="calculating-sizing"></a>Calculando o dimensionamento


Depois que a máquina é marcada como pronta para o Azure, a Avaliação do Servidor faz recomendações de dimensionamento para identificar o Azure VM e o SKU do disco. Os cálculos de dimensionamento dependem se você está usando o dimensionamento no local ou o dimensionamento baseado em desempenho.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (como está no local)

 Se você usar o dimensionamento no local, a Avaliação do Servidor não considerará o histórico de desempenho das VMs e discos.

- **Dimensionamento de cálculo**: Ele aloca um Azure VM SKU com base no tamanho alocado no local.
- **Dimensionamento de armazenamento/disco:** A avaliação do servidor analisa o tipo de armazenamento especificado nas propriedades de avaliação (HDD/SSD/premium padrão) e recomenda o tipo de disco de acordo. O tipo de armazenamento padrão são discos premium.
- **Dimensionamento da rede**: A avaliação do servidor considera o adaptador de rede na máquina no local.


### <a name="calculate-sizing-performance-based"></a>Calcular dimensionamento (baseado em desempenho)

Se você usar o dimensionamento de base de desempenho, a avaliação do servidor faz recomendações de dimensionamento da seguinte forma:

- A Avaliação do Servidor considera o histórico de desempenho da máquina para identificar o tamanho da VM e o tipo de disco no Azure.
- Se os servidores tiverem sido importados usando um arquivo CSV, os valores especificados serão usados. Este método é especialmente útil se você tiver alocado demais a máquina no local, a utilização é baixa e você deseja dimensionar corretamente o VM no Azure para economizar custos. 
- Se você não quiser usar os dados de desempenho, reconfigure os critérios de dimensionamento para as-está no local, conforme descrito na seção anterior.

#### <a name="calculate-storage-sizing"></a>Calcular o dimensionamento do armazenamento

Para dimensionamento de armazenamento, o Azure Migrate tenta mapear cada disco conectado à máquina a um disco no Azure e funciona da seguinte forma:

1. A Avaliação do Servidor adiciona o IOPS de leitura e gravação de um disco para obter o IOPS total necessário. Da mesma forma, adiciona os valores de leitura e gravação de throughput para obter o throughput total de cada disco.
2. Se você especificou o tipo de armazenamento como Automático, com base nos valores eficazes de IOPS e throughput, a Avaliação do Servidor determinará se o disco deve ser mapeado para um HDD padrão, SSD padrão ou um disco premium no Azure. Se o tipo de armazenamento estiver definido como HDD/SSD/Premium padrão, a Avaliação do Servidor tentará encontrar um SKU de disco dentro do tipo de armazenamento selecionado (discos HDD/SSD/Premium padrão).
3. Os discos são selecionados da seguinte forma:
    - Se a Avaliação do Servidor não conseguir encontrar um disco com o IOPS e o throughput necessários, ele marcará a máquina como inadequada para o Azure.
    - Se a Avaliação do Servidor encontrar um conjunto de discos adequados, ele selecionará os discos que suportam o local especificado nas configurações de avaliação.
    - Se houver vários discos elegíveis, a Avaliação do Servidor selecionará o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, os dados de configuração do disco (tamanho do disco) são usados para encontrar um disco SSD padrão no Azure.

#### <a name="calculate-network-sizing"></a>Calcular o dimensionamento da rede

A Avaliação do Servidor tenta encontrar uma VM Azure que possa suportar o número de adaptadores de rede conectados à máquina no local e o desempenho exigido por esses adaptadores de rede.
- Para obter o desempenho efetivo da rede vm no local, o Server Assessment agrega os dados transmitidos por segundo (MBps) para fora da máquina (rede fora), em todos os adaptadores de rede, e aplica o fator de conforto. Ele usa esse número para encontrar uma VM Azure que possa suportar o desempenho de rede necessário.
- Juntamente com o desempenho da rede, o Server Assessment também considera se o Azure VM pode suportar o número necessário de adaptadores de rede.
- Se não houver dados de desempenho de rede disponíveis, a Avaliação do Servidor considerará apenas a contagem de adaptadores de rede para o dimensionamento de VM.


#### <a name="calculate-compute-sizing"></a>Calcular dimensionamento de cálculo

Depois de calcular os requisitos de armazenamento e rede, o Server Assessment considera os requisitos de CPU e memória para encontrar um tamanho de VM adequado no Azure.
- O Azure Migrate analisa os núcleos e a memória efetivamente utilizados para encontrar um tamanho de VM adequado no Azure.
- Se nenhum tamanho adequado for encontrado, a máquina será marcada como inadequada para o Azure.
- Se um tamanho adequado for encontrado, as Migrações para Azure aplicarão os cálculos de armazenamento e rede. Em seguida, aplica as configurações de localização e nível de preços para a recomendação final de tamanho da VM.
- Se houver vários discos qualificados, será recomendado aquele com o menor custo.


## <a name="confidence-ratings-performance-based"></a>Classificações de confiança (baseadas em desempenho)

Cada avaliação baseada em desempenho no Azure Migrate está associada a uma classificação de confiança que varia de uma (menor) a cinco estrelas (mais alta). O índice de confiança ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.

- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado em desempenho, a avaliação do servidor precisa:
    - Os dados de utilização da memória CPU e VM.
    - O disco IOPS e os dados de throughput para cada disco conectado à VM.
    - A I/O da rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede conectado a uma VM.
    - Se algum desses números de utilização não estiver disponível, as recomendações de tamanho podem não ser confiáveis.

> [!NOTE]
> As classificações de confiança não são atribuídas para servidores avaliados usando um importado . Arquivo CSV. As classificações também não são aplicáveis para avaliação no local.
   
### <a name="ratings"></a>Classificações

Dependendo da porcentagem de pontos de dados disponíveis, o índice de confiança para a avaliação é o seguinte.

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0-20% | 1 estrela
   21-40% | 2 estrelas
   41-60% | 3 estrelas
   61-80% | 4 estrelas
   81-100% | 5 estrelas

### <a name="low-confidence-ratings"></a>Baixas taxas de confiança

Aqui estão algumas razões pelas quais uma avaliação pode obter uma baixa taxa de confiança:

- Você não perfilou seu ambiente durante a duração para a qual você está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida para um dia, você deve esperar pelo menos um dia após iniciar a descoberta para que todos os pontos de dados sejam coletados.
- Algumas VMs foram desligadas durante o período sujeito à avaliação. Se quaisquer VMs forem desligados por algum tempo, a Avaliação do Servidor não poderá coletar os dados de desempenho desse período.
- Algumas VMs foram criadas durante o período para o qual a avaliação foi calculada. Por exemplo, se você criou uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente há apenas uma semana, o histórico de desempenho das novas VMs não existirá durante toda a duração.

> [!NOTE]
> Se a avaliação for inferior a cinco estrelas, recomendamos que você espere pelo menos um dia para que o aparelho faça o perfil do ambiente e, em seguida, recalcule a avaliação. Se você não fizer isso, o dimensionamento baseado em desempenho pode não ser confiável. Nesse caso, recomendamos que você mude a avaliação para o dimensionamento no local.

## <a name="calculate-monthly-costs"></a>Calcular custos mensais

Depois que as recomendações de dimensionamento são concluídas, o Azure Migrate calcula os custos de computação e armazenamento para após a migração.

- **Custo de computação**: usando o tamanho recomendado de VM do Azure, as Migrações para Azure usam a API de Cobrança para calcular o custo mensal para a VM.
    - O cálculo leva em conta sistema operacional, Software Assurance, instâncias reservadas, tempo de atividade da VM, localização e configurações de moeda.
    - Ele agrega o custo em todas as máquinas para calcular o custo total mensal do cálculo.
- **Custo de armazenamento**: O custo mensal de armazenamento de uma máquina é calculado agregando o custo mensal de todos os discos conectados à máquina, da seguinte forma:
    - A Avaliação do Servidor calcula os custos totais mensais de armazenamento agregando os custos de armazenamento de todas as máquinas.
    - Atualmente, o cálculo não considera ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação.


## <a name="next-steps"></a>Próximas etapas

[Revise](best-practices-assessment.md) as melhores práticas para criar avaliações. 


- Saiba mais sobre a execução de avaliações para [VMs VMware,](tutorial-prepare-vmware.md) [Hyper-VMs](tutorial-prepare-hyper-v.md)e [servidores físicos.](tutorial-prepare-physical.md)
- Saiba como avaliar servidores [importados com um arquivo CSV](tutorial-assess-import.md).
- Saiba mais sobre a criação de [visualização de dependência](concepts-dependency-visualization.md).
