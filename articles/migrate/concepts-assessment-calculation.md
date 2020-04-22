---
title: Avaliações na avaliação do servidor azure migrate
description: Saiba mais sobre avaliações na avaliação do servidor azure migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769931"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Avaliações no Azure Migrate: Avaliação do servidor

Este artigo fornece uma visão geral das avaliações na ferramenta [Azure Migrate: Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) A ferramenta pode avaliar máquinas virtuais VMware no local, VMs Hyper-V e servidores físicos para migração para o Azure.

## <a name="whats-an-assessment"></a>O que é uma avaliação?

Uma avaliação com a ferramenta Avaliação de Servidores mede a prontidão e estima o efeito da migração de servidores locais para o Azure.

> [!NOTE]
> No Governo Azure, revise os locais de avaliação [dos alvos apoiados.](migrate-support-matrix.md#supported-geographies-azure-government) Observe que as recomendações de tamanho de VM nas avaliações usarão a série VM especificamente para regiões de Nuvem de Governo. [Saiba mais](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) sobre os tipos de VM.

## <a name="types-of-assessments"></a>Tipos de avaliações

As avaliações criadas com a Avaliação do Servidor são um instantâneo pontual dos dados. A Avaliação do Servidor fornece dois tipos de avaliações.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho coletados | A recomendação de tamanho vm é baseada em dados de utilização de CPU e RAM.<br/><br/> A recomendação do tipo disco é baseada nas operações de entrada/saída por segundo (IOPS) e no throughput dos discos on-premises. Os tipos de disco são os discos Azure Standard HDD, Azure Standard SSD e Azure Premium.
**As-is on-premises** | Avaliações que não usam dados de desempenho para fazer recomendações | A recomendação de tamanho vm é baseada no tamanho da VM no local.<br/><br> O tipo de disco recomendado é baseado no tipo de armazenamento selecionado para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?

Há algumas maneiras de fazer uma avaliação.

- Avalie as máquinas usando metadados de servidor coletados por um aparelho leve do Azure Migrate. O dispositivo descobre computadores locais. Em seguida, envia metadados da máquina e dados de desempenho para o Azure Migrate.
- Avalie as máquinas usando metadados de servidor importados em um formato CSV (Comma-separated Values).

## <a name="how-do-i-assess-with-the-appliance"></a>Como avaliar com o aparelho?

Se você estiver implantando um aparelho Azure Migrate para descobrir servidores no local, faça as seguintes etapas:

1. Configure o Azure e seu ambiente local para trabalhar com avaliação do servidor.
1. Para sua primeira avaliação, crie um projeto Do Zure e adicione a ferramenta Avaliação do Servidor a ele.
1. Implante um aparelho leve Azure Migrate. O aparelho descobre continuamente máquinas no local e envia metadados da máquina e dados de desempenho para o Azure Migrate. Implante o aparelho como uma VM ou uma máquina física. Você não precisa instalar nada em máquinas que você deseja avaliar.

Depois que o aparelho começa a ser descoberto, você pode reunir máquinas que deseja avaliar em um grupo e executar uma avaliação para o grupo.

Siga nossos tutoriais para [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)ou servidores físicos para testar essas [etapas.](tutorial-prepare-physical.md)

## <a name="how-do-i-assess-with-imported-data"></a>Como avaliar com dados importados?

Se você está avaliando servidores usando um arquivo CSV, você não precisa de um aparelho. Em vez disso, faça as seguintes etapas:

1. Configure o Azure para trabalhar com avaliação do servidor.
1. Para sua primeira avaliação, crie um projeto Do Zure e adicione a ferramenta Avaliação do Servidor a ele.
1. Baixe um modelo csv e adicione dados do servidor a ele.
1. Importe o modelo para avaliação do servidor.
1. Descubra servidores adicionados com a importação, reúna-os em um grupo e execute uma avaliação para o grupo.

## <a name="what-data-does-the-appliance-collect"></a>Quais dados o aparelho coleta?

Se você estiver usando o aparelho Azure Migrate para avaliação, conheça os metadados e dados de desempenho coletados para [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como o aparelho calcula os dados de desempenho?

Se você usar o aparelho para ser descoberto, ele coletará dados de desempenho para configurações de computação com estas etapas:

1. O aparelho coleta um ponto de amostra em tempo real.

    - **VMware VMs**: Um ponto de amostra é coletado a cada 20 segundos.
    - **Hiper-VMs**: Um ponto de amostra é coletado a cada 30 segundos.
    - **Servidores físicos**: Um ponto de amostra é coletado a cada cinco minutos.

1. O aparelho combina os pontos de amostra para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados, o aparelho seleciona os valores de pico de todas as amostras. Em seguida, envia o ponto de dados para o Azure.
1. A Avaliação do Servidor armazena todos os pontos de dados de 10 minutos do último mês.
1. Quando você cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados apropriado para usar para a rightsizing. A identificação é baseada nos valores por cento para histórico de *desempenho* e *utilização por cento*.

    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização por percentil for o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos para a última semana. Ele os classifica em ordem crescente e escolhe o valor do percentil 95 para a rightsizing.
    - O valor do percentil 95 faz com que você ignore qualquer outliers, que podem ser incluídos se você escolheu o percentil 99.
    - Se você quiser escolher o pico de uso para o período e não quiser perder nenhum outliers, selecione o percentil 99 para utilização por percentil.

1. Esse valor é multiplicado pelo fator de conforto para obter os dados efetivos de utilização de desempenho para essas métricas que o aparelho coleta:

    - Utilização da CPU
    - Utilização de RAM
    - IOPS de disco (ler e escrever)
    - Throughput de disco (ler e gravar)
    - Throughput de rede (dentro e fora)

## <a name="how-are-assessments-calculated"></a>Como são calculadas as avaliações?

A Avaliação do Servidor usa os dados de metadados e desempenho das máquinas locais para calcular avaliações. Se você implantar o aparelho Azure Migrate, a avaliação usará os dados coletados pelo aparelho. Mas se você executar uma avaliação importada usando um arquivo CSV, você fornecerá os metadados para o cálculo.

Os cálculos ocorrem nessas três etapas:

1. **Calcular prontidão do Azure**: Avalie se as máquinas são adequadas para migração para o Azure.
1. **Calcular recomendações de dimensionamento**: Estimar cálculo, armazenamento e dimensionamento de rede.
1. **Calcular custos mensais**: Calcule os custos mensais estimados de computação e armazenamento para executar as máquinas no Azure após a migração.

Os cálculos estão na ordem anterior. Um servidor de máquina se move para um estágio posterior apenas se passar o anterior. Por exemplo, se um servidor falhar no estágio de prontidão do Azure, ele será marcado como inadequado para o Azure. Cálculos de dimensionamento e custos não são feitos para esse servidor.

## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

Aqui está o que está incluído em uma avaliação na avaliação do servidor:

Propriedade | Detalhes
--- | ---
**Local de destino** | O local para o qual você deseja migrar. A avaliação do servidor atualmente suporta essas regiões azure alvo:<br/><br/> Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Central dos EUA, China Leste, China Norte, Leste da Ásia, Leste dos EUA, Leste dos EUA 2, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Ocidental, Coréia Central, Coréia do Sul, Norte central dos EUA, Europa do Norte, Centro-Sul dos EUA, Sudeste Asiático, Índia do Sul, Reino Unido, Reino Unido Ocidental, US Gov Arizona, US Gov Texas, US Gov Virginia , Centro-Oeste dos EUA, Europa Ocidental, Índia Ocidental, OESTE DOS EUA e Oeste dos EUA 2.
**Disco de armazenamento de destino (como o dimensionamento)** | O tipo de disco a ser usado para armazenamento no Azure. <br/><br/> Especifique o disco de armazenamento de destino como gerenciado por Premium, gerenciado por SSD padrão ou gerenciado por HDD padrão.
**Disco de armazenamento de destino (dimensionamento baseado em desempenho)** | Especifica o tipo de disco de armazenamento de destino como automático, gerenciado por Premium, gerenciado por HDD padrão ou gerenciado por SSD padrão.<br/><br/> **Automático**: A recomendação do disco é baseada nos dados de desempenho dos discos, ou seja, o IOPS e o throughput.<br/><br/>**Premium ou Padrão**: A avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se você quiser um Contrato de nível de serviço (SLA) de uma única instância vm de 99,9%, considere usar discos gerenciados por Premium. Esse uso garante que todos os discos da avaliação sejam recomendados como discos gerenciados por Premium.<br/><br/> O Azure Migrate suporta apenas discos gerenciados para avaliação de migração.
**Instâncias da máquina virtual reservada do Azure** | Especifica [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) para que as estimativas de custos na avaliação as levem em conta.<br/><br/> Atualmente, o Azure Migrate suporta atualmente o Azure Reserve VM Instances apenas para ofertas de pagamento à medida que você vai.
**Critérios de dimensionamento** | Usado para dimensionar corretamente a VM Azure.<br/><br/> Use dimensionamento as-is dimensionamento ou dimensionamento baseado em desempenho.
**Histórico de desempenho** | Usado com dimensionamento baseado em desempenho. O histórico de desempenho especifica a duração usada quando os dados de desempenho são avaliados.
**Utilização de percentual** | Usado com dimensionamento baseado em desempenho. A utilização por cento especifica o valor percentual da amostra de desempenho usada para a compreensão.
**Série VM** | A série Azure VM que você deseja considerar para direitos. Por exemplo, se você não tiver um ambiente de produção que precise de VMs da série A no Azure, você pode excluir a série A da lista de séries.
**Fator de conforto** | O buffer usado durante a avaliação. Ele é aplicado aos dados de utilização de CPU, RAM, disco e rede para VMs. Ele explica questões como uso sazonal, histórico de desempenho curto e aumentos prováveis no uso futuro.<br/><br/> Por exemplo, uma VM de 10 núcleos com 20% de utilização normalmente resulta em uma VM de dois núcleos. Com um fator de conforto de 2.0, o resultado é uma VM de quatro núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está matriculado. A Avaliação do Servidor estima o custo dessa oferta.
**Moeda** | A moeda de faturamento da sua conta.
**Desconto (%)** | Quaisquer descontos específicos de assinatura que você recebe em cima da oferta do Azure. A configuração padrão é 0%.
**Tempo de atividade da VM** | A duração em dias por mês e horas por dia para As VMs do Azure que não funcionarão continuamente. As estimativas de custos são baseadas nessa duração.<br/><br/> Os valores padrão são 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se você tem garantia de software e é elegível para [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se a configuração tiver o valor padrão "Sim", os preços do Azure para sistemas operacionais diferentes do Windows são considerados para VMs windows.

[Revise as práticas recomendadas](best-practices-assessment.md) para criar uma avaliação com avaliação do servidor.

## <a name="calculate-readiness"></a>Calcular prontidão

Nem todas as máquinas são adequadas para funcionar no Azure. A Avaliação do Servidor avalia todas as máquinas no local e atribui-lhes uma categoria de prontidão.

- **Pronto para o Azure**: A máquina pode ser migrada como está para o Azure sem quaisquer alterações. Começará no Azure com suporte total ao Azure.
- **Condicionadamente pronto para OZure**: A máquina pode começar no Azure, mas pode não ter suporte completo do Azure. Por exemplo, o Azure não suporta uma máquina que está executando uma versão antiga do Windows Server. Você deve ter cuidado antes de migrar essas máquinas para o Azure. Para corrigir quaisquer problemas de prontidão, siga a orientação de remediação que a avaliação sugere.
- **Não está pronto para o Azure**: A máquina não vai começar em Azure. Por exemplo, se o disco de uma máquina no local armazena mais de 64 TB, o Azure não pode hospedar a máquina. Siga a orientação de remediação para corrigir o problema antes da migração.
- **Prontidão desconhecida**: O Azure Migrate não pode determinar a prontidão da máquina devido a metadados insuficientes.

Para calcular a prontidão, a Avaliação do Servidor analisa as propriedades da máquina e as configurações do sistema operacional resumidas nas tabelas a seguir.

### <a name="machine-properties"></a>Propriedades do computador

A Avaliação do Servidor analisa as seguintes propriedades de uma VM no local para determinar se ela pode ser executada no Azure.

Propriedade | Detalhes | Status de preparação do Azure
--- | --- | ---
**Tempo de inicialização** | O Azure suporta VMs com um tipo de inicialização de BIOS, não UEFI. | Condicionamento pronto se o tipo de inicialização for UEFI
**Núcleos** | Cada máquina não deve ter mais de 128 núcleos, que é o número máximo que um Azure VM suporta.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados para comparação. Se as configurações de avaliação especificarem um fator de conforto, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, o Azure Migrate usa os núcleos alocados sem aplicar o fator de conforto. | Pronto se o número de núcleos estiver dentro do limite
**Ram** | Cada máquina não deve ter mais de 3.892 GB de RAM, que&nbsp;é o tamanho máximo que um Azure Série M Standard_M128m<sup>2</sup> VM suporta. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, o Azure Migrate considerará a RAM utilizada para comparação. Se um fator de conforto for especificado, a RAM utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a RAM alocada é usada sem a aplicação de um fator de conforto.<br/><br/> | Pronto se a quantidade de RAM estiver dentro do limite
**Disco de armazenamento** | O tamanho alocado de um disco não deve ser superior a 32 TB. Embora o Azure suporte discos de 64 TB com discos Azure Ultra SSD, o Azure Migrate: Server Assessment atualmente verifica para 32 TB como o limite de tamanho de disco porque ainda não suporta Ultra SSD. <br/><br/> O número de discos conectados à máquina, incluindo o disco do SISTEMA OPERACIONAL, deve ser de 65 ou menos. | Pronto se o tamanho e o número do disco estiverem dentro dos limites
**Rede** | Uma máquina não deve ter mais de 32 interfaces de rede (NICs) conectadas a ela. | Pronto se o número de NICs estiver dentro do limite

### <a name="guest-operating-system"></a>Sistema operacional convidado

Juntamente com a revisão das propriedades da VM, o Server Assessment analisa o sistema operacional convidado de uma máquina para determinar se ela pode ser executada no Azure.

> [!NOTE]
> Para lidar com a análise de convidados para VMs VMware, o Server Assessment usa o sistema operacional especificado para a VM no vCenter Server. Para VMs Linux em execução no VMware, o Server Assessment atualmente não identifica a versão do kernel do sistema operacional convidado.

A Avaliação do Servidor usa a seguinte lógica para identificar a prontidão do Azure com base no sistema operacional:

**Sistema operacional** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2012 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte total.| Pronto para o Azure.
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2003 e Windows Server 2003 R2 | Esses sistemas operacionais passaram suas datas de fim de suporte e precisam de um [Contrato de Suporte Personalizado (CSA)](https://aka.ms/WSosstatement) para suporte no Azure. | Condicionalmente pronto para Azure. Considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 e MS-DOS | Esses sistemas operacionais passaram suas datas de fim de suporte. A máquina pode começar no Azure, mas o Azure não oferece suporte ao SO. | Condicionalmente pronto para Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Windows 7, Windows 8 e Windows 10 | O Azure fornece suporte apenas com uma [assinatura do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente pronto para Azure.
Windows 10 Pro | O Azure oferece suporte com [Direitos de Hospedagem multilocatário.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente pronto para Azure.
Windows Vista e Windows XP Professional | Esses sistemas operacionais passaram suas datas de fim de suporte. A máquina pode começar no Azure, mas o Azure não oferece suporte ao SO. | Condicionalmente pronto para Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Linux | Veja os [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md) que o Azure endossa. Outros sistemas operacionais Linux podem começar no Azure. Mas recomendamos que você atualize o Sistema Operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão é aprovada.<br/><br/>Condicionalmente pronto se a versão não for endossada.
Outros sistemas operacionais como Oracle Solaris, Apple macOS e FreeBSD | O Azure não endossa esses sistemas operacionais. A máquina pode começar no Azure, mas o Azure não oferece suporte ao SO. | Condicionalmente pronto para Azure. Recomendamos que você instale um sistema operacional suportado antes de migrar para o Azure.  
Sistema operacional especificado como **Outros** no vCenter Server | O Azure Migrate não pode identificar o sistema operacional neste caso. | Preparação desconhecida. Certifique-se de que o Azure suporta o sistema operacional em execução dentro da VM.
Sistemas operacionais de 32 bits | A máquina pode começar no Azure, mas o Azure pode não fornecer suporte total. | Condicionalmente pronto para Azure. Considere atualizar para um Sistema Operacional de 64 bits antes de migrar para o Azure.

## <a name="calculating-sizing"></a>Calculando o dimensionamento

Depois que a máquina é marcada como pronta para o Azure, a Avaliação do Servidor faz recomendações de dimensionamento. Essas recomendações identificam o Azure VM e o Disk SKU. Os cálculos de dimensionamento dependem se você está usando o dimensionamento no local ou o dimensionamento baseado em desempenho.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (como está no local)

 Se você usar o dimensionamento no local, a Avaliação do Servidor não considerará o histórico de desempenho das VMs e discos.

- **Dimensionamento de cálculo**: A Avaliação do Servidor aloca um Azure VM SKU com base no tamanho alocado no local.
- **Armazenamento e dimensionamento de disco**: A avaliação do servidor analisa o tipo de armazenamento especificado nas propriedades de avaliação e recomenda o tipo de disco apropriado. Os possíveis tipos de armazenamento são HDD Padrão, SSD padrão e Premium. O tipo de armazenamento padrão é Premium.
- **Dimensionamento da rede**: A avaliação do servidor considera o adaptador de rede na máquina no local.

### <a name="calculate-sizing-performance-based"></a>Calcular dimensionamento (baseado em desempenho)

Se você usar o dimensionamento baseado em desempenho, a Avaliação do Servidor faz as recomendações de dimensionamento da seguinte forma:

- A Avaliação do Servidor considera o histórico de desempenho da máquina para identificar o tamanho da VM e o tipo de disco no Azure.
- Se você importar servidores usando um arquivo CSV, os valores especificados serão usados. Este método é especialmente útil se você tiver superalocado a máquina no local, a utilização é baixa e você deseja dimensionar corretamente a VM do Azure para economizar custos.
- Se você não quiser usar os dados de desempenho, reconfigure os critérios de dimensionamento para as-está no local, conforme descrito na seção anterior.

#### <a name="calculate-storage-sizing"></a>Calcular o dimensionamento do armazenamento

Para dimensionamento de armazenamento, o Azure Migrate tenta mapear cada disco conectado à máquina a um disco Do Zure. O dimensionamento funciona da seguinte forma:

1. A Avaliação do Servidor adiciona o IOPS de leitura e gravação de um disco para obter o IOPS total necessário. Da mesma forma, adiciona os valores de leitura e gravação de throughput para obter o throughput total de cada disco.
1. Se você especificou o tipo de armazenamento como automático, o tipo selecionado será baseado nos valores de IOPS e throughput eficazes. A Avaliação do Servidor determina se é mapeado o disco para um HDD padrão, SSD padrão ou disco Premium no Azure. Se o tipo de armazenamento estiver definido como um desses tipos de disco, a Avaliação do Servidor tentará encontrar um SKU de disco dentro do tipo de armazenamento selecionado.
1. Os discos são selecionados da seguinte forma:
    - Se a Avaliação do Servidor não conseguir encontrar um disco com o IOPS e o throughput necessários, ele marcará a máquina como inadequada para o Azure.
    - Se a Avaliação do Servidor encontrar um conjunto de discos adequados, ele selecionará os discos que suportam o local especificado nas configurações de avaliação.
    - Se houver vários discos elegíveis, a Avaliação do Servidor selecionará o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, o tamanho do disco de configuração será usado para encontrar um disco SSD padrão no Azure.

#### <a name="calculate-network-sizing"></a>Calcular o dimensionamento da rede

A Avaliação do Servidor tenta encontrar uma VM Azure que suporte o número e o desempenho necessário dos adaptadores de rede conectados à máquina no local.

- Para obter o desempenho efetivo da rede de VM no local, o Server Assessment agrega a taxa de transmissão de dados da máquina (saída de rede) em todos os adaptadores de rede. Em seguida, aplica o fator conforto. Ele usa o valor resultante para encontrar uma VM Azure que possa suportar o desempenho de rede necessário.
- Juntamente com o desempenho da rede, o Server Assessment também considera se o Azure VM pode suportar o número necessário de adaptadores de rede.
- Se os dados de desempenho da rede não estiverem disponíveis, a Avaliação do Servidor considerará apenas a contagem de adaptadores de rede para o dimensionamento de VM.

#### <a name="calculate-compute-sizing"></a>Calcular dimensionamento de cálculo

Depois de calcular os requisitos de armazenamento e rede, o Server Assessment considera os requisitos de CPU e RAM para encontrar um tamanho de VM adequado no Azure.

- O Azure Migrate analisa os núcleos e a RAM utilizados eficazes para encontrar um tamanho adequado de VM Azure.
- Se nenhum tamanho adequado for encontrado, a máquina será marcada como inadequada para o Azure.
- Se um tamanho adequado for encontrado, as Migrações para Azure aplicarão os cálculos de armazenamento e rede. Em seguida, aplica as configurações de localização e nível de preço para a recomendação final de tamanho de VM.
- Se houver vários discos qualificados, será recomendado aquele com o menor custo.

## <a name="confidence-ratings-performance-based"></a>Classificações de confiança (baseadas em desempenho)

Cada avaliação baseada em desempenho no Azure Migrate está associada a uma avaliação de confiança. A classificação varia de uma (mais baixa) a cinco (mais altas) estrelas. O índice de confiança ajuda a estimar a confiabilidade das recomendações de tamanho que o Azure Migrate fornece.

- O índice de confiança é atribuído a uma avaliação. A classificação é baseada na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado em desempenho, a avaliação do servidor precisa:
    - Os dados de utilização da CPU e da MEMÓRIA RAM VM.
    - O disco IOPS e os dados de throughput para cada disco conectado à VM.
    - A I/O da rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede conectado a uma VM.

Se algum desses números de utilização não estiver disponível, as recomendações de tamanho podem não ser confiáveis.

> [!NOTE]
> As classificações de confiança não são atribuídas para servidores avaliados usando um arquivo CSV importado. As classificações também não são aplicáveis para avaliação no local.

### <a name="ratings"></a>Classificações

Esta tabela mostra os índices de confiança de avaliação, que dependem da porcentagem de pontos de dados disponíveis:

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
- Algumas VMs foram desligadas durante o período para o qual a avaliação foi calculada. Se quaisquer VMs forem desligados por algum tempo, a Avaliação do Servidor não poderá coletar os dados de desempenho desse período.
- Algumas VMs foram criadas durante o período para o qual a avaliação foi calculada. Por exemplo, suponha que você criou uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas apenas uma semana atrás. O histórico de desempenho das novas VMs não existirá durante toda a duração.

> [!NOTE]
> Se a avaliação for inferior a cinco estrelas, recomendamos que você espere pelo menos um dia para que o aparelho faça o perfil do ambiente e, em seguida, recalcule a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável. Nesse caso, recomendamos que você mude a avaliação para o dimensionamento no local.

## <a name="calculate-monthly-costs"></a>Calcular custos mensais

Depois que as recomendações de dimensionamento são concluídas, o Azure Migrate calcula os custos de computação e armazenamento para após a migração.

- **Custo do cálculo**: O Azure Migrate usa o tamanho recomendado do Azure VM e a API de Faturamento Do Azure para calcular o custo mensal para a VM.

    O cálculo leva em conta o:
    - Sistema operacional
    - Garantia de software
    - Instâncias reservadas
    - Tempo de atividade da VM
    - Location
    - Configurações da moeda

    A Avaliação do Servidor agrega o custo em todas as máquinas para calcular o custo total mensal do cálculo.

- **Custo de armazenamento**: O custo mensal de armazenamento de uma máquina é calculado agregando o custo mensal de todos os discos que estão conectados à máquina.

    A Avaliação do Servidor calcula os custos totais mensais de armazenamento agregando os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não considera ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação.

## <a name="next-steps"></a>Próximas etapas

[Revise](best-practices-assessment.md) as melhores práticas para criar avaliações. 

- Saiba mais sobre a execução de avaliações para [VMs VMware,](tutorial-prepare-vmware.md) [Hyper-VMs](tutorial-prepare-hyper-v.md)e [servidores físicos.](tutorial-prepare-physical.md)
- Saiba como avaliar servidores [importados com um arquivo CSV](tutorial-assess-import.md).
- Saiba mais sobre a criação de [visualização de dependência](concepts-dependency-visualization.md).
