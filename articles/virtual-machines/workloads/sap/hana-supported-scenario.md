---
title: Cenários suportados SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Cenários suportados e seus detalhes de arquitetura para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ed63f5caa6b1f1c0072a92f6a60ad43c5431af0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538366"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários suportados para instâncias grandes do HANA
Este documento descreve os cenários com suporte, juntamente com os detalhes da arquitetura para as HANA Large Instances (HLI).

>[!NOTE]
>Se o cenário necessário não for mencionado aqui, entre em contato com a equipe do Microsoft Service Management para avaliar seus requisitos.
Antes de prosseguir com o provisionamento da unidade HLI, valide o projeto com a SAP ou com seu parceiro de implementação de serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e definições usados no documento.

- SID: System Identifier para o sistema HANA.
- HLI: Hana Large Instances.
- DR: um site de recuperação de desastres.
- DR Normal: Uma configuração do sistema com um recurso dedicado para fins de DR usado somente.
- DR multiuso: um sistema no site de DR configurado para usar o ambiente de não produção junto com a instância de produção configurada para usar no evento de DR. 
- SID único: um sistema com uma instância instalada.
- Multi SID: Um sistema com várias instâncias configuradas. Também chamado de ambiente MCOS.
- HSR: replicação de sistema do SAP HANA.

## <a name="overview"></a>Visão Geral
As instâncias grandes do HANA suportam a variedade de arquiteturas para atender aos seus requisitos de negócios. A lista a seguir abrange os cenários e seus detalhes de configuração. 

O design da arquitetura derivada é puramente da perspectiva da infraestrutura e você deve consultar a SAP ou seus parceiros de implementação para a implantação do HANA. Se os seus cenários não estiverem listados, entre em contato com a equipe de contas da Microsoft para revisar a arquitetura e obter uma solução para você.

**Essas arquiteturas são totalmente compatíveis com o design TDI (Tailored Data Integration) e suportadas pelo SAP.**

Este documento descreve os detalhes dos dois componentes em cada arquitetura suportada:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor provisionado vem pré-configurado com os conjuntos de interfaces Ethernet. Aqui estão os detalhes das interfaces Ethernet configuradas em cada unidade HLI.

- **A**: essa interface é usada para/pelo acesso do cliente.
- **B**: essa interface é usada para a comunicação de nó a nó. Essa interface é configurada em todos os servidores (independentemente da topologia solicitada), mas só é usada para os 
- cenários de expansão.
- **C**: essa interface é usada para o nó para a conectividade de armazenamento.
- **D**: esta interface é usada para a conexão do dispositivo Node to ISCSI para a configuração do STONITH. Essa interface é configurada somente quando a configuração do HSR é solicitada.  

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | STONITH |

Você usa as interfaces com base na topologia configurada na unidade HLI. Por exemplo, a interface “B” é configurada para comunicação nó a nó, o que é útil quando você tem uma topologia de scale-out configurada. No caso de configuração de escalonamento de nó único, essa interface não é usada. Revise seus cenários necessários (mais adiante neste documento) para obter mais informações sobre o uso da interface. 

Se necessário, você pode definir cartões NIC adicionais por conta própria. No entanto, a configuração nas NICs existentes NÃO pode ser alterada.

>[!NOTE]
>Você ainda pode encontrar interfaces adicionais, que são interfaces físicas ou ligações. Você deve considerar as interfaces acima mencionadas para o seu caso usado, o resto pode ser ignorado / ou não ser moderado.

A distribuição de unidades com dois endereços IP atribuídos deve ser parecida com esta:

- A Ethernet "A" deve ter um endereço IP atribuído fora do intervalo de endereços do pool de IP do servidor que você enviou à Microsoft. Esse endereço IP deve ser usado para ser mantido em /etc/hosts do sistema operacional.

- A Ethernet “C” deve ter um endereço IP atribuído que seja usado para a comunicação com o NFS. Portanto, esses endereços **NÃO** precisam ser mantidos em etc/hosts para permitir o tráfego entre instâncias no locatário.

Para casos de implantação de Replicação de Sistema do HANA ou de expansão do HANA, uma configuração de folha com dois endereços IP atribuídos não é adequada. Caso você tenha apenas dois endereços IP atribuídos e deseje implantar uma configuração como essa, contate o Gerenciamento de Serviços do SAP HANA no Azure para obter um terceiro endereço IP em uma terceira VLAN atribuída. Para as unidades de Instância Grande do HANA que têm três endereços IP atribuídos em três portas NIC, as seguintes regras de uso se aplicam:

- A Ethernet "A" deve ter um endereço IP atribuído fora do intervalo de endereços do pool de IP do servidor que você enviou à Microsoft. Portanto, esse endereço IP não deve ser usado para ser mantido em /etc/hosts do sistema operacional.

- A Ethernet “B” deve ser usada exclusivamente para ser mantida em etc/hosts para a comunicação entre as diferentes instâncias. Esses endereços também serão os endereços IP que precisam ser mantidos em configurações em expansão do HANA como os endereços IP usados pelo HANA para a configuração entre nós.

- A Ethernet “C” deve ter um endereço IP atribuído que seja usado para a comunicação com o armazenamento NFS. Portanto, esse tipo de endereço não deve ser mantido em etc/hosts.

- A Ethernet “D” deve ser usada exclusivamente para o acesso ao dispositivo STONITH para o marcapasso. Essa interface é necessária quando você configura o HSR (HANA System Replication) e deseja obter o failover automático no sistema operacional usando um dispositivo baseado em SBD.


### <a name="storage"></a>Armazenamento
O armazenamento é pré-configurado com base na topologia solicitada. Os tamanhos de volume e ponto de montagem variam com base no número de servidores, SKUs e topologia configurados. Revise seus cenários necessários (mais adiante neste documento) para obter mais informações. Se mais armazenamento for necessário, você poderá comprá-lo em um incremento de TB.

>[!NOTE]
>O/usr/SAP/de mountpoint\<> SID é um link simbólico para o/Hana/Shared mountpoint.


## <a name="supported-scenarios"></a>Cenários com suporte

Nos diagramas de arquitetura, as seguintes notações são usadas para os gráficos:

[![legendas. PNG](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

A lista a seguir mostra os cenários suportados:

1. Único nó com um SID
2. Único nó MCOS
3. Único nó com DR (normal)
4. Único nó com DR (multiuso)
5. HSR com STONITH
6. HSR com DR (Normal / Multipurpose) 
7. Failover Automático do Host (1 + 1) 
8. Expansão com espera
9. Expansão sem espera
10. Expansão com DR



## <a name="1-single-node-with-one-sid"></a>1. nó único com um SID

Esta topologia suporta um nó em uma configuração de scale up com um SID.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA | 
|/hana/data/SID/mnt00001 | Arquivos de dados instalar | 
|/hana/log/SID/mnt00001 | Arquivos de log instalar | 
|/hana/logbackups/SID | Redo logs |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. nó único MCOS

Esta topologia suporta um nó em uma configuração de expansão com vários SIDs.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID1 | HANA Install for SID1 | 
|/hana/data/SID1/mnt00001 | HANA Install for SID1 | 
|/hana/log/SID1/mnt00001 | Arquivos de log instalar para SID1 | 
|/hana/logbackups/SID1 | Redo logs for SID1 |
|/hana/shared/SID2 | HANA Install for SID2 | 
|/hana/data/SID2/mnt00001 | Data files install for SID2 | 
|/hana/log/SID2/mnt00001 | Arquivos de log instalar para o SID2 | 
|/hana/logbackups/SID2 | Redo logs for SID2 |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.

## <a name="3-single-node-with-dr-using-storage-replication"></a>3. nó único com DR usando replicação de armazenamento
 
Esta topologia suporta um nó em uma configuração de scale up com um ou vários SIDs com a replicação baseada em armazenamento para o site de DR para um SID primário. No diagrama, apenas o SID único é representado no site primário, mas o multisid (MCOS) também é suportado.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA para SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID | 
|/hana/log/SID/mnt00001 | Arquivos de dados são instalados para o SID | 
|/hana/logbackups/SID | Redo logs for SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.
- No DR: Os volumes e pontos de montagem são configurados (marcados como “Obrigatório para instalação do HANA”) para a instalação da Instância do HANA de produção na unidade DR HLI. 
- No DR: Os dados, os log-ups e os volumes compartilhados (marcados como “Replicação de Armazenamento”) são replicados via instantâneo no site de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o documento [Procedimento de failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes.
- O volume de inicialização para **classe SKU Tipo I** é replicado para o nó DR.


## <a name="4-single-node-with-dr-multipurpose-using-storage-replication"></a>4. nó único com DR (multipropósito) usando replicação de armazenamento
 
Esta topologia suporta um nó em uma configuração de scale up com um ou vários SIDs com a replicação baseada em armazenamento para o site de DR para um SID primário. No diagrama, apenas o SID único é representado no site primário, mas o multisid (MCOS) também é suportado. No local de DR, a unidade HLI é usada para a instância de controle de qualidade, enquanto as operações de produção estão sendo executadas a partir do site primário. No momento do failover de recuperação de falhas (ou teste de failover), a instância do controle de qualidade no site de recuperação de desastres é desativada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/shared/QA-SID | Instalação do HANA para o QA SID | 
|/hana/data/QA-SID/mnt00001 | Arquivos de dados são instalados para o QA SID | 
|/hana/log/QA-SID/mnt00001 | Arquivos de log são instalados para o QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.
- No DR: Os volumes e pontos de montagem são configurados (marcados como “Obrigatório para instalação do HANA”) para a instalação da Instância do HANA de produção na unidade DR HLI. 
- No DR: Os dados, os log-ups e os volumes compartilhados (marcados como “Replicação de Armazenamento”) são replicados via instantâneo no site de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o documento [Procedimento de failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes. 
- No DR: Os dados, logbackups, log, volumes compartilhados para QA (marcados como “instalação da Instância do QA”) são configurados para a instalação da instância do QA.
- O volume de inicialização para **classe SKU Tipo I** é replicado para o nó DR.

## <a name="5-hsr-with-stonith-for-high-availability"></a>5. HSR com STONITH para alta disponibilidade
 
Essa topologia suporta dois nós para a configuração do HANA System Replication (HSR). Apenas há suporte para essa configuração em instâncias únicas do HANA em um nó. Isso significa que não há suporte para cenários de MCOS.

**A partir de agora, esta arquitetura é suportada apenas pelo sistema operacional SUSE.**


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Used for STONITH |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Used for STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó secundário**|
|/hana/shared/SID | Instalação do HANA para o SID secundário | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID secundário | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID secundário | 
|/hana/logbackups/SID | Redo logs para o secundário SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.
- STONITH: Um SBD está configurado para a configuração do STONITH. No entanto, um uso de STONITH é opcional.


## <a name="6-high-availability-with-hsr-and-dr-with-storage-replication"></a>6. alta disponibilidade com HSR e DR com replicação de armazenamento
 
Essa topologia suporta dois nós para a configuração do HANA System Replication (HSR). Tanto o normal e multiuso DR é suportado. Apenas há suporte para essas configurações em instâncias únicas do HANA em um nó. Isso significa que não há suporte para cenários de MCOS com essas configurações.

No diagrama, o cenário de várias finalidades é representado onde, no site de DR, a unidade de HLI é usada para a instância de QA enquanto as operações de produção estão sendo executadas a partir do site primário. No momento do failover de recuperação de falhas (ou teste de failover), a instância do controle de qualidade no site de recuperação de desastres é desativada. 



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Used for STONITH |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Used for STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário no site primário**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó secundário no site principal**|
|/hana/shared/SID | Instalação do HANA para o SID secundário | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID secundário | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID secundário | 
|/hana/logbackups/SID | Redo logs para o secundário SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/shared/QA-SID | Instalação do HANA para o QA SID | 
|/hana/data/QA-SID/mnt00001 | Arquivos de dados são instalados para o QA SID | 
|/hana/log/QA-SID/mnt00001 | Arquivos de log são instalados para o QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.
- STONITH: Um SBD está configurado para a configuração do STONITH. No entanto, um uso de STONITH é opcional.
- No DR: **Dois conjuntos de volumes de armazenamento são necessários** para replicação de nó primário e secundário.
- No DR: Os volumes e pontos de montagem são configurados (marcados como “Obrigatório para instalação do HANA”) para a instalação da Instância do HANA de produção na unidade DR HLI. 
- No DR: Os dados, os log-ups e os volumes compartilhados (marcados como “Replicação de Armazenamento”) são replicados via instantâneo no site de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o documento [Procedimento de failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes. 
- No DR: Os dados, logbackups, log, volumes compartilhados para QA (marcados como “instalação da Instância do QA”) são configurados para a instalação da instância do QA.
- O volume de inicialização para **classe SKU Tipo I** é replicado para o nó DR.


## <a name="7-host-auto-failover-11"></a>7. failover automático do host (1 + 1)
 
Esta topologia suporta dois nós em uma configuração de failover automático do host. Existe um nó com função mestre/trabalhador e outro como reserva. **SAP suporta este cenário apenas para S/4 HANA.** Consulte a nota do OSS “[2408419 - SAP S / 4HANA - Suporte a vários nós](https://launchpad.support.sap.com/#/notes/2408419)” para mais detalhes.



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre e de espera**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |



### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Em espera: os volumes e os pontos de montagem são configurados (marcados como “Obrigatório para instalação do HANA”) para a instalação da Instância do HANA na unidade em espera.
 

## <a name="8-scale-out-with-standby"></a>8. escalar horizontalmente com em espera
 
Essa topologia permite vários nós em uma configuração de expansão. Existe um nó com função principal, um ou mais nós com função de trabalho e um ou mais nós como standby. Porém, pode haver apenas um nó mestre em um determinado ponto do tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre, de trabalho e de espera**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


## <a name="9-scale-out-without-standby"></a>9. escalar horizontalmente sem espera
 
Essa topologia permite vários nós em uma configuração de expansão. Há um nó com função de mestre e um ou nós de modo com função de trabalho. Porém, pode haver apenas um nó mestre em um determinado ponto do tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre e trabalhador**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.

## <a name="10-scale-out-with-dr-using-storage-replication"></a>10. Escale horizontalmente com recuperação de desastre usando replicação de armazenamento
 
Esta topologia permite vários nós em uma expansão com DR. Ambos normal e multiuso DR é suportado. No diagrama, apenas o DR de finalidade única é representado. Você pode solicitar essa topologia com ou sem o nó de espera.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó DR**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
-  No DR: Os volumes e pontos de montagem são configurados (marcados como “Obrigatório para instalação do HANA”) para a instalação da Instância do HANA de produção na unidade DR HLI. 
- No DR: Os dados, os log-ups e os volumes compartilhados (marcados como “Replicação de Armazenamento”) são replicados via instantâneo no site de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o documento [Procedimento de failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes. 
- O volume de inicialização para **classe SKU Tipo I** é replicado para o nó DR.


## <a name="11-single-node-with-dr-using-hsr"></a>11. nó único com DR usando HSR
 
Essa topologia dá suporte a um nó em uma configuração de expansão com um SID com a replicação de sistema do HANA para o site de recuperação de desastre para um SID primário. No diagrama, apenas o SID único é representado no site primário, mas o multisid (MCOS) também é suportado.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-Node-HSR-Dr-111. png](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI/HSR |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados em ambas as unidades de HLI (primário e DR):

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA para SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID | 
|/hana/log/SID/mnt00001 | Arquivos de dados são instalados para o SID | 
|/hana/logbackups/SID | Redo logs for SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.
- Nó primário obtenha sincronização com o nó de DR usando a replicação de sistema do HANA. 
- [Alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para vincular os circuitos do ExpressRoute em conjunto para tornar uma rede privada entre sua rede de regiões.



## <a name="12-single-node-hsr-to-dr-cost-optimized"></a>12. nó único HSR para DR (com otimização de custo) 
 
 Essa topologia dá suporte a um nó em uma configuração de expansão com um SID com a replicação de sistema do HANA para o site de recuperação de desastre para um SID primário. No diagrama, apenas o SID único é representado no site primário, mas o multisid (MCOS) também é suportado. No local de DR, a unidade HLI é usada para a instância de controle de qualidade, enquanto as operações de produção estão sendo executadas a partir do site primário. No momento do failover de recuperação de falhas (ou teste de failover), a instância do controle de qualidade no site de recuperação de desastres é desativada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-Node-HSR-Dr-cost-Optimized-121. png](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI/HSR |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|/hana/shared/QA-SID | Instalação do HANA para o QA SID | 
|/hana/data/QA-SID/mnt00001 | Arquivos de dados são instalados para o QA SID | 
|/hana/log/QA-SID/mnt00001 | Arquivos de log são instalados para o QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Consulte a seção [Visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória são suportados com o ambiente multisid.
- Na recuperação de desastre: os volumes e montagem são configurados (marcados como "instância de produção no local de recuperação de desastre") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- No DR: Os dados, logbackups, log, volumes compartilhados para QA (marcados como “instalação da Instância do QA”) são configurados para a instalação da instância do QA.
- Nó primário obtenha sincronização com o nó de DR usando a replicação de sistema do HANA. 
- [Alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para vincular os circuitos do ExpressRoute em conjunto para tornar uma rede privada entre sua rede de regiões.

## <a name="13-high-availability-and-disaster-recovery-with-hsr"></a>13. alta disponibilidade e recuperação de desastre com o HSR 
 
 Essa topologia dá suporte a dois nós para a configuração de HSR (replicação do sistema HANA) para a alta disponibilidade das regiões locais. Para a recuperação de desastre, o terceiro nó na região de DR é sincronizado do site primário usando HSR (modo assíncrono). 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Hana-System-Replication-Dr-131. png](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI/HSR |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Na recuperação de desastre: os volumes e montagem são configurados (marcados como "instância de DR de produção") para a instalação da instância do HANA de produção na unidade de DR HLI. 
- Nó do site primário obtenha a sincronização para o nó de DR usando a replicação de sistema do HANA. 
- [Alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para vincular os circuitos do ExpressRoute em conjunto para tornar uma rede privada entre sua rede de regiões.

## <a name="14-high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>14. alta disponibilidade e recuperação de desastre com HSR (com otimização de custo)
 
 Essa topologia dá suporte a dois nós para a configuração de HSR (replicação do sistema HANA) para a alta disponibilidade das regiões locais. Para a recuperação de desastre, o terceiro nó na região de DR é sincronizado do site primário usando HSR (modo assíncrono), enquanto uma outra instância (por exemplo, P e r) já está ficando fora do nó de recuperação de desastre. 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Hana-System-Replication-Dr-cost-Optimized-141. png](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI/HSR |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|/hana/shared/QA-SID | Instalação do HANA para o QA SID | 
|/hana/data/QA-SID/mnt00001 | Arquivos de dados são instalados para o QA SID | 
|/hana/log/QA-SID/mnt00001 | Arquivos de log são instalados para o QA SID |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Na recuperação de desastre: os volumes e montagem são configurados (marcados como "instância de DR de produção") para a instalação da instância do HANA de produção na unidade de DR HLI. 
- No DR: Os dados, logbackups, log, volumes compartilhados para QA (marcados como “instalação da Instância do QA”) são configurados para a instalação da instância do QA.
- Nó do site primário obtenha a sincronização para o nó de DR usando a replicação de sistema do HANA. 
- [Alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para vincular os circuitos do ExpressRoute em conjunto para tornar uma rede privada entre sua rede de regiões.

## <a name="15-scale-out-with-dr-using-hsr"></a>15. Escale horizontalmente com DR usando HSR
 
Esta topologia permite vários nós em uma expansão com DR. Você pode solicitar essa topologia com ou sem o nó de espera. Os nós do site primário são sincronizados com os nós do site de recuperação de desastre com a replicação do sistema do HANA (modo assíncrono).


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

[![scale-out-Dr-HSR-151. png](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DA NIC | TIPO SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | > de\<de VLAN tenantNo | team0.tenant | Cliente para HLI/HSR |
| b | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó DR**|
|/hana/shared | Instalação do HANA para produção SID | 
|/hana/data/SID/mnt00001 | Arquivos de dados são instalados para o SID de produção | 
|/hana/log/SID/mnt00001 | Arquivos de log são instalados para o SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Considerações-chave
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Na recuperação de desastre: os volumes e montagem são configurados para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- Os nós do site primário são sincronizados com nós de DR usando a replicação de sistema do HANA. 
- [Alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para vincular os circuitos do ExpressRoute em conjunto para tornar uma rede privada entre sua rede de regiões.


## <a name="next-steps"></a>Próximos passos
- Consulte [Infraestrutura e conectividade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) para o HLI
- Consulte [Alta disponibilidade e recuperação de desastres](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para o HLI
