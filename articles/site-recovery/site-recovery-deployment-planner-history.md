---
title: Histórico de versão do planejador de implantação de recuperação do site do Azure
description: Diferentes correções do Planejador de Implantação de Recuperação de Site e limitações conhecidas, juntamente com suas datas de lançamento.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433418"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Histórico de versão do planejador de implantação de recuperação do site do Azure

Este artigo fornece o histórico de todas as versões do Azure Site Recovery Deployment Planner, juntamente com as correções, limitações conhecidas em cada uma e suas datas de lançamento.

## <a name="version-251"></a>Versão 2.51

**Data de lançamento: 22 de agosto de 2019**

**Correções:**

- Corrigimos o problema de recomendação de custos com a versão 2.5 do Deployment Planner

## <a name="version-25"></a>Versão 2.5

**Data de lançamento: 29 de julho de 2019**

**Correções:**

- Para máquinas virtuais e máquinas físicas VMware, a recomendação é atualizada para ser baseada na replicação de Discos Gerenciados.
- Suporte adicionado para Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 ou posterior

## <a name="version-24"></a>Versão 2.4

**Data de lançamento: 17 de abril de 2019**

**Correções:**

- Melhor compatibilidade do sistema operacional, especificamente ao lidar com erros baseados em localização.
- Adicionado VMs com até 20 Mbps de taxa de alteração de dados (churn) à lista de verificação de compatibilidade.
- Mensagens de erro melhoradas
- Suporte adicionado ao vCenter 6.7.
- Adicionado suporte para a estação de trabalho Windows Server 2019 e Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versão 2.3

**Data de lançamento: 3 de dezembro de 2018**

**Correções:**

- Corrigimos um problema que impedia o Planejador de Implantação de gerar um relatório com o local de destino e a assinatura fornecidos.

## <a name="version-22"></a>Versão 2.2 

**Data de lançamento: 25 de abril de 2018**

**Correções:**

- Operações GetVMList:
  - Corrigimos um problema que fez com que o GetVMList falhasse se a pasta especificada não existisse. Agora cria o diretório padrão ou cria o diretório especificado no parâmetro outputfile.
  - Adicionado razões de falha mais detalhadas para GetVMList.
- Adicionado informações do tipo VM como uma coluna na folha de VMs compatível do relatório Planejador de implantação.
- Recuperação de desastres do Hyper-V para Azure:
  - Excluiu as VMs com VHDs compartilhados e discos PassThrough da criação de perfil. A operação Startprofiling mostra a lista de VMs excluídos no console.
  - Adicionado VMs com mais de 64 discos à lista de VMs incompatíveis.
  - Atualizei o fator de compressão de replicação inicial (IR) e de replicação delta (DR).
  - Adicionado suporte limitado para armazenamento SMB.

## <a name="version-21"></a>Versão 2.1

**Data de lançamento: 3 de janeiro de 2018**

**Correções:**

- Atualizei o relatório do Excel.
- Corrigimos bugs na operação GetThroughput.
- Opção adicionada para limitar o número de VMs ao perfil ou gerar o relatório. O limite padrão é de 1.000 VMs.
- Recuperação de desastres do VMware para Azure:
  - Corrigimos um problema do Windows Server 2016 VM indo para a tabela incompatível. 
  - Mensagens de compatibilidade atualizadas para VMs do Windows Windows (Extensible Firmware Interface, interface de firmware extensível).
- Atualizei o VMware para Azure e Hyper-V para Azure, limite de churn de dados VM por VM. 
- Confiabilidade melhorada do analisador de arquivos da lista VM.

## <a name="version-201"></a>Versão 2.0.1

**Data de lançamento: 7 de dezembro de 2017**

**Correções:**

- Recomendação adicionada para otimizar a largura de banda da rede.

## <a name="version-20"></a>Versão 2,0

**Data de lançamento: 28 de novembro de 2017**

**Correções:**

- Adicionado suporte para hyper-V para recuperação de desastres do Azure.
- Calculadora de custos adicionada.
- Verificação da versão do Sistema Operacional para VMware para recuperação de desastres do Azure para determinar se a VM é compatível ou incompatível com a proteção. A ferramenta usa a seqüência de versões do Sistema Operacional que é devolvida pelo servidor vCenter para essa VM. É a versão do sistema operacional convidado que o usuário selecionou ao criar a VM no VMware.

**Limitações conhecidas:**

- Para recuperação de desastres de Hyper-V para Azure, `,`a `"` `[`VM com nome contendo os caracteres como: , , , `]`e ``` ` ``` não são suportados. Se perfilado, a geração do relatório falhará ou terá um resultado incorreto.
- Para recuperação de desastres do VMware para Azure, a VM com nome contendo comuma não é suportada. Se perfilado, a geração do relatório falha ou terá um resultado incorreto.

## <a name="version-131"></a>Versão 1.3.1

**Data de lançamento: 19 de julho de 2017** 

**Correções:**

- Adicionado suporte para discos grandes (> 1 TB) na geração de relatórios. Agora você pode usar o Deployment Planner para planejar a replicação para máquinas virtuais que tenham tamanhos de disco maiores que 1 TB (até 4095 GB).
Leia mais sobre [Suporte a discos grandes no Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versão 1.3

**Data de lançamento: 9 de maio de 2017**

**Correções:**

- Adicionado suporte para disco gerenciado na geração de relatórios. O número de VMs que podem ser colocados em uma única conta de armazenamento é calculado com base em se o disco gerenciado for selecionado para Failover/Failover de teste.

## <a name="version-12"></a>Versão 1.2

**Data de lançamento: 7 de abril de 2017**

**Correções:**

- O tipo de inicialização adicionado (BIOS ou EFI) verifica cada VM para determinar se a VM é compatível ou incompatível para a proteção.
- Adicionado informações do tipo SO para cada máquina virtual nas VMs compatíveis e em planilhas de VMs incompatíveis.
- Adicionado suporte à operação GetThroughput para o governo dos EUA e para as regiões do Microsoft Azure da China.
- Algumas outras verificações de pré-requisitos foram adicionadas para vCenter e Servidor ESXi.
- Corrigimos um problema de relatório incorreto sendo gerado quando as configurações locais são definidas como não-inglês.

## <a name="version-11"></a>Versão 1.1

**Data de lançamento: 9 de março de 2017**

**Correções:**

- Corrigimos um problema que impedia o perfil de VMs quando há duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts vCenter ESXi.
- Corrigimos um problema que fez com que a cópia e a pesquisa fossem desativadas para as VMs compatíveis e planilhas de VMs incompatíveis.

## <a name="version-10"></a>Versão 1.0

**Data de lançamento: 23 de fevereiro de 2017**

**Limitações conhecidas:**

- Suporta apenas para cenários de recuperação de desastres VMware para Azure. Para cenários de recuperação de desastres Hyper-V a Azure, use a [ferramenta de planejador de capacidade Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Não suporta a operação GetThroughput para o governo dos EUA e para as regiões microsoft azure da China.
- A ferramenta não faz o perfil das VMs se o servidor vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts ESXi.
Nesta versão, a ferramenta ignora a criação de perfil para nomes de VM ou endereços IP duplicados em VMListFile. A solução alternativa é criar um perfil das VMs usando um host ESXi em vez do servidor vCenter. Certifique-se de executar uma instância para cada host ESXi.
