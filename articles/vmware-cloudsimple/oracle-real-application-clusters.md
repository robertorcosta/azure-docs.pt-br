---
title: Solução Azure VMware by CloudSimple - Otimize sua nuvem privada cloudsimple para Oracle RAC
description: Descreve como implantar um novo cluster e otimizar uma VM para instalação e configuração de Clusters de Aplicativos Reais Oracle (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016010"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Otimize sua Nuvem Privada Cloud Simple para instalar o Oracle RAC

Você pode implantar o Oracle Real Application Clusters (RAC) no ambiente CloudSimple Private Cloud. Este guia descreve como implantar um novo cluster e otimizar uma VM para a solução Oracle RAC. Depois de concluir as etapas neste tópico, você pode instalar e configurar o Oracle RAC.

## <a name="storage-policy"></a>Política de armazenamento

A implementação bem-sucedida do Oracle RAC requer um número adequado de nódulos no cluster.  Na política de armazenamento vSAN, as falhas de tolerar (FTT) são aplicadas aos discos de dados usados para armazenar os discos de banco de dados, log e redo.  O número necessário de nódulos para tolerar efetivamente falhas é 2N+1 onde N é o valor de FTT.

Exemplo: Se o FTT desejado for 2, então o número total de nós no cluster deve ser 2*2+1 = 5.

## <a name="overview-of-deployment"></a>Visão geral da implantação

As seções a seguir descrevem como configurar seu ambiente CloudSimple Private Cloud para Oracle RAC.

1. Práticas recomendadas para configuração de disco
2. Implantar cloudSimple Private Cloud vSphere Cluster
3. Configurar o Networking para Oracle RAC
4. Configurar políticas de armazenamento vSAN
5. Crie VMs Oracle e crie discos VM compartilhados
6. Configure as regras de afinidade VM-para-host

## <a name="best-practices-for-disk-configuration"></a>Práticas recomendadas para configuração de disco

As máquinas virtuais Oracle RAC têm vários discos, que são usados para funções específicas.  Os discos compartilhados são montados em todas as máquinas virtuais, que são usadas pelo Oracle RAC Cluster.  Os discos de instalação do sistema operacional e do software são montados apenas nas máquinas virtuais individuais.  

![Visão geral dos discos da máquina virtual Oracle RAC](media/oracle-vm-disks-overview.png)

A seguir, utiliza-se os discos definidos na tabela abaixo.

| Disco                                      | Finalidade                                       | Disco Compartilhado |
|-------------------------------------------|-----------------------------------------------|-------------|
| Sistema operacional                                        | Disco do sistema operacional                         | Não          |
| Grade                                      | Instalar localização para o software Oracle Grid     | Não          |
| DATABASE                                  | Instalar localização para o software de banco de dados Oracle | Não          |
| ORAHOME                                   | Localização base para binários de banco de dados Oracle    | Não          |
| DATA1, DATA2, DATA3, DATA4                | Disco onde os arquivos de banco de dados Oracle são armazenados   | Sim         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Discos de registro de redo                                | Sim         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Discos de votação                                  | Sim         |
| FRA1, FRA2                                | Discos de área de recuperação rápida                      | Sim         |

![Configuração de disco de máquina virtual Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

* Cada máquina virtual é configurada com quatro controladores SCSI.
* O tipo de controlador SCSI é definido como paravirtual do VMware.
* Vários discos virtuais (.vmdk) são criados.
* Os discos são montados em diferentes controladores SCSI.
* O tipo de compartilhamento de vários escritores é definido para discos de cluster compartilhados.
* A política de armazenamento vSAN é definida para garantir alta disponibilidade de discos.

### <a name="operating-system-and-software-disk-configuration"></a>Configuração do sistema operacional e do disco de software

Cada máquina virtual Oracle é configurada com vários discos para o sistema operacional host, swap, instalação de software e outras funções do SISTEMA OPERACIONAL.  Esses discos não são compartilhados entre as máquinas virtuais.  

* Três discos para cada máquina virtual são configurados como discos virtuais e montados em máquinas virtuais Oracle RAC.
    * Disco do sistema operacional
    * Disco para armazenar Oracle Grid instala arquivos
    * Disco para armazenar arquivos de instalação de banco de dados Oracle
* Os discos podem ser configurados como **Provisionado fino**.
* Cada disco é montado no primeiro controlador SCSI (SCSI0).  
* O compartilhamento é definido **como Sem compartilhamento**.
* A redundância é definida no armazenamento usando políticas vSAN.  

![Configuração do grupo de disco de dados Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configuração do disco de dados

Os discos de dados são usados principalmente para armazenar arquivos de banco de dados.  

* Quatro discos são configurados como discos virtuais e montados em todas as máquinas virtuais Oracle RAC.
* Cada disco é montado em um controlador SCSI diferente.
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.  
* O compartilhamento é definido como **Multi-writer**.  
* Os discos devem ser configurados como um grupo de discos ASM (Automatic Storage Management, gerenciamento automático de armazenamento).  
* A redundância é definida no armazenamento usando políticas vSAN.  
* A redundância ASM está definida como redundância **externa.**

![Configuração do grupo de disco de dados Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Configuração do disco de log refazer

Os arquivos de registro redo são usados para armazenar uma cópia das alterações feitas no banco de dados.  Os arquivos de registro são usados quando os dados precisam ser recuperados após quaisquer falhas.

* Os discos de log redo devem ser configurados como vários grupos de disco.  
* Seis discos são criados e montados em todas as máquinas virtuais Oracle RAC.
* Os discos são montados em diferentes controladores SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* O compartilhamento é definido como **Multi-writer**.  
* Os discos devem ser configurados como dois grupos de discos ASM.
* Cada grupo de disco ASM contém três discos, que estão em diferentes controladores SCSI.  
* A redundância ASM está definida como redundância **normal.**
* Cinco arquivos de log redo são criados em ambos os grupos de log ASM Redo

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Configuração do grupo de log de log do Oracle RAC refazer](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configuração do disco de votação Oracle

Os discos de votação fornecem a funcionalidade do disco de quórum como um canal de comunicação adicional para evitar qualquer situação de cérebro dividido.

* Cinco discos são criados e montados em todas as máquinas virtuais Oracle RAC.
* Os discos são montados em um controlador SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* O compartilhamento é definido como **Multi-writer**.  
* Os discos devem ser configurados como um grupo de discos ASM.  
* A redundância ASM está definida como **alta** redundância.

![Configuração do grupo de disco de votação Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Configuração do disco da área de recuperação rápida Oracle (opcional)

A área de recuperação rápida (FRA) é um sistema de arquivos gerenciado pelo grupo de discos Oracle ASM.  O FRA fornece um local de armazenamento compartilhado para arquivos de backup e recuperação. A Oracle cria registros arquivados e registros de flashback na área de recuperação rápida. O Oracle Recovery Manager (RMAN) pode, opcionalmente, armazenar seus conjuntos de backup e cópias de imagem na área de recuperação rápida, e ele o usa ao restaurar arquivos durante a recuperação de mídia.

* Dois discos são criados e montados em todas as máquinas virtuais Oracle RAC.
* Os discos são montados em diferentes controladores SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* O compartilhamento é definido como **Multi-writer**.  
* Os discos devem ser configurados como um grupo de discos ASM.  
* A redundância ASM está definida como redundância **externa.**

![Configuração do grupo de disco de votação Oracle RAC](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Implantar o cluster CloudSimple Private Cloud vSphere

Para implantar um cluster vSphere em sua Nuvem Privada, siga este processo:

1. A partir do portal CloudSimple, [crie uma Nuvem Privada](create-private-cloud.md). O CloudSimple cria um usuário padrão do vCenter chamado 'cloudowner' na recém-criada Nuvem Privada. Para obter detalhes sobre o modelo padrão de permissão e usuário da Nuvem Privada, consulte [Aprenda o modelo de permissão da Private Cloud](learn-private-cloud-permissions.md).  Essa etapa cria o cluster de gerenciamento principal para sua Nuvem Privada.

2. A partir do portal CloudSimple, [expanda a Nuvem Privada](expand-private-cloud.md) com um novo cluster.  Este cluster será usado para implantar o Oracle RAC.  Selecione o número de nódulos com base na tolerância de falha desejada (mínimo três nós).

## <a name="set-up-networking-for-oracle-rac"></a>Configurar rede para Oracle RAC

1. Em sua Nuvem Privada, [crie dois VLANs](create-vlan-subnet.md), um para a rede pública Oracle e outro para a rede privada Oracle e atribua CIDRs de sub-rede apropriadas.
2. Depois que os VLANs forem criados, crie os [grupos de portas distribuídas no vCenter private cloud](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Configure uma [máquina virtual de servidor DHCP e DNS](dns-dhcp-setup.md) em seu cluster de gerenciamento para o ambiente Oracle.
4. [Configure o encaminhamento de DNS no servidor DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) instalado na Nuvem Privada.

## <a name="set-up-vsan-storage-policies"></a>Configurar políticas de armazenamento vSAN

As políticas vSAN definem as falhas de toleração e striping de disco para os dados armazenados nos discos VM.  A política de armazenamento criada deve ser aplicada nos discos VM durante a criação da VM.

1. [Faça login no cliente vSphere](https://docs.azure.cloudsimple.com/vsphere-access) da sua Nuvem Privada.
2. No menu superior, selecione **Políticas e Perfis**.
3. No menu à esquerda, selecione **Políticas de armazenamento em VM** e selecione Criar uma política de armazenamento em **VM**.
4. Digite um nome significativo para a política e clique **EM NEXT**.
5. Na seção **Estrutura de diretiva,** **selecione Habilitar regras para armazenamento vSAN** e clique **em NEXT**.
6. Na seção **vSAN** > **Disponibilidade,** **selecione Nenhum** para tolerância a desastres no Site. Para que as falhas tolerem, selecione a opção **RAID - Espelhamento** para o FTT desejado.
    ![configurações](media/oracle-rac-storage-wizard-vsan.png)vSAN .
7. Na seção **Avançado,** selecione o número de listras de disco por objeto. Para reserva de espaço Objeto, selecione **'Espessura provisionada**.' Selecione **Desativar a soma de verificação de objetos**. Clique **EM NEXT**.
8. Siga as instruções na tela para visualizar a lista de datastores vSAN compatíveis, revise as configurações e termine a configuração.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Crie VMs Oracle e crie discos VM compartilhados para Oracle

Para criar uma VM para oracle, clone uma VM existente ou crie uma nova.  Esta seção descreve como criar uma nova VM e cloná-la para criar uma segunda após a instalação do sistema operacional base.  Depois que as VMs forem criadas, você pode criar um add disks para eles.  O cluster Oracle usa discos compartilhados para armazenar, dados, logs e registros de refazer.

### <a name="create-vms"></a>Criar VMs

1. No vCenter, clique no ícone **Hosts e Clusters.** Selecione o cluster que você criou para o Oracle.
2. Clique com o botão direito do mouse no cluster e selecione **Nova máquina virtual**.
3. Selecione **Criar nova máquina virtual** e clique em **Next**.
4. Nomeie a máquina, selecione a localização da VM Oracle e clique **em Next**.
5. Selecione o recurso de cluster e clique **em Avançar**.
6. Selecione o datastore vSAN para o cluster e clique **em Next**.
7. Mantenha a seleção padrão de compatibilidade ESXi 6.5 e clique **em Next**.
8. Selecione o sistema operacional convidado da ISO para a VM que você está criando e clique **em Next**.
9. Selecione o tamanho do disco rígido necessário para a instalação do sistema operacional.
10. Para instalar o aplicativo em um dispositivo diferente, clique em **Adicionar novo dispositivo**.
11. Selecione as opções de rede e atribua o grupo de portas distribuídas criado para a rede pública.
12. Para adicionar interfaces de rede adicionais, clique **em Adicionar novo dispositivo** e selecione o grupo de portas distribuídas criado para a rede privada.
13. Para Nova unidade DC/DVD, selecione o arquivo ISO do datastore que contém o ISO para a instalação do sistema operacional preferido. Selecione o arquivo que você carregou anteriormente na pasta ISOs e Modelos e clique em **OK**.
14. Revise as configurações e clique em **OK** para criar a nova VM.
15. Energia na VM. Instale o sistema operacional e quaisquer atualizações necessárias

Depois que o sistema operacional é instalado, você pode clonar uma segunda VM. Clique com o botão direito do mouse na entrada VM e selecione a opção e clone.

### <a name="create-shared-disks-for-vms"></a>Criar discos compartilhados para VMs

A Oracle usa disco compartilhado para armazenar os arquivos de registro de dados, log e refazer.  Você pode criar um disco compartilhado no vCenter e montá-lo em ambas as VMs.  Para obter um desempenho superior, coloque os discos de dados em diferentes controladores SCSI Etapas abaixo mostram como criar um disco compartilhado no vCenter e, em seguida, anexá-lo a uma máquina virtual. o cliente vCenter Flash é usado para modificar as propriedades da VM.

#### <a name="create-disks-on-the-first-vm"></a>Criar discos na primeira VM

1. No vCenter, clique com o botão direito do mouse em uma das VMs oracle e selecione **Editar configurações**.
2. Na nova seção do dispositivo, selecione **o controlador SCSI** e clique **em Adicionar**.
3. Na nova seção do dispositivo, selecione **Novo disco rígido** e clique em **Adicionar**.
4. Expanda as propriedades do novo disco rígido.
5. Especifique o tamanho do disco rígido.
6. Especifique a política de armazenamento vM para ser a política de armazenamento vSAN que você definiu anteriormente.
7. Selecione o local como uma pasta no datastore vSAN. O local ajuda na navegação e na fixação dos discos em uma segunda VM.
8. Para o provisionamento de disco, selecione **A provisão grossa ansiosa zerada**.
9. Para compartilhar, especifique **Multi-writer**.
10. Para o nó do dispositivo virtual, selecione o novo controlador SCSI que foi criado na etapa 2.

    ![Criar discos na primeira VM](media/oracle-rac-new-hard-disk.png)

Repita as etapas 2 – 10 para todos os novos discos necessários para os arquivos de registro de dados Oracle, logs e refazer.

#### <a name="attach-disks-to-second-vm"></a>Anexar discos à segunda VM

1. No vCenter, clique com o botão direito do mouse em uma das VMs oracle e selecione **Editar configurações**.
2. Na nova seção do dispositivo, selecione **o controlador SCSI** e clique **em Adicionar**.
3. Na nova seção do dispositivo, selecione **Disco rígido existente** e clique em **Adicionar**.
4. Navegue até o local onde o disco foi criado para a primeira VM e selecione o arquivo VMDK.
5. Especifique a política de armazenamento vM para ser a política de armazenamento vSAN que você definiu anteriormente.
6. Para o provisionamento de disco, selecione **A provisão grossa ansiosa zerada**.
7. Para compartilhar, especifique **Multi-writer**.
8. Para o nó do dispositivo virtual, selecione o novo controlador SCSI que foi criado na etapa 2.

    ![Criar discos na primeira VM](media/oracle-rac-existing-hard-disk.png)

Repita as etapas 2 – 7 para todos os novos discos necessários para os arquivos de dados, logs e registros do Oracle.

## <a name="set-up-vm-host-affinity-rules"></a>Configure as regras de afinidade do host VM

As regras de afinidade VM-para-host garantem que a VM seja executada no host desejado.  Você pode definir regras no vCenter para garantir que o VM Oracle seja executado no host com recursos adequados e para atender a quaisquer requisitos específicos de licenciamento.

1. No portal CloudSimple, [aumente os privilégios](escalate-private-cloud-privileges.md) do usuário cloudowner.
2. [Faça login no cliente vSphere](https://docs.azure.cloudsimple.com/vsphere-access) da sua Nuvem Privada.
3. No cliente vSphere, selecione o cluster onde as VMs Oracle são implantadas e clique **em Configurar**.
4. Em Configure, selecione **Grupos VM/Host**.
5. Clique **+** em .
6. Adicione um grupo VM. Selecione o **grupo VM** como o tipo. Digite o nome do grupo. Selecione as VMs e clique em **OK** para criar o grupo.
6. Adicione um grupo de host. Selecione **O Grupo de Host** como o tipo. Digite o nome do grupo. Selecione os hosts onde as VMs serão executadas e clique em **OK** para criar o grupo.
7. Para criar uma regra, clique em **VM/Host rules**.
8. Clique **+** em .
9. Digite um nome para a regra e verifique **Ativar**.
10. Para o tipo de regra, selecione **Máquinas Virtuais para hospedar**.
11. Selecione o grupo VM que contém as VMs Oracle.
12. Selecione **Deve ser executado em hosts neste grupo**.
13. Selecione o grupo de host que você criou.
14. Clique em **OK** para criar a regra.

## <a name="references"></a>Referências

* [Sobre as políticas vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Atributo multi-escritor VMware para VMDKs compartilhados](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
