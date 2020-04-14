---
title: Implantação do DBMS de Máquinas de Virtuais do ASE SAP do Azure para carga de trabalho do SAP | Microsoft Docs
description: Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273198"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP

Neste documento, aborda várias áreas diferentes a serem consideradas ao implantar o ASE SAP no IaaS do Azure. Como uma pré-condição para este documento, você deve ler o documento [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md), e outros guias de [carga de trabalho do SAP na documentação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Este documento abrange o SAP ASE em execução no Linux e no Windows Operating Systems. A versão mínima suportada no Azure é sap ASE 16.0.02 (Release 16 Support Pack 2). Recomenda-se implantar a versão mais recente do SAP e o mais recente Patch Level.  Como um SAP ASE mínimo 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7) é recomendado.  A versão mais recente do SAP pode ser encontrada no [Targeted ASE 16.0 Release Schedule e nas informações da lista de CR](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Informações adicionais sobre suporte à liberação com aplicativos SAP ou localização de mídia de instalação são encontradas, além da Matriz de Disponibilidade de Produtos SAP nesses locais:

- [Nota de suporte SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Nota de suporte SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Nota de suporte SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Nota de suporte SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Observação: Durante toda a documentação dentro e fora do mundo SAP, o nome do produto é referenciado como Sybase ASE ou SAP ASE ou em alguns casos ambos. Para se manter consistente, usamos o nome **SAP ASE** nesta documentação.

## <a name="operating-system-support"></a>Suporte do sistema operacional
A Matriz de Disponibilidade de Produto SAP contém as combinações de sistema operacional e kernel SAP suportadas para cada aplicativo SAP.  Distribuições Linux SUSE 12.x, SUSE 15.x, Red Hat 7.x são totalmente suportados.  O Oracle Linux como sistema operacional para SAP ASE não é suportado.  Recomenda-se usar as versões linux mais recentes disponíveis. Os clientes do Windows devem usar as versões do Windows Server 2016 ou Do Windows Server 2019.  Versões mais antigas do Windows, como o Windows 2012, são tecnicamente suportadas, mas a versão mais recente do Windows é sempre recomendada.


## <a name="specifics-to-sap-ase-on-windows"></a>Informações específicas para o SAP ASE no Windows
Começando com o Microsoft Azure, você pode migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. O SAP ASE em uma Máquina Virtual do Azure permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresarial migrando facilmente esses aplicativos para o Microsoft Azure. Com o SAP ASE em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.

O Microsoft Azure oferece vários tipos de máquinas virtuais diferentes que permitem que você execute menores sistemas SAP e paisagens até grandes sistemas SAP e paisagens com milhares de usuários. Os números SAPS de dimensionamento SAPS das diferentes SAP certificadas VM SKUs são fornecidos na [nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).

A documentação para instalar o SAP ASE no Windows pode ser encontrada no [Guia de Instalação SAP ASE para Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

'Bloquear páginas na memória' é uma configuração que impedirá que o buffer de banco de dados SAP ASE seja excluído.  Esta configuração é útil para grandes sistemas ocupados com muita memória. Entre em contato com o BC-DB-SYB para obter mais informações. 


## <a name="linux-operating-system-specific-settings"></a>Configurações específicas do sistema operacional Linux
Em VMs Linux, execute `saptune` com o perfil SAP-ASE Linux Huge Pages deve ser habilitado por padrão e pode ser verificado com comando  

`cat /proc/meminfo` 

O tamanho da página é tipicamente 2048 KB. Para obter detalhes, consulte o artigo [Páginas Enormes no Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Recomendações sobre a estrutura de VM e disco para implantações SAP ASE

SAP ASE for SAP NetWeaver Applications é suportado em qualquer tipo de VM listado na [nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) tipos típicos de VM usados para servidores de banco de dados SAP ASE de tamanho médio incluem Esv3.  Grandes bancos de dados de vários terabytes podem aproveitar os tipos de VM da série M. O desempenho de gravação de registro de transação SAP ASE pode ser melhorado ativando o M-series Write Accelerator. O Write Accelerator deve ser testado cuidadosamente com o SAP ASE devido à maneira como o SAP ASE executa gravações de log.  Revise [a nota de suporte sap #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) e considere executar um teste de desempenho.  
O Write Accelerator foi projetado apenas para registro de transações. O cache de nível de disco deve ser definido como NENHUM. Não se surpreenda se o Azure Write Accelerator não mostrar melhorias semelhantes às de outros DBMS. Com base na forma como o SAP ASE grava no registro de transações, pode ser que haja pouca ou nenhuma aceleração pelo Azure Write Accelerator.
Discos separados são recomendados para dispositivos de dados e dispositivos de log.  Os bancos de dados `saptools` do sistema não exigem discos dedicados e podem ser colocados nos discos que contêm os dados do banco de dados SAP e dispositivos de log 

![Configuração de armazenamento para SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Sistemas de arquivos, tamanho de listras & balanceamento IO 
O SAP ASE grava os dados sequencialmente em dispositivos de armazenamento em disco, a menos que configurado em contrário. Isso significa que um banco de dados SAP ASE vazio com quatro dispositivos gravará dados apenas no primeiro dispositivo.  Os outros dispositivos de disco só serão gravados quando o primeiro dispositivo estiver cheio.  A quantidade de READ e WRITE IO para cada dispositivo SAP ASE provavelmente será diferente. Para equilibrar o IO de disco em todos os discos Azure disponíveis, tanto o Windows Storage Spaces quanto o Linux LVM2 precisam ser usados. No Linux, recomenda-se usar o sistema de arquivos XFS para formatar os discos. O tamanho da listra LVM deve ser testado com um teste de desempenho. 128 KB tamanho listrado é um bom ponto de partida. No Windows, o Tamanho da Unidade de Alocação NTFS (AUS) deve ser testado. 64 KB pode ser usado como um valor inicial. 

Recomenda-se configurar a expansão automática do banco de dados conforme descrito no artigo [Configurando a expansão automática do espaço do banco de dados no SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) e [na nota de suporte sap #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Exemplo SAP ASE nas configurações de máquina virtual, disco e sistema de arquivos do Azure 
Os modelos abaixo mostram configurações de exemplo para Linux e Windows. Antes de confirmar a configuração da máquina e do disco virtual, certifique-se de que as cotas de largura de banda de rede e armazenamento da VM individual sejam suficientes para atender ao requisito de negócios. Também tenha em mente que diferentes tipos de VM do Azure têm diferentes números máximos de discos que podem ser anexados à VM. Por exemplo, uma VM E4s_v3 tem um throughput de IO de armazenamento de 48 MB/s limite. Se o throughput de armazenamento exigido pela atividade de backup do banco de dados exigir mais de 48 MB/seg, então um tipo de VM maior com mais throughput de largura de banda de armazenamento é inevitável. Ao configurar o armazenamento Azure, você também precisa ter em mente que, especialmente com o [armazenamento Azure Premium,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) o throughput e o IOPS por GB de capacidade mudam. Veja mais sobre este tópico no artigo [Quais tipos de disco estão disponíveis no Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). As cotas para tipos específicos de VM do Azure estão documentadas no artigo [Memória otimizada tamanhos de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) e artigos vinculados a ele. 

> [!NOTE]
>  Se um sistema DBMS estiver sendo movido de on-premises para Azure, recomenda-se realizar o monitoramento na VM e avaliar a CPU, memória, IOPS e throughput de armazenamento. Compare os valores de pico observados com os limites de cotas de VM documentados nos artigos mencionados acima

Os exemplos abaixo são para fins ilustrativos e podem ser modificados com base nas necessidades individuais. Devido ao design do SAP ASE, o número de dispositivos de dados não é tão crítico quanto com outros bancos de dados. O número de dispositivos de dados detalhados neste documento é apenas um guia. 

Um exemplo de configuração para um pequeno Servidor SAP ASE DB com um tamanho de banco de dados entre 50 GB – 250 GB, como o SAP Solution Manager, pode parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 4 | 4 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | Xfs |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 2 x P10 (RAID0) | Armazenamento premium: 2 x P10 (RAID0)| Cache = Somente leitura |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo instância única |
| # de dispositivos de backup | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |


Um exemplo de configuração para um servidor SAP ASE DB médio com um tamanho de banco de dados entre 250 GB – 750 GB, como um sistema menor do SAP Business Suite, pode parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | E16s_v3 (RAM de 16 vCPU/128 GB) | E16s_v3 (RAM de 16 vCPU/128 GB) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 8 | 8 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | Xfs |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4 x P20 (RAID0) | Armazenamento premium: 4 x P20 (RAID0)| Cache = Somente leitura |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo instância única |
| # de dispositivos de backup | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |

Um exemplo de configuração para um pequeno Servidor SAP ASE DB com um tamanho de banco de dados entre 750 GB – 2000 GB, como um sistema sap business suite maior, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 16 | 16 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | Xfs |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4 x P30 (RAID0) | Armazenamento premium: 4 x P30 (RAID0)| Cache = Somente leitura |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo instância única |
| # de dispositivos de backup | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |


Um exemplo de configuração para um pequeno Servidor SAP ASE DB com um tamanho de banco de dados de 2 TB+, como um sistema SAP Business Suite maior usado globalmente, pode parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | Série M (RAM de 1,0 a 4,0 TB)  | Série M (RAM de 1,0 a 4,0 TB) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 32 | 32 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | Xfs |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4+ x P30 (RAID0) | Armazenamento premium: 4+ x P30 (RAID0)| Cache = Somente leitura, considere o disco Azure Ultra |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NONE, Considere o disco Azure Ultra |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo instância única |
| # de dispositivos de backup | 16 | 16 | --- |
| # e tipo de discos de backup | 4 | 4 | Use espaços de armazenamento/LVM2 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Backup & restaurar considerações para SAP ASE no Azure
Aumentar o número de dados e dispositivos de backup aumenta o desempenho de backup e restauração. Recomenda-se a listra dos discos Azure que hospedam o dispositivo de backup SAP ASE como mostrado nas tabelas mostradas anteriormente. Deve-se ter cuidado para equilibrar o número de dispositivos de backup e discos e garantir que o throughput de backup não deve exceder 40%-50% da cota total de rendimento supida da VM. Recomenda-se usar a Compactação de Backup SAP como padrão. Mais detalhes podem ser encontrados nos artigos:

- [Nota de suporte SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Nota de suporte SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Nota de suporte SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Não use a unidade D:\ ou /espaço temporário como banco de dados ou destino de despejo de log.

### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações onde a largura de banda de I/O pode se tornar um fator limitante, medidas que reduzem o IOPS podem ajudar a esticar a carga de trabalho que se pode executar em um cenário IaaS como o Azure. Portanto, é recomendável certificar-se de que a compactação do SAP ASE seja usada antes de carregar um banco de dados SAP existente no Azure.

A recomendação para aplicar a compactação antes de carregar no Azure é fornecida por várias razões:

* A quantidade de dados a ser carregada no Azure é menor
* A duração da execução de compactação é menor, presumindo-se que seja possível usar um hardware mais poderoso com mais CPUs ou maior largura de banda de E/S ou menor latência de E/S local
* Tamanhos menores de banco de dados podem levar a menos custos de alocação de disco

A compactação de LOB e dados funciona em uma VM hospedada em máquinas virtuais do Azure como o faz localmente. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados SAP ASE existente, verifique a [nota de suporte sap 1750510](https://launchpad.support.sap.com/#/notes/1750510). Para obter mais detalhes sobre a compactação do banco de dados SAP ASE, verifique a [nota de suporte sap #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Alta disponibilidade de SAP ASE no Azure 
O Guia de Usuários HADR detalha a configuração e a configuração de uma solução SAP ASE de 2 nós "Always-on".  Além disso, um terceiro nó de recuperação de desastres também é suportado. O SAP ASE suporta muitas configurações de alta disponibilidade, incluindo disco compartilhado e clustering nativo do Sistema Operacional (IP flutuante). A única configuração suportada no Azure é o uso do Fault Manager sem IP flutuante.  O método Floating IP Address não funcionará no Azure.  O Kernel SAP é um aplicativo "HA Aware" e conhece os servidores SAP ASE primários e secundários. Não há integrações estreitas entre o SAP ASE e o Azure, o balanceador de carga Interno Do Azure não é usado. Portanto, a documentação Padrão SAP ASE deve ser seguida a partir do [Guia de Usuários SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> A única configuração suportada no Azure é o uso do Fault Manager sem IP flutuante.  O método Floating IP Address não funcionará no Azure. 

### <a name="third-node-for-disaster-recovery"></a>Terceiro nó para recuperação de desastres
Além de usar o SAP ASE Always-On para alta disponibilidade local, você pode querer estender a configuração para um nó assíncronamente replicado em outra região do Azure. A documentação para tal cenário pode ser encontrada [aqui.](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

## <a name="sap-ase-database-encryption--ssl"></a>Criptografia de banco de dados SAP ASE & SSL 
O SAP Software provisioning Manager (SWPM) está dando uma opção para criptografar o banco de dados durante a instalação.  Se você quiser usar criptografia, recomenda-se usar a Criptografia de Banco de Dados Completo SAP.  Veja detalhes documentados em:

- [Nota de suporte SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Nota de suporte SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Nota de suporte SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Nota de suporte SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Se um banco de dados SAP ASE for criptografado, a Compactação de despejo de backup não funcionará. Veja também [a nota de suporte sap #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE na lista de verificação de implantação do Azure
 
- Implantar SAP ASE 16.0.03.07 ou superior
- Atualização para a versão mais recente e patches de FaultManager e SAPHostAgent
- Implantar no so mais recente certificado disponível, como Windows 2019, Suse 15.1 ou Redhat 7.6 ou superior
- Use VMs certificados SAP – SKUs Azure VM de alta memória, como Es_v3 ou para sistemas x-large Série VM SKUs são recomendados
- Combine o disco IOPS e a cota total de throughput agregado da VM com o design do disco.  Implantar número suficiente de discos
- Discos agregados usando Espaços de Armazenamento do Windows ou Linux LVM2 com tamanho de listra correto e sistema de arquivos
- Crie um número suficiente de dispositivos para fins de dados, log, temporários e backup
- Considere usar o UltraDisk para sistemas x-large 
- Execute `saptune` o SAP-ASE no Sistema Operacional Linux 
- Proteja o banco de dados com criptografia DB – armazene manualmente as chaves no Azure Key Vault 
- Complete o [SAP na lista de verificação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Configure backup de log e backup completo 
- Teste HA/DR, faça backup e restauração e realize o teste de & de estresse 
- Confirmar a extensão automática do banco de dados está funcionando 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando o DBACockpit para monitorar instâncias de banco de dados
Para sistemas SAP que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit está acessível como janelas de navegador inseridas no DBACockpit da transação ou como Webdynpro. No entanto, a funcionalidade completa para monitorar e administrar o banco de dados está disponível apenas na implementação do Webdynpro do DBACockpit.

Como com sistemas locais, são necessárias várias etapas para habilitar toda a funcionalidade do SAP NetWeaver usada pela implementação do Webdynpro do DBACockpit. Siga [a nota de suporte sap #1245200](https://launchpad.support.sap.com/#/notes/1245200) para permitir o uso de webdynpros e gerar os necessários. Ao seguir as instruções nas notas acima, você`ICM`também configura o Gerenciador de Comunicação da Internet ( ) juntamente com as portas a serem usadas para conexões http e https. A configuração padrão para http tem esta aparência:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados no DBACockpit da transação são semelhantes a este:

> https:\//\<fullqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo de como a máquina virtual do Azure que hospeda o sistema SAP está conectado ao seu AD e DNS, você precisa se certificar de que o ICM esteja usando um nome de host totalmente qualificado que pode ser resolvido na máquina virtual da qual você está abrindo o DBACockpit. Consulte [a nota de suporte do SAP #773830](https://launchpad.support.sap.com/#/notes/773830) para entender como o ICM determina o nome de host totalmente qualificado com base nos parâmetros de perfil e definir o parâmetro ICM/host_name_full explicitamente, se necessário.

Se você implantou a VM em um cenário somente em nuvem sem conectividade entre o local e o `domainlabel`Azure, você precisa definir um endereço IP público e um . O formato do nome DNS público da VM tem esta aparência:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mais detalhes relacionados ao nome DNS podem ser encontrados [aqui][máquinas virtuais-azurerm-versus-azuresm].

Definindo o parâmetro de perfil do SAP icm/host_name_full para o nome DNS da VM do Azure, o link pode ser semelhante a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Nesse caso, você precisa se certificar de:

* Adicione regras de entrada ao Grupo de Segurança de Rede no portal do Azure para as portas TCP/IP usadas para comunicação com o ICM
* Adicionar regras de entrada para a configuração do Firewall do Windows para as portas TCP/IP usadas para se comunicar com o ICM

Para um processo importado automatizado de todas as correções disponíveis, recomenda-se aplicar periodicamente a Nota SAP da coleção de correções aplicável à sua versão do SAP:

* [Nota de suporte SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Nota de suporte SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Nota de suporte SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Mais informações sobre o DBA Cockpit para SAP ASE podem ser encontradas nas seguintes Notas SAP:

* [Nota de suporte SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Nota de suporte SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Nota de suporte SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Nota de suporte SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Nota de suporte SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Nota de suporte SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Nota de suporte SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Nota de suporte SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Links úteis, notas & whitepapers para SAP ASE
A página inicial do [SAP ASE 16.0.03.07 Documentação](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) fornece links para vários documentos dos quais os documentos de:

- Jornada de Aprendizagem SAP ASE - Administração & Monitoramento
- Jornada de aprendizagem SAP ASE - Atualização de & de instalação

são úteis. Outro documento útil são [os aplicativos SAP no SAP Adaptive Server Enterprise Práticas recomendadas para migração e tempo de execução](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Outras notas úteis de suporte SAP são:

- [Nota de suporte SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Nota de suporte SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Nota de suporte SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Nota de suporte SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Nota de suporte SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Nota de suporte SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Nota de suporte SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Nota de suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Nota de suporte SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Nota de suporte SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Nota de suporte SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota de suporte SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Outras informações são publicadas em 

- [Aplicativos SAP no SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [Infocenter SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always-on com configuração do 3º NÓ DR](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Um boletim informativo mensal é publicado através da [nota de suporte sap #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Próximas etapas
Confira o artigo [Cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

