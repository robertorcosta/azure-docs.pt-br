---
title: Cenários suportados para SAP HANA no Azure (Instâncias Grandes)| Microsoft Docs
description: Cenários suportados e seus detalhes de arquitetura para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617174"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários suportados para instâncias grandes do HANA
Este artigo descreve os cenários e detalhes de arquitetura suportados para HANA Large Instances (HLI).

>[!NOTE]
>Se o cenário necessário não for mencionado neste artigo, entre em contato com a equipe de Gerenciamento de Serviços da Microsoft para avaliar seus requisitos.
Antes de configurar a unidade HLI, valide o design com o SAP ou seu parceiro de implementação de serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e definições que são usados neste artigo:

- **SID**: Um identificador de sistema para o sistema HANA
- **HLI**: Hana Grandes Instâncias
- **DR**: Recuperação de desastres
- **DR normal**: Uma configuração do sistema com um recurso dedicado apenas para fins de DR
- **DR multiuso**: Um sistema de local DR configurado para usar um ambiente de não-produção ao lado de uma instância de produção configurada para um evento DR 
- **SID único**: Um sistema com uma instância instalada
- **Multi-SID**: Um sistema com várias instâncias configuradas; também chamado de ambiente MCOS
- **HSR**: Replicação do sistema SAP HANA

## <a name="overview"></a>Visão geral
Hana Large Instances suporta uma variedade de arquiteturas para ajudá-lo a cumprir seus requisitos de negócios. As seções a seguir abrangem os cenários arquitetônicos e seus detalhes de configuração. 

O design de arquitetura derivado é puramente de uma perspectiva de infra-estrutura, e você deve consultar o SAP ou seus parceiros de implementação para a implantação do HANA. Se seus cenários não estiverem listados neste artigo, entre em contato com a equipe de conta da Microsoft para revisar a arquitetura e obter uma solução para você.

> [!NOTE]
> Essas arquiteturas são totalmente compatíveis com o design TDI (Tailored Data Integration, integração de dados medida) e suportadas pela SAP.

Este artigo descreve os detalhes dos dois componentes em cada arquitetura suportada:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor provisionado vem pré-configurado com conjuntos de interfaces Ethernet. As interfaces Ethernet configuradas em cada unidade HLI são categorizadas em quatro tipos:

- **A**: Usado para ou por acesso ao cliente.
- **B**: Usado para comunicação nó-nó. Esta interface é configurada em todos os servidores (independentemente da topologia solicitada), mas usada apenas para cenários de escala.
- **C**: Usado para conectividade nó-a-armazenamento.
- **D**: Usado para conexão do dispositivo nó-para-iSCSI para a configuração STONITH. Esta interface só é configurada quando uma configuração HSR é solicitada.  

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó-para-nó|
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Nó-para-nó|
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | STONITH |

Você escolhe a interface com base na topologia configurada na unidade HLI. Por exemplo, a interface "B" está configurada para comunicação nó-a-nó, o que é útil quando você tem uma topologia de escala configurada. Esta interface não é usada para configurações de nó único e escala. Para obter mais informações sobre o uso da interface, revise os cenários necessários (mais tarde neste artigo). 

Se necessário, você pode definir cartões NIC adicionais por conta própria. No entanto, as configurações dos NICs existentes *não podem* ser alteradas.

>[!NOTE]
>Você pode encontrar interfaces adicionais que são interfaces físicas ou vínculos. Você deve considerar apenas as interfaces mencionadas anteriormente para o seu caso de uso. Qualquer outro pode ser ignorado.

A distribuição para unidades com dois endereços IP atribuídos deve parecer:

- O Ethernet "A" deve ter um endereço IP atribuído que está dentro da faixa de endereço do pool ip do servidor que você enviou à Microsoft. Este endereço IP deve ser mantido no diretório */etc/hosts* do sistema operacional.

- O Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação com o NFS. Este endereço *não* precisa ser mantido no diretório *etc/hosts* para permitir tráfego instância a ocorrência dentro do inquilino.

Para a implantação de replicação do sistema HANA ou hana, uma configuração de lâmina com dois endereços IP atribuídos não é adequada. Se você tiver apenas dois endereços IP atribuídos e quiser implantar essa configuração, entre em contato com o SAP HANA no Azure Service Management. Eles podem atribuir-lhe um terceiro endereço IP em uma terceira VLAN. Para unidades HANA Large Instances com três endereços IP atribuídos em três portas NIC, as seguintes regras de uso se aplicam:

- O Ethernet "A" deve ter um endereço IP atribuído que está fora da faixa de endereço do pool ip do servidor que você submeteu à Microsoft. Este endereço IP não deve ser mantido no diretório *etc/hosts* do sistema operacional.

- O Ethernet "B" deve ser mantido exclusivamente no diretório *etc/hosts* para comunicação entre as várias instâncias. Estes são os endereços IP a serem mantidos em configurações HANA de escala como os endereços IP que o HANA usa para a configuração entre nós.

- O Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação ao armazenamento NFS. Este tipo de endereço não deve ser mantido no diretório *etc/hosts.*

- O Ethernet "D" deve ser usado exclusivamente para acesso a dispositivos STONITH para Marcapasso. Essa interface é necessária quando você configura a replicação do sistema HANA e deseja obter o failover automático do sistema operacional usando um dispositivo baseado em SBD.


### <a name="storage"></a>Armazenamento
O armazenamento é pré-configurado com base na topologia solicitada. Os tamanhos de volume e pontos de montagem variam dependendo do número de servidores, do número de SKUs e da topologia configurada. Para obter mais informações, revise seus cenários necessários (mais tarde neste artigo). Se você precisar de mais armazenamento, você pode comprá-lo em incrementos de 1 TB.

>[!NOTE]
>O ponto de montagem /usr/sap/\<SID> é um link simbólico para o ponto de montagem /hana/compartilhado.


## <a name="supported-scenarios"></a>Cenários com suporte

Os diagramas de arquitetura nas próximas seções usam as seguintes notações:

[![Tabela de diagramas de arquitetura](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Aqui estão os cenários suportados:

* Único nó com um SID
* Único nó MCOS
* Nó único com DR (normal)
* Nó único com DR (multiuso)
* HSR com STONITH
* HSR com DR (normal/multiuso) 
* Failover Automático do Host (1 + 1) 
* Expansão com espera
* Expansão sem espera
* Expansão com DR

## <a name="single-node-with-one-sid"></a>Único nó com um SID

Esta topologia suporta um nó em uma configuração de escala com um SID.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Único nó com um SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação hana | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log | 
|/hana/logbackups/SID | Redo logs |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.

## <a name="single-node-mcos"></a>Único nó MCOS

Esta topologia suporta um nó em uma configuração de escala com vários SIDs.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Único nó MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID1 | Instalação hana para SID1 | 
|/hana/data/SID1/mnt00001 | Instalação de arquivos de dados para SID1 | 
|/hana/log/SID1/mnt00001 | Instalação de arquivos de log para SID1 | 
|/hana/logbackups/SID1 | Redo logs for SID1 |
|/hana/shared/SID2 | Instalação hana para SID2 | 
|/hana/data/SID2/mnt00001 | Instalação de arquivos de dados para SID2 | 
|/hana/log/SID2/mnt00001 | Instalação de arquivos de log para SID2 | 
|/hana/logbackups/SID2 | Redo logs for SID2 |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

## <a name="single-node-with-dr-using-storage-replication"></a>Nó único com DR usando replicação de armazenamento
 
Esta topologia suporta um nó em uma configuração de escala com um ou vários SIDs, com replicação baseada em armazenamento no site DR para um SID principal. No diagrama, apenas um sistema single-SID é retratado no local principal, mas os sistemas MCOS também são suportados.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Nó único com DR usando replicação de armazenamento](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação hana para SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID | 
|/hana/logbackups/SID | Redo logs for SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- No local do DR: Os volumes e pontos de montagem são configurados (marcados como "Necessários para a instalação do HANA") para a instalação da ocorrência de HANA de produção na unidade DR HLI. 
- No site do DR: Os dados, backups de log e volumes compartilhados (marcados como "Replicação de armazenamento") são replicados via instantâneo do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento failover de recuperação de desastres](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
- O volume de inicialização para *classe SKU Tipo I* é replicado no nó DR.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Nó único com DR (multiuso) usando replicação de armazenamento
 
Esta topologia suporta um nó em uma configuração de escala com um ou vários SIDs, com replicação baseada em armazenamento no site DR para um SID principal. No diagrama, apenas um sistema single-SID é representado no local principal, mas os sistemas multi-SID (MCOS) também são suportados. No local dr, a unidade HLI é usada para a instância de QA enquanto as operações de produção estão sendo executados a partir do local principal. Durante o failover dr (ou teste de failover), a instância de QA no site do DR é retirada.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Nó único com DR (multiuso) usando replicação de armazenamento](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/shared/QA-SID | Instalação hana para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- No local do DR: Os volumes e pontos de montagem são configurados (marcados como "Necessários para a instalação do HANA") para a instalação da ocorrência de HANA de produção na unidade DR HLI. 
- No site do DR: Os dados, backups de log e volumes compartilhados (marcados como "Replicação de armazenamento") são replicados via instantâneo do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento failover de recuperação de desastres](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- No site do DR: Os dados, backups de log, log e volumes compartilhados para QA (marcado como "instalação de instância de QA") são configurados para a instalação de instância de QA.
- O volume de inicialização para *classe SKU Tipo I* é replicado no nó DR.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR com STONITH para alta disponibilidade
 
Esta topologia suporta dois nós para a configuração de replicação do sistema HANA. Esta configuração é suportada apenas para instâncias HANA únicas em um nó. Isso significa que os cenários do MCOS *não* são suportados.

> [!NOTE]
> A partir de dezembro de 2019, essa arquitetura é suportada apenas para o sistema operacional SUSE.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![HSR com STONITH para alta disponibilidade](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Used for STONITH |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Used for STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|** No nó secundário **|
|/hana/shared/SID | Instalação hana para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID secundário | 
|/hana/logbackups/SID | Redo logs para o secundário SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: Um SBD está configurado para a configuração do STONITH. No entanto, o uso do STONITH é opcional.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Alta disponibilidade com HSR e DR com replicação de armazenamento
 
Esta topologia suporta dois nós para a configuração de replicação do sistema HANA. Suporte a DRs normais e multiuso. Essas configurações são suportadas apenas para instâncias HANA únicas em um nó. Isso significa que os cenários mcos *não* são suportados com essas configurações.

No diagrama, um cenário multiuso é retratado no local do DR, onde a unidade HLI é usada para a instância de QA enquanto as operações de produção estão sendo executados a partir do local principal. Durante o failover dr (ou teste de failover), a instância de QA no site do DR é retirada. 

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Alta disponibilidade com HSR e DR com replicação de armazenamento](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Used for STONITH |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Used for STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário no site primário**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó secundário no site principal**|
|/hana/shared/SID | Instalação hana para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID secundário | 
|/hana/logbackups/SID | Redo logs para o secundário SID |
|**No site de DR**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/shared/QA-SID | Instalação hana para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: Um SBD está configurado para a configuração do STONITH. No entanto, o uso do STONITH é opcional.
- No site dr: *Dois conjuntos de volumes de armazenamento são necessários* para a replicação do nó primário e secundário.
- No local do DR: Os volumes e pontos de montagem são configurados (marcados como "Necessários para a instalação do HANA") para a instalação da ocorrência de HANA de produção na unidade DR HLI. 
- No site do DR: Os dados, backups de log e volumes compartilhados (marcados como "Replicação de armazenamento") são replicados via instantâneo do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento failover de recuperação de desastres](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- No site do DR: Os dados, backups de log, log e volumes compartilhados para QA (marcado como "instalação de instância de QA") são configurados para a instalação de instância de QA.
- O volume de inicialização para *classe SKU Tipo I* é replicado no nó DR.


## <a name="host-auto-failover-11"></a>Failover Automático do Host (1 + 1)
 
Esta topologia suporta dois nós em uma configuração de failover automático do host. Há um nó com um papel de mestre/trabalhador e outro como um standby. *O SAP suporta este cenário apenas para S/4 HANA.* Para obter mais informações, consulte [a nota OSS 2408419 - SAP S/4HANA - Suporte a vários nós](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Failover Automático do Host (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-a-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre e de espera**|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |



### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Em espera: Os volumes e pontos de montagem são configurados (marcados como "Obrigatórios para a instalação do HANA") para a instalação da instância HANA na unidade de espera.
 

## <a name="scale-out-with-standby"></a>Expansão com espera
 
Essa topologia permite vários nós em uma configuração de expansão. Há um nó com um papel principal, um ou mais nós com um papel de trabalhador, e um ou mais nós como standby. No entanto, só pode haver um nó mestre em qualquer ponto no tempo.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Expansão com espera](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-a-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre, de trabalho e de espera**|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


## <a name="scale-out-without-standby"></a>Expansão sem espera
 
Essa topologia permite vários nós em uma configuração de expansão. Há um nó com um papel principal, e um ou mais nós com um papel de trabalhador. No entanto, só pode haver um nó mestre em qualquer ponto no tempo.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Expansão sem espera](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-a-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre e trabalhador**|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Scale-out com DR usando replicação de armazenamento
 
Esta topologia permite vários nós em uma expansão com DR. Suporte a DRs normais e multiuso. No diagrama, apenas o DR de finalidade única é representado. Você pode solicitar essa topologia com ou sem o nó de espera.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Scale-out com DR usando replicação de armazenamento](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-a-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó DR **|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
-  No local do DR: Os volumes e pontos de montagem são configurados (marcados como "Necessários para a instalação do HANA") para a instalação da ocorrência de HANA de produção na unidade DR HLI. 
- No site do DR: Os dados, backups de log e volumes compartilhados (marcados como "Replicação de armazenamento") são replicados via instantâneo do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento failover de recuperação de desastres](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- O volume de inicialização para *classe SKU Tipo I* é replicado no nó DR.


## <a name="single-node-with-dr-using-hsr"></a>Nó único com DR usando HSR
 
Esta topologia suporta um nó em uma configuração de escala com um SID, com replicação do sistema HANA para o site DR para um SID principal. No diagrama, apenas um sistema single-SID é representado no local principal, mas os sistemas multi-SID (MCOS) também são suportados.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Nó único com DR usando HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados em ambas as unidades HLI (Principal e DR):

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação hana para SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID | 
|/hana/logbackups/SID | Redo logs for SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- O nó primário sincroniza-se com o nó DR usando a replicação do sistema HANA. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usada para conectar os circuitos ExpressRoute para fazer uma rede privada entre suas redes regionais.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR de nó único para DR (custo otimizado) 
 
 Esta topologia suporta um nó em uma configuração de escala com um SID, com replicação do sistema HANA para o site DR para um SID principal. No diagrama, apenas um sistema single-SID é representado no local principal, mas os sistemas multi-SID (MCOS) também são suportados. No local dr, uma unidade HLI é usada para a instância de QA enquanto as operações de produção estão sendo executados a partir do local principal. Durante o failover dr (ou teste de failover), a instância de QA no site do DR é retirada.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![HSR de nó único para DR (custo otimizado)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|/hana/shared/QA-SID | Instalação hana para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória são suportados em um ambiente multiSID, consulte [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- No site do DR: Os volumes e pontos de montagem são configurados (marcados como "ProD Instance at DR site") para a instalação da instância hana de produção na unidade DR HLI. 
- No site do DR: Os dados, backups de log, log e volumes compartilhados para QA (marcado como "instalação de instância de QA") são configurados para a instalação de instância de QA.
- O nó primário sincroniza-se com o nó DR usando a replicação do sistema HANA. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usada para conectar os circuitos ExpressRoute para fazer uma rede privada entre suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Alta disponibilidade e recuperação de desastres com HSR 
 
 Esta topologia suporta dois nós para a configuração de replicação do sistema HANA para a alta disponibilidade das regiões locais. Para o DR, o terceiro nó na região DR sincroniza-se com o local primário usando HSR (modo de sincronização). 

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Alta disponibilidade e recuperação de desastres com HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- No local dr: Os volumes e pontos de montagem são configurados (marcados como "exemplo de DR PROD") para a instalação da ocorrência de HANA de produção na unidade DR HLI. 
- O nó principal do site sincroniza-se com o nó DR usando a replicação do sistema HANA. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usada para conectar os circuitos ExpressRoute para fazer uma rede privada entre suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Alta disponibilidade e recuperação de desastres com HSR (custo otimizado)
 
 Esta topologia suporta dois nós para a configuração de replicação do sistema HANA para a alta disponibilidade das regiões locais. Para o DR, o terceiro nó na região DR sincroniza com o local principal usando HSR (modo de sincronização), enquanto outra instância (por exemplo, QA) já está se esgotando do nó DR. 

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Alta disponibilidade e recuperação de desastres com HSR (custo otimizado)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|/hana/shared/QA-SID | Instalação hana para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- No local dr: Os volumes e pontos de montagem são configurados (marcados como "exemplo de DR PROD") para a instalação da ocorrência de HANA de produção na unidade DR HLI. 
- No site do DR: Os dados, backups de log, log e volumes compartilhados para QA (marcado como "instalação de instância de QA") são configurados para a instalação de instância de QA.
- O nó principal do site sincroniza-se com o nó DR usando a replicação do sistema HANA. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usada para conectar os circuitos ExpressRoute para fazer uma rede privada entre suas redes regionais.

## <a name="scale-out-with-dr-using-hsr"></a>Scale-out com DR usando HSR
 
Esta topologia permite vários nós em uma expansão com DR. Você pode solicitar essa topologia com ou sem o nó de espera. O nó principal do site sincroniza-se com o nó do site DR usando a replicação do sistema HANA (modo assync).


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

[![Scale-out com DR usando HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-a-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem estão pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó DR **|
|/hana/shared | Instalação hana para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- No local dr: Os volumes e pontos de montagem são configurados para a instalação de exemplo hana de produção na unidade DR HLI. 
- O nó principal do site sincroniza-se com o nó DR usando a replicação do sistema HANA. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usada para conectar os circuitos ExpressRoute para fazer uma rede privada entre suas redes regionais.


## <a name="next-steps"></a>Próximas etapas
- [Infra-estrutura e conectividade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) para hana grandes instâncias
- [Alta disponibilidade e recuperação de desastres](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para HANA Large Instances
