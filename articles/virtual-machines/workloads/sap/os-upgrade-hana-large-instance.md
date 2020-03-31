---
title: Atualização de sistema operacional do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Executar a atualização de sistema operacional para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675625"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do so é responsabilidade do cliente, o suporte às operações da Microsoft pode guiá-lo para as áreas-chave para ficar atento durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

Durante o provisionamento da unidade HLI, a equipe de operações da Microsoft instala o sistema operacional.
Ao longo do tempo, você deverá manter o sistema operacional (exemplo: patches, ajustes, atualizações etc.) na unidade HLI.

Antes de fazer grandes alterações no sistema operacional (por exemplo, atualizar o SP1 para o SP2), você precisa entrar em contato com a equipe de Operações da Microsoft abrindo um ticket de suporte para consultar.

Inclua em seu bilhete:

* A ID da assinatura da HLI.
* O nome do servidor.
* O nível de patch que você pretende aplicar.
* A data em que você está planejando essa alteração. 

Recomendamos que você abra esse tíquete pelo menos uma semana antes da data de conclusão da atualização desejável, para que a equipe de operações verifique se uma atualização de firmware é necessária na folha do seu servidor.


Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. Você precisa reinstalar o SFS compatível após a atualização do sistema operacional.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você precisa reinstalar a versão compatível dos drivers após a atualização.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuração recomendada sap HANA Large Instance (Tipo I)

A configuração do sistema operacional pode se afastar das configurações recomendadas ao longo do tempo devido a patches, upgrades de sistema e alterações feitas pelos clientes. Além disso, a Microsoft identifica as atualizações necessárias para os sistemas existentes para garantir que eles estejam configurados de forma ideal para o melhor desempenho e resiliência. Seguindo as instruções, delineie recomendações que abordam o desempenho da rede, a estabilidade do sistema e o desempenho ideal do HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versões compatíveis do driver eNIC/fNIC
  Para ter o desempenho adequado da rede e a estabilidade do sistema, é aconselhável garantir que a versão apropriada específica do sistema operacional de drivers eNIC e fNIC seja instalada conforme descrito na tabela de compatibilidade a seguir. Os servidores são entregues aos clientes com versões compatíveis. Observe que, em alguns casos, durante o patches do OS/Kernel, os drivers podem ser revertidos para as versões padrão do driver. Certifique-se de que a versão apropriada do driver está executando operações de correção pós-OS/Kernel.
       
      
  |  Fornecedor de OS    |  Versão do pacote do SISTEMA OPERACIONAL     |  Versão do Firmware  |  Driver eNIC |  Driver fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandos para atualização do driver e para limpar pacotes de rpm antigos
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Comandos para confirmar
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Falha na atualização do SuSE HLIs GRUB
O SAP no Azure HANA Large Instances (Tipo I) pode estar em um estado não inicializador após a atualização. O procedimento abaixo corrige este problema.
#### <a name="execution-steps"></a>Etapas de execução


*   Executar `multipath -ll` comando.
*   Obtenha o ID LUN cujo tamanho seja aproximadamente 50G ou use o comando:`fdisk -l | grep mapper`
*   Atualizar `/etc/default/grub_installdevice` arquivo `/dev/mapper/<LUN ID>`com linha . Exemplo: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>O ID LUN varia de servidor para servidor.


### <a name="disable-edac"></a>Desativar o EDAC 
   O módulo EDAC (Error Detection And Correction) ajuda na detecção e correção de erros de memória. No entanto, o hardware subjacente para SAP HANA no Azure Large Instances (Tipo I) já está executando a mesma função. Ter o mesmo recurso ativado nos níveis de hardware e sistema operacional (OS) pode causar conflitos e pode levar a desligamentos ocasionais e não planejados do servidor. Portanto, recomenda-se desativar o módulo do sistema operacional.

#### <a name="execution-steps"></a>Etapas de execução

* Verifique se o módulo EDAC está habilitado. Se uma saída for retornada no comando abaixo, isso significa que o módulo está ativado. 
```
lsmod | grep -i edac 
```
* Desative os módulos anexando as seguintes linhas ao arquivo`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Uma reinicialização é necessária para fazer mudanças no local. Execute `lsmod` o comando e verifique se o módulo não está presente lá na saída.


### <a name="kernel-parameters"></a>Parâmetros de kernel
   Certifique-se de `transparent_hugepage`que `numa_balancing` `processor.max_cstate`a `ignore_ce` `intel_idle.max_cstate` configuração correta para , e são aplicadas.

* intel_idle.max_cstate=1
* processador.max_cstate=1
* transparent_hugepage=nunca
* numa_balancing=desativar
* mce=ignore_ce


#### <a name="execution-steps"></a>Etapas de execução

* Adicione esses parâmetros à `GRB_CMDLINE_LINUX` linha no arquivo`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Crie um novo arquivo grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Sistema de reinicialização.


## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Consulte [o Backup do SISTEMA OPERACIONAL para SKUs tipo II de selos de Revisão 3](os-backup-type-ii-skus.md) para classe SKU tipo II.
