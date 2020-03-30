---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935870"
---
**Última atualização do documento**: 12 novembro 2019 10:00 PST.

A divulgação de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecida como ataques de canal paralelo de execução especulativa resultou em várias perguntas dos clientes que queriam mais esclarecimentos sobre o assunto.  

A Microsoft implantou atenuações em todos os nossos serviços de nuvem. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente entre elas está protegida. Isso significa que um invasor potencial que usa a mesma infraestrutura não pode atacar seu aplicativo usando essas vulnerabilidades.

O Azure usa a [manutenção da preservação da memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) sempre que possível, para minimizar o impacto para o cliente e eliminar a necessidade de reinicializações. O Azure continuará utilizando esses métodos ao fazer atualizações em todo o sistema do host e proteger nossos clientes.

Mais informações sobre como a segurança é integrada em todos os aspectos do Azure estão disponíveis no site [Documentação de segurança do Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Desde a primeira publicação deste documento, foram divulgadas várias variantes dessa classe de vulnerabilidade. A Microsoft continua investindo intensamente na proteção de nossos clientes e no fornecimento de diretrizes. Esta página será atualizada conforme continuamos liberando correções adicionais. 
> 
> Em 12 de novembro de 2019, [a Intel publicou](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) um aviso técnico sobre a vulnerabilidade Intel® Transactiongen Synchronization Extensions (Intel® TSX) Transaction Assynchronous Abort (TAA) que é atribuída [cve-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Essa vulnerabilidade afeta os processadores Intel® Core® e Intel® Xeon®.  O Microsoft Azure lançou atualizações do sistema operacional e está implantando um novo microcódigo, pois é disponibilizado pela Intel, em toda a nossa frota para proteger nossos clientes contra essas novas vulnerabilidades.   O Azure está trabalhando em estreita colaboração com a Intel para testar e validar o novo microcódigo antes de seu lançamento oficial na plataforma. 
>
> **Os clientes que estão executando código não confiável em sua VM** precisam tomar medidas para proteger contra essas vulnerabilidades, lendo abaixo para obter orientação adicional sobre todas as vulnerabilidades de canais laterais de execução especulativa (Microsoft Advisories ADV [180002,](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Outros clientes devem avaliar essas vulnerabilidades a partir de uma perspectiva de Defesa em Profundidade e considerar as implicações de segurança e desempenho de sua configuração escolhida.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Mantendo seus sistemas operacionais atualizados

Embora uma atualização do sistema operacional não seja necessária para isolar os aplicativos em execução no Azure de outros clientes do Azure, é sempre uma melhor prática manter o software atualizado. Os últimos Pacotes Cumulativos de Atualizações de Segurança para o Windows contêm mitigações de várias vulnerabilidades de canal paralelo de execução especulativa. Da mesma forma, as distribuições Linux liberaram várias atualizações para resolver essas vulnerabilidades. Aqui estão nossas ações recomendadas para atualizar seu sistema operacional:

| Oferta | Ação recomendada  |
|----------|---------------------|
| Serviços de nuvem do Azure  | Habilite [a atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou certifique-se de que está executando o mais novo Sistema Operacional convidado. |
| Máquinas Virtuais do Linux do Azure | Instale atualizações do provedor do sistema operacional. Para obter mais informações, confira [Linux](#linux) mais adiante neste documento. |
| Máquinas Virtuais do Windows do Azure  | Instale o pacote cumulativo de atualizações de segurança mais recente.
| Outros Serviços de PaaS do Azure | Não é necessária nenhuma ação para clientes que usam esses serviços. O Azure mantém automaticamente suas versões de Sistema Operacional atualizadas. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Orientações adicionais se você estiver executando código não confiável 

Os clientes que permitem que usuários não confiáveis executem um código arbitrário podem desejar implementar alguns recursos de segurança adicionais nas Máquinas Virtuais do Azure ou nos Serviços de Nuvem. Esses recursos protegem contra vetores de divulgação dentro do processo descritos por várias vulnerabilidades de execução especulativa.

Cenários de exemplo em que os recursos de segurança adicionais são recomendados:

- Você permite que um código não confiável seja executado em sua VM.  
    - *Por exemplo, você permite que um de seus clientes carregue um binário ou um script que você, em seguida, executa no aplicativo*. 
- Você permite que os usuários não confiáveis façam logon em sua VM usando contas com baixos privilégios.   
    - *Por exemplo, você permite que um usuário com poucos privilégios faça logon em uma de suas VMs usando a área de trabalho remota ou o SSH*.  
- Você permite que usuários não confiáveis acessem máquinas virtuais implementadas por meio da virtualização aninhada.  
    - *Por exemplo, você controla o host Hyper-V, mas aloca as VMs a usuários não confiáveis*. 

Os clientes que não implementam um cenário que envolva um código não confiável não precisam habilitar esses recursos de segurança adicionais. 

## <a name="enabling-additional-security"></a>Habilitando a segurança adicional 

Você pode habilitar recursos adicionais de segurança dentro de sua VM ou Cloud Service se estiver executando código não confiável. Paralelamente, certifique-se de que seu sistema operacional esteja atualizado para habilitar recursos de segurança dentro de sua VM ou Cloud Service

### <a name="windows"></a>Windows 

O sistema operacional de destino precisa estar atualizado para habilitar esses recursos de segurança adicionais. Embora várias mitigações de ataques de canal paralelo de execução especulativa estejam habilitadas por padrão, os recursos adicionais descritos aqui precisam ser habilitados manualmente e podem causar um impacto no desempenho. 


**Passo 1: Desativar o hiper-threading na VM** - Os clientes que executam código não confiável em uma VM hiper-threaded precisarão desativar o hiper-threading ou mover-se para um tamanho de VM não hiper-threaded. Faça referência [a este doc](https://docs.microsoft.com/azure/virtual-machines/windows/acu) para uma lista de tamanhos de VM hiper-threaded (onde a razão de vCPU para Core é de 2:1). Para verificar se sua VM tem hiper-threading ativado, consulte o script abaixo usando a linha de comando do Windows de dentro da VM.

Digite `wmic` para entrar na interface interativa. Em seguida, digite o abaixo para ver a quantidade de processadores físicos e lógicos na VM.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se o número de processadores lógicos for maior do que os processadores físicos (núcleos), então o hiper-threading será ativado.  Se você estiver executando uma VM hiper-threaded, entre [em contato com o Suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para desativar o hiper-threading.  Uma vez que o hiper-threading esteja desativado, **o suporte exigirá uma reinicialização completa da VM**. Consulte a [contagem de núcleos](#core-count) para entender por que sua contagem de núcleos VM diminuiu.


**Passo 2**: Paralelamente à Etapa 1, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar se as proteções estão habilitadas usando o módulo [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell.

> [!NOTE]
> Se você já baixou este módulo, precisará instalar a versão mais recente.
>


A saída do script PowerShell deve ter os valores abaixo para validar proteções habilitadas contra essas vulnerabilidades:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Se a `MDS mitigation is enabled: False`saída aparecer, entre [em contato com o Suporte azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter opções de mitigação disponíveis.



**Passo 3**: Para habilitar o suporte ao Sistema Operacional De So (KVAS) e ao Sistema Operacional Branch Target Injection `Session Manager` (BTI) do Kernel, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar proteções usando as chaves do registro. Uma reinicialização é necessária.


**Passo 4**: Para implantações que estejam usando [apenas a virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (somente D3 e E3): Estas instruções se aplicam dentro do VM que você está usando como um host Hyper-V.

1.  Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) `MinVmVersionForCpuBasedMitigations` para habilitar proteções usando as chaves do registro.
2.  Defina o tipo de agendador do hipervisor seguindo `Core` as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>A habilitação do conjunto de recursos de segurança adicionais internamente exige que o sistema operacional de destino esteja totalmente atualizado. Algumas mitigações serão habilitadas por padrão. A seção a seguir descreve os recursos que estão desativados por padrão e/ou que são dependentes de suporte de hardware (microcódigo). A habilitação desses recursos pode causar um impacto no desempenho. Referencie a documentação do provedor do sistema operacional para obter mais instruções


**Passo 1: Desativar o hiper-threading na VM** - Os clientes que executam código não confiável em uma VM hiper-threaded precisarão desativar o hiper-threading ou passar para uma VM não hiper-threaded.  Faça referência [a este doc](https://docs.microsoft.com/azure/virtual-machines/linux/acu) para uma lista de tamanhos de VM hiper-threaded (onde a razão de vCPU para Core é de 2:1). Para verificar se você está executando uma VM `lscpu` hiper-threaded, execute o comando na VM Linux. 

Se `Thread(s) per core = 2`, então a hiper-rosca foi ativada. 

Se `Thread(s) per core = 1`, então a hiper-threading foi desativada. 

 
Saída de amostra para uma VM com hiper-threading ativado: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Se você estiver executando uma VM hiper-threaded, entre [em contato com o Suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para desativar o hiper-threading.  Uma vez que o hiper-threading esteja desativado, **o suporte exigirá uma reinicialização completa da VM**. Consulte a [contagem de núcleos](#core-count) para entender por que sua contagem de núcleos VM diminuiu.



**Passo 2**: Para mitigar qualquer uma das vulnerabilidades de canal lateral de execução especulativa abaixo, consulte a documentação do provedor do sistema operacional:   
 
- [Red Hat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [Suse](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Contagem de núcleos

Quando uma VM hiper-threaded é criada, o Azure aloca 2 threads por núcleo - estes são chamados de vCPUs. Quando o hiper-threading é desativado, o Azure remove um segmento e superfícies até núcleos rosqueados únicos (núcleos físicos). A proporção de vCPU para CPU é de 2:1, então uma vez que o hiper-threading é desativado, a contagem de CPU na VM parece ter diminuído pela metade. Por exemplo, uma VM D8_v3 é uma VM hiper-threaded em execução em 8 vCPUs (2 threads por núcleo x 4 núcleos).  Quando o hiper-threading estiver desativado, as CPUs cairão para 4 núcleos físicos com 1 thread por núcleo. 

## <a name="next-steps"></a>Próximas etapas

Este artigo fornece orientação para os ataques de canal side-channel de execução especulativa abaixo que afetam muitos processadores modernos:

[Fusão de espectro:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)
- CVE-2017-5715 - Injeção de Alvo de Ramificação (BTI)  
- CVE-2017-5754 - Isolamento da tabela da página do kernel (KPTI)
- CVE-2018-3639 - Desvio especulativo da loja (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Informações sobre o kernel do Windows – variante da Variante Spectre 1
 
[Falha do terminal L1 (L1TF):](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)
- CVE-2018-3615 - Extensões do Intel Software Guard (Intel SGX)
- CVE-2018-3620 - Sistemas Operacionais (OS) e Modo de Gestão do Sistema (SMM)
- CVE-2018-3646 – impacta o Virtual Machine Manager (VMM)

[Amostragem de dados microarquitetônicos:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013) 
- CVE-2019-11091 - Memória incache (MDSUM) microarquitetônica de amostragem de dados
- CVE-2018-12126 - Amostragem de dados de buffer de microarquitetura (MSBDS)
- CVE-2018-12127 - Amostragem de dados da porta de carga microarquitetônica (MLPDS)
- CVE-2018-12130 - Amostragem de dados de buffer de preenchimento microarquitetônico (MFBDS)

Extensões de sincronização transacional (Intel® TSX) Abortar Assíncrono de Transação:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX Transaction Assynchronous Abort (TAA)








