---
title: Como usar o PerfInsights Linux no Microsoft Azure | Microsoft Docs
description: Aprende a usar o PerfInsights para solucionar problemas de desempenho de VM do Linux.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394767"
---
# <a name="how-to-use-perfinsights"></a>Como usar o PerfInsights

O [PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) é uma ferramenta de diagnóstico de autoajuda que coleta e analisa os dados de diagnóstico e fornece um relatório para ajudar a solucionar problemas de desempenho de máquina virtual do Linux no Azure. O PerfInsights pode ser executado em máquinas virtuais com suporte como uma ferramenta autônoma ou diretamente do portal usando o [diagnóstico de desempenho para máquinas virtuais do Azure](performance-diagnostics.md).

Caso esteja tendo problemas de desempenho com máquinas virtuais, antes de contatar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de solução de problemas com suporte

O PerfInsights pode coletar e analisar vários tipos de informações. As seções a seguir cobrem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida de desempenho

Esse cenário coleta informações básicas, como a configuração de armazenamento e de hardware de sua máquina virtual, vários logs, incluindo:

- Informações do sistema operacional

- Informações do dispositivo PCI

- Logs gerais de SO convidado

- Arquivos de configuração

- Informações de armazenamento

- Configuração de máquina virtual do Azure (coletada usando o [serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service))

- Lista de processos em execução, disco, memória e uso da CPU

- Informações de rede

Esta é uma coleção de passiva de informações que não devem afetar o sistema.

>[!Note]
>O cenário de análise de desempenho rápido é incluído automaticamente em cada um dos seguintes cenários:

### <a name="performance-analysis"></a>Análise de desempenho

Esse cenário é semelhante à análise rápida de desempenho, mas permite capturar informações de diagnóstico por duração maior.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é coletada pelo PerfInsights

Informações sobre a máquina virtual Linux, sistema operacional, dispositivos de bloco, consumidores de recursos altos, configuração e vários logs são coletados. Aqui estão mais detalhes:

- Sistema operacional
  - Distribuição e versão do Linux
  - Informações do kernel
  - Informações do driver

- Hardware
  - Dispositivos PCI [`*`]

- Processos e memória
  - Lista de processos (nome da tarefa, memória usada, arquivos abertos)
  - Memória física total, disponível e livre
  - Memória de permuta total, disponível e livre
  - Criação de perfil da captura de CPU e processa o uso da CPU em um intervalo de 5 segundos
  - Criação de perfil de captura de processos de uso de memória em intervalo de 5 segundos

- Rede  
  - Lista de adaptadores de rede com estatísticas de adaptadores
  - Tabela de roteamento de rede
  - Portas e status abertos

- Armazenamento
  - Bloquear lista de dispositivos
  - Lista de partições
  - Lista de pontos de montagem
  - Informações de volume do MDADM
  - Informações de volume LVM
  - Criação de perfil de captura em todos os discos em intervalo de 5 segundos

- Logs
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/Cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[pasta de extensão]/log de\*\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Saída de journalctl dos últimos cinco dias

- [Metadados da instância de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] As informações de PCI ainda não foram coletadas em distribuições Debian e SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Executar o PerfInsights Linux em sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que preciso saber antes de executar a ferramenta

#### <a name="tool-requirements"></a>Requisitos da ferramenta

- Esta ferramenta deve ser executada na VM que tem o problema de desempenho.
- O Python 2,7 deve ser instalado na VM

- Atualmente, há suporte para as seguintes distribuições:

    | Distribuição               | Versão                                         |
    |----------------------------|-------------------------------------------------|
    | Servidor de Oracle Linux        | 6,10 [`*`], 7,3, 7,6, 7,5 (imagem do Marketplace do Oracle-Database-EE 13,8)|
    | CentOS                     | 6,5 [`*`], 7,6                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Veja a seção de [problemas conhecidos](#known-issues)

### <a name="known-issues"></a>Problemas conhecidos

- O RHEL 8 não tem o Python instalado por padrão. Para executar o PerfInsights Linux, você deve primeiro instalar o Python 2,7

- A coleta de informações do agente convidado pode falhar no CentOS 6. x

- As informações de dispositivos PCI não são coletadas em distribuições baseadas em Debian

- As informações de LVM são parcialmente coletadas em algumas distribuições

### <a name="how-do-i-run-perfinsights"></a>Como fazer executar PerfInsights

Você pode executar o PerfInsights em uma máquina virtual instalando o diagnóstico de desempenho do Azure de portal do Azure. Você também pode executá-la como uma ferramenta independente.

>[!Note]
>O PerfInsights simplesmente coleta e analisa os dados. Ele não faz nenhuma modificação no sistema.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instalar e executar o PerfInsights no portal do Azure

Para obter mais informações sobre essa opção, consulte [diagnóstico de desempenho do Azure](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Executar PerfInsights no modo autônomo

Para executar a ferramenta PerfInsights, siga estas etapas:

1. Baixe [PerfInsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) em uma pasta em sua máquina virtual e extraia o conteúdo usando os comandos abaixo do terminal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navegue até a pasta que contém `perfinsights.py` arquivo e execute `perfinsights.py` para exibir os parâmetros de linha de comando disponíveis.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Captura de tela da saída de linha de comando do Linux PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    A sintaxe básica para execução de cenários do PerfInsights é:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Você pode usar o exemplo abaixo para executar um cenário de análise de desempenho rápido por 1 minuto e criar os resultados na pasta/tmp/output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Você pode usar o exemplo abaixo para executar o cenário de análise de desempenho de 5 minutos e carregar a bola de resultado tar para a conta de armazenamento:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Antes de executar um cenário, o PerfInsights solicita que o usuário concorde em compartilhar informações de diagnóstico e aceite os termos de licença. Use a opção **-a ou--Accept-disenção-e-share-Diagnostics** para ignorar esses prompts.
    >
    >Se você tiver um tíquete de suporte ativo com a Microsoft e executando PerfInsights de acordo com a solicitação do engenheiro de suporte com o qual está trabalhando, certifique-se de fornecer o número do tíquete de suporte usando a opção **-s ou--support-Request** .

Quando a execução for concluída, um novo arquivo tar aparecerá na mesma pasta que PerfInsights, a menos que nenhuma pasta de saída seja especificada. O nome do arquivo é **PerformanceDiagnostics\_aaaa-mm-dd\_FFF. tar. gz.** Você pode enviar esse arquivo para o agente de suporte para análise ou abrir o relatório dentro do arquivo para examinar as conclusões e as recomendações.

## <a name="review-the-diagnostics-report"></a>Examine o relatório de diagnóstico

Dentro do **PerformanceDiagnostics\_aaaa-mm-dd\_arquivo FFF. tar. gz** , você pode encontrar um relatório HTML que detalha as conclusões de PerfInsights. Para examinar o relatório, expanda o **PerformanceDiagnostics\_aaaa-mm-dd\_arquivo FFF. tar. gz** e, em seguida, abra o arquivo **PerfInsights Report. html** .

### <a name="overview-tab"></a>Guia Visão geral

A guia **visão geral** fornece detalhes de execução básica e informações de máquina virtual. A guia **conclusões** exibe um resumo das recomendações de todas as seções diferentes do relatório PerfInsights.

![Captura de tela do relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Captura de tela do relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Constatações categorizadas como altas são problemas conhecidos que podem causar problemas de desempenho. Constatações categorizadas como médias representam configurações não adequadas que não necessariamente causam problemas de desempenho. Constatações categorizadas como lentas são declarações apenas informativas.

Analise as recomendações e links para todas as conclusões altas e médias. Saiba mais sobre como eles podem afetar o desempenho e também sobre as melhores práticas para configurações de otimização de desempenho.

### <a name="cpu-tab"></a>Guia CPU

A guia **CPU** fornece informações sobre o consumo de CPU em todo o sistema durante a execução do PerfInsights. Informações sobre períodos altos de uso da CPU e maiores consumidores de CPU de longa duração serão úteis para solucionar problemas altos relacionados à CPU.

![Captura de tela da guia de CPU do relatório PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Guia Armazenamento

A seção **Conclusões** exibe várias conclusões e recomendações relacionadas ao armazenamento.

Os **dispositivos de bloco** e outras seções relacionadas, **como partições**, **LVM**e **MDADM** guias descrevem como os dispositivos de bloco são configurados e relacionados entre si.

![Captura de tela da guia armazenamento](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Captura de tela da guia MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Guia do Linux

A guia **Linux** contém informações sobre o hardware e o sistema operacional em execução na sua VM. Os detalhes incluem uma lista de processos e informações em execução sobre o agente convidado, o PCI, a CPU, os drivers e os drivers LIS.

![Captura de tela da guia Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Próximas etapas

Você pode carregar relatórios e logs de diagnóstico para o Suporte da Microsoft para análise. Quando você trabalha com a equipe de Suporte da Microsoft, elas podem solicitar que você transmita a saída gerada pelo PerfInsights para auxiliar no processo de solução de problemas.

A captura de tela a seguir mostra uma mensagem semelhante à qual você poderá receber:

![Captura de tela da mensagem de exemplo do Suporte da Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Siga as instruções na mensagem para acessar o workspace de transferência de arquivo. Para mais segurança, você deve alterar sua senha no primeiro uso.

Depois de entrar, você encontrará uma caixa de diálogo para carregar o **PerformanceDiagnostics\_aaaa-mm-dd\_arquivo FFF. tar. gz** que foi coletado pelo PerfInsights.
