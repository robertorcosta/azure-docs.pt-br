---
title: Como usar o PerfInsights Linux no Microsoft Azure| Microsoft Docs
description: Aprende como usar o PerfInsights para solucionar problemas de desempenho do Linux VM.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266982"
---
# <a name="how-to-use-perfinsights"></a>Como usar o PerfInsights

[O PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) é uma ferramenta de diagnóstico de autoajuda que coleta e analisa os dados de diagnóstico e fornece um relatório para ajudar a solucionar problemas de desempenho da máquina virtual Linux no Azure. O PerfInsights pode ser executado em máquinas virtuais suportadas como uma ferramenta autônoma ou diretamente do portal usando [diagnósticos de desempenho para máquinas virtuais Azure](performance-diagnostics.md).

Caso esteja tendo problemas de desempenho com máquinas virtuais, antes de contatar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de solução de problemas com suporte

O PerfInsights pode coletar e analisar vários tipos de informações. As seções a seguir cobrem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida de desempenho

Este cenário coleta informações básicas, como armazenamento e configuração de hardware da sua máquina virtual, vários logs, incluindo:

- Informações do sistema operacional

- Informações do dispositivo PCI

- Registros gerais do Sistema Operacional de Hóspedes

- Arquivos de configuração

- Informações de armazenamento

- Configuração da máquina virtual do Azure (coletada usando [o Serviço de Metadados de Instância do Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- Lista de processos em execução, uso de disco, memória e CPU

- Informações de rede

Esta é uma coleção de passiva de informações que não devem afetar o sistema.

>[!Note]
>O cenário de análise rápida de desempenho é automaticamente incluído em cada um dos seguintes cenários:

### <a name="performance-analysis"></a>Análise de desempenho

Este cenário é semelhante à análise de desempenho rápido, mas permite capturar informações de diagnóstico por mais tempo.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é coletada pelo PerfInsights

Informações sobre a máquina virtual Linux, sistema operacional, dispositivos de bloqueio, consumidores de alto recurso, configuração e vários logs são coletados. Estes são mais detalhes:

- Sistema operacional
  - Distribuição e versão linux
  - Informações do kernel
  - Informações do motorista

- Hardware
  - Dispositivos PCI`*`[ ]

- Processos e memória
  - Lista de processos (nome da tarefa, memória usada, arquivos abertos)
  - Total, disponível e memória física gratuita
  - Memória total, disponível e troca livre
  - Captura de criação de perfil da CPU e processa o uso da CPU em intervalo de 5 segundos
  - Captura de criação de perfil do uso da memória de processos em intervalo de 5 segundos

- Rede  
  - Lista de adaptadores de rede com estatísticas de adaptadores
  - Tabela de roteamento de rede
  - Portas abertas e status

- Armazenamento
  - Lista de dispositivos de bloco
  - Lista de partições
  - Lista de pontos de montagem
  - Informações sobre o volume mdadm
  - Informações sobre o volume de LVM
  - Captura de criação de perfil em todos os discos em intervalo de 5 segundos

- Logs
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[pasta de\*extensão]/ log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Saída de journalctl para os últimos cinco dias

- [Metadados de instância da máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] As informações do PCI ainda não foram coletadas nas distribuições Debian e SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Execute o PerfInsights Linux em sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que eu tenho que saber antes de executar a ferramenta

#### <a name="tool-requirements"></a>Requisitos da ferramenta

- Esta ferramenta deve ser executada na VM que tem o problema de desempenho.
- Python 2.7 deve ser instalado na VM

- As seguintes distribuições são suportadas atualmente:

    | Distribuição               | Versão                                         |
    |----------------------------|-------------------------------------------------|
    | Servidor Oracle Linux        | 6.10`*`[], 7.3, 7.6, 7.5 (imagem de mercado Oracle-Database-Ee 13.8)|
    | CentOS                     | 6.5`*`[], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Consulte a seção [Problemas conhecidos](#known-issues)

### <a name="known-issues"></a>Problemas conhecidos

- O RHEL 8 não tem python instalado por padrão. Para executar o PerfInsights Linux, primeiro você deve instalar o Python 2.7

- A coleta de informações do Agente Convidado pode falhar no CentOS 6.x

- As informações dos dispositivos PCI não são coletadas em distribuições baseadas no Debian

- As informações do LVM são parcialmente coletadas em algumas distribuições

### <a name="how-do-i-run-perfinsights"></a>Como executar o PerfInsights

Você pode executar o PerfInsights em uma máquina virtual instalando o Azure Performance Diagnostics a partir do portal Azure. Você também pode executá-la como uma ferramenta independente.

>[!Note]
>O PerfInsights simplesmente coleta e analisa os dados. Não faz modificações no sistema.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instalar e executar o PerfInsights do Portal do Azure

Para obter mais informações sobre essa opção, consulte [Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Executar o PerfInsights no modo autônomo

Para executar a ferramenta PerfInsights, siga estas etapas:

1. Baixe [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) para uma pasta em sua máquina virtual e extraia o conteúdo usando os comandos abaixo do terminal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navegue até a `perfinsights.py` pasta que `perfinsights.py` contém arquivo e, em seguida, execute para exibir os parâmetros de linha de comando disponíveis.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Captura de tela da saída de linha de comando do PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    A sintaxe básica para execução de cenários do PerfInsights é:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Você pode usar o exemplo abaixo para executar o cenário de análise de desempenho rápido por 1 minuto e criar os resultados em /tmp/pasta de saída:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Você pode usar o exemplo abaixo para executar o cenário de análise de desempenho por 5 min e carregar a bola de piche de resultado para a conta de armazenamento:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Antes de executar um cenário, o PerfInsights solicita que o usuário concorde em compartilhar informações de diagnóstico e aceite os termos de licença. Use **-a ou --accept-isenção de responsabilidade e share-diagnostics** para pular essas solicitações.
    >
    >Se você tiver um bilhete de suporte ativo com a Microsoft e executar o PerfInsights de acordo com a solicitação do engenheiro de suporte com o qual você está trabalhando, certifique-se de fornecer o número do bilhete de suporte usando a opção **-s ou --support-request.**

Quando a execução é concluída, um novo arquivo de piche será exibido na mesma pasta que o PerfInsights, a menos que nenhuma pasta de saída seja especificada. O nome do arquivo é **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz.** Você pode enviar este arquivo para o agente de suporte para análise ou abrir o relatório dentro do arquivo para revisar as conclusões e recomendações.

## <a name="review-the-diagnostics-report"></a>Examine o relatório de diagnóstico

Dentro do arquivo **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz,** você pode encontrar um relatório HTML que detalha as descobertas do PerfInsights. Para revisar o relatório, expanda o arquivo **\_PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** e abra o arquivo **PerfInsights Report.html.**

### <a name="overview-tab"></a>Guia Visão Geral

A **guia Visão geral** fornece detalhes básicos de execução e informações da máquina virtual. A guia **Conclusões** exibe um resumo das recomendações de todas as diferentes seções do relatório PerfInsights.

![Captura de tela do Relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Captura de tela do Relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Constatações categorizadas como altas são problemas conhecidos que podem causar problemas de desempenho. Constatações categorizadas como médias representam configurações não adequadas que não necessariamente causam problemas de desempenho. Constatações categorizadas como lentas são declarações apenas informativas.

Analise as recomendações e links para todas as conclusões altas e médias. Saiba mais sobre como eles podem afetar o desempenho e também sobre as melhores práticas para configurações de otimização de desempenho.

### <a name="cpu-tab"></a>Guia da CPU

**A** guia CPU fornece informações sobre o consumo de CPU em todo o sistema durante a execução do PerfInsights. Informações sobre altos períodos de uso da CPU e os principais consumidores de CPU de longa duração serão úteis para solucionar problemas relacionados à CPU.

![Captura de tela da guia CPU do PerfInsights Report](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Guia Armazenamento

A seção **Conclusões** exibe várias conclusões e recomendações relacionadas ao armazenamento.

Os **dispositivos de bloco** e outras seções relacionadas, como **Partições,** **LVM**e **mDADM** descrevem como os dispositivos de bloco são configurados e relacionados entre si.

![Captura de tela da guia de armazenamento](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Captura de tela da guia MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Guia Linux

A guia **Linux** contém informações sobre o hardware e o sistema operacional em execução em sua VM. Os detalhes incluem uma lista de processos em execução e informações sobre drivers Guest Agent, PCI, CPU, Drivers e LIS.

![Captura de tela da guia Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Próximas etapas

Você pode carregar relatórios e logs de diagnóstico para o Suporte da Microsoft para análise. Quando você trabalha com a equipe de suporte do Microsoft, eles podem solicitar que você transmita a saída gerada pelo PerfInsights para ajudar no processo de solução de problemas.

A captura de tela a seguir mostra uma mensagem semelhante à qual você poderá receber:

![Captura de tela da mensagem de exemplo do Suporte da Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Siga as instruções na mensagem para acessar o workspace de transferência de arquivo. Para mais segurança, você deve alterar sua senha no primeiro uso.

Depois de fazer login, você encontrará uma caixa de diálogo para carregar o arquivo **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** que foi coletado pelo PerfInsights.
