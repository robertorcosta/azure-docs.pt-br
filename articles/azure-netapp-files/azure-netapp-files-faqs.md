---
title: Perguntas frequentes sobre arquivos do Azure NetApp | Microsoft Docs
description: Respostas frequentemente feitas perguntas sobre arquivos Do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547097"
---
# <a name="faqs-about-azure-netapp-files"></a>Perguntas frequentes sobre arquivos do Azure NetApp

Este artigo responde a perguntas frequentes (FAQs) sobre arquivos do Azure NetApp. 

## <a name="networking-faqs"></a>Perguntas frequentes em rede

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>O caminho de dados do NFS passa pela Internet?  

Não. O caminho de dados NFS não passa pela Internet. O Azure NetApp Files é um serviço nativo do Azure que é implantado na Rede Virtual Azure (VNet), onde o serviço está disponível. O Azure NetApp Files usa uma sub-rede delegada e fornece uma interface de rede diretamente no VNet. 

Consulte [Diretrizes para o planejamento da rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Posso conectar um VNet que já criei ao serviço Azure NetApp Files?

Sim, você pode conectar VNets que você criou ao serviço. 

Consulte [Diretrizes para o planejamento da rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Posso montar um volume NFS de Arquivos Azure NetApp usando o nome DNS FQDN?

Sim, você pode, se você criar as entradas DNS necessárias. O Azure NetApp Files fornece o IP do serviço para o volume provisionado. 

> [!NOTE] 
> O Azure NetApp Files pode implantar IPs adicionais para o serviço conforme necessário.  As entradas de DNS podem precisar ser atualizadas periodicamente.

## <a name="security-faqs"></a>Perguntas frequentes sobre segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre a VM do Azure e o armazenamento pode ser criptografado?

O tráfego de dados (tráfego do cliente NFSv3, NFSv4.1 ou SMBv3 para os volumes de arquivos do Azure NetApp) não é criptografado. No entanto, o tráfego de uma VM Azure (executando um cliente NFS ou SMB) para arquivos Do Azure NetApp é tão seguro quanto qualquer outro tráfego Azure-VM-to-VM. Esse tráfego é local para a rede de data center do Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser criptografado em repouso?

Todos os volumes de Arquivos Do Azure NetApp são criptografados usando o padrão FIPS 140-2. Todas as chaves são gerenciadas pelo serviço Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Como as chaves de criptografia são gerenciadas? 

O gerenciamento de chaves para arquivos Azure NetApp é tratado pelo serviço. Uma chave de criptografia de dados XTS-AES-256 exclusiva é gerada para cada volume. Uma hierarquia de chaves de criptografia é usada para criptografar e proteger todas as chaves de volume. Essas chaves de criptografia nunca são exibidas ou relatadas em um formato não criptografado. As chaves de criptografia são excluídas imediatamente quando um volume é excluído.

O suporte para chaves gerenciadas pelo usuário (Bring Your Own Keys) usando o Azure Dedicated HSM está disponível em uma base controlada nas regiões leste dos EUA, Oeste dos EUA2 e Centro-Sul dos EUA.  Você pode solicitar **anffeedback@microsoft.com**acesso em . Como a capacidade está disponível, os pedidos serão aprovados.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Posso configurar as regras da política de exportação do NFS para controlar o acesso ao alvo de montagem do serviço Azure NetApp Files?


Sim, você pode configurar até cinco regras em uma única política de exportação NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>O Azure NetApp Files suporta grupos de segurança de rede?

Não, atualmente você não pode aplicar Grupos de Segurança de Rede à sub-rede delegada do Azure NetApp Files ou às interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Posso usar o Azure IAM com arquivos do Azure NetApp?

Sim, o Azure NetApp Files suporta recursos RBAC com o Azure IAM.

## <a name="performance-faqs"></a>Perguntas frequentes sobre desempenho

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>O que devo fazer para otimizar ou ajustar o desempenho do Azure NetApp Files?

Você pode tomar as seguintes ações de acordo com os requisitos de desempenho: 
- Certifique-se de que a Máquina Virtual é dimensionada adequadamente.
- Habilite a rede acelerada para a VM.
- Selecione o nível de serviço e o tamanho desejados para o pool de capacidade.
- Crie um volume com o tamanho de cota desejado para a capacidade e desempenho.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Como converter os níveis de serviço baseados em throughput de Arquivos Azure NetApp para IOPS?

Você pode converter MB/s em IOPS usando a seguinte fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Como alterar o nível de serviço de um volume?

A alteração do nível de serviço de um volume não é suportada no momento.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como monitorar o desempenho do Azure NetApp Files?

O Azure NetApp Files fornece métricas de desempenho de volume. Você também pode usar o Azure Monitor para monitorar métricas de uso de Arquivos Do Azure NetApp.  Consulte [Métricas para Arquivos do Azure NetApp](azure-netapp-files-metrics.md) para a lista de métricas de desempenho para Arquivos Do Azure NetApp.

## <a name="nfs-faqs"></a>Perguntas frequentes da NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando uma VM do Azure for iniciada ou reiniciada.  Como configuro meu host para volumes NFS persistentes?

Para que um volume NFS seja montado automaticamente na inicialização `/etc/fstab` ou reinicialização da VM, adicione uma entrada ao arquivo no host. 

Consulte [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para obter detalhes.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Por que o comando DF no cliente NFS não mostra o tamanho do volume provisionado?

O tamanho do volume relatado no DF é o tamanho máximo que o volume de Arquivos Do Azure NetApp pode crescer. O tamanho do volume de Arquivos Do Azure NetApp no comando DF não reflete a cota ou o tamanho do volume.  Você pode obter o tamanho ou cota de volume do Azure NetApp Files através do portal Azure ou da API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Qual versão NFS o Azure NetApp Files suporta?

O Azure NetApp Files suporta NFSv3 e NFSv4.1. Você pode [criar um volume](azure-netapp-files-create-volumes.md) usando qualquer versão NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Como habilitar o esmagamento raiz?

O esmagamento de raiz não é suportado no momento.

## <a name="smb-faqs"></a>Perguntas frequentes sobre SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É necessária uma conexão Active Directory para acesso ao SMB? 

Sim, você deve criar uma conexão Active Directory antes de implantar um volume SMB. Os Controladores de Domínio especificados devem ser acessíveis pela sub-rede delegada do Azure NetApp Files para obter uma conexão bem-sucedida.  Consulte [Criar um volume de SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) para obter detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Quantas conexões de Diretório Ativo são suportadas?

O Azure NetApp Files não suporta várias conexões AD (Active Directory) em uma única *região*, mesmo que as conexões AD estejam em diferentes contas do NetApp. No entanto, você pode ter várias conexões aD em uma única *assinatura,* desde que as conexões AD estejam em diferentes regiões. Se você precisar de várias conexões aD em uma única região, você pode usar assinaturas separadas para fazê-lo. 

Uma conexão AD é configurada por conta do NetApp; a conexão AD é visível apenas através da conta NetApp em que é criada.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>O Azure NetApp Files suporta o Azure Active Directory? 

Tanto [os Serviços de Domínio do Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) quanto os Serviços de [Domínio de Diretório Ativo (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são suportados. Você pode usar os controladores de domínio do Active Directory existentes com arquivos do Azure NetApp. Os controladores de domínio podem residir no Azure como máquinas virtuais ou em locais via ExpressRoute ou S2S VPN. O Azure NetApp Files não suporta a adesão de Anúncios para [o Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) no momento.

Se você estiver usando arquivos do Azure NetApp com serviços de `OU=AADDC Computers` domínio do Diretório Ativo do Azure, o caminho da unidade organizacional é quando você configura o Active Directory para sua conta netapp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quais versões do Windows Server Active Directory são suportadas?

O Azure NetApp Files suporta versões do Windows Server 2008r2SP1-2019 dos Serviços de Domínio do Active Directory.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Por que o espaço disponível no meu cliente SMB não mostra o tamanho provisionado?

O tamanho de volume relatado pelo cliente SMB é o tamanho máximo que o volume de Arquivos do Azure NetApp pode crescer. O tamanho do volume de Arquivos do Azure NetApp como mostrado no cliente SMB não reflete a cota ou o tamanho do volume. Você pode obter o tamanho ou cota de volume do Azure NetApp Files através do portal Azure ou da API.

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>O Azure NetApp Files suporta a criptografia Kerberos?

Sim, por padrão, o Azure NetApp Files suporta criptografia AES-128 e AES-256 para tráfego entre o serviço e os controladores de domínio do Active Directory direcionados. Consulte [Criar um volume de SMB para arquivos do Azure NetApp](azure-netapp-files-create-volumes-smb.md) para requisitos. 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Perguntas frequentes sobre gestão de capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como monitorar o uso do pool de capacidade e do volume de Arquivos Azure NetApp? 

O Azure NetApp Files fornece métricas de uso de pool de capacidade e volume. Você também pode usar o Azure Monitor para monitorar o uso de Arquivos Do Azure NetApp. Consulte [Métricas para obter detalhes sobre métricas do Azure NetApp.](azure-netapp-files-metrics.md) 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Posso gerenciar arquivos do Azure NetApp através do Azure Storage Explorer?

Não. O Azure NetApp Files não é suportado pelo Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Como determino se um diretório está se aproximando do tamanho limite?

Você pode `stat` usar o comando de um cliente para ver se um diretório está se aproximando do limite máximo de tamanho (320 MB).

Para um diretório de 320 MB, o número de blocos é de 655360, com cada tamanho de bloco sendo 512 bytes.  (Ou seja, 320x1024x1024/512.)  

Exemplos:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Perguntas frequentes sobre migração e proteção de dados

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como migrar dados para arquivos do Azure NetApp?
O Azure NetApp Files fornece volumes NFS e SMB.  Você pode usar qualquer ferramenta de cópia baseada em arquivos para migrar dados para o serviço. 

O NetApp oferece uma solução baseada em SaaS, [o NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  A solução permite replicar dados NFS ou SMB para exportações NFS ou compartilhamentos de SMB do Azure NetApp Files. 

Você também pode usar uma ampla gama de ferramentas gratuitas para copiar dados. Para o NFS, você pode usar ferramentas de carga de trabalho, como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem em um volume de Arquivos Do Azure NetApp. Para SMB, você pode usar cargas de trabalho [robocopia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma maneira.  Essas ferramentas também podem replicar permissões de arquivos ou pastas. 

Os requisitos para migração de dados das instalações para os arquivos do Azure NetApp são os seguintes: 

- Certifique-se de que os Arquivos Azure NetApp estejam disponíveis na região alvo do Azure.
- Valide a conectividade de rede entre a fonte e o endereço IP de volume de volume do Azure NetApp Files. A transferência de dados entre as instalações e o serviço Azure NetApp Files é suportado pelo ExpressRoute.
- Crie o volume de arquivos do Azure NetApp.
- Transfira os dados de origem para o volume de destino usando sua ferramenta de cópia de arquivo preferida.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como criar uma cópia de um volume de Arquivos Do Azure NetApp em outra região do Azure?
    
O Azure NetApp Files fornece volumes NFS e SMB.  Qualquer ferramenta de cópia baseada em arquivos pode ser usada para replicar dados entre as regiões do Azure. 

A NetApp oferece uma solução baseada em SaaS, [netapp cloud sync](https://cloud.netapp.com/cloud-sync-service).  A solução permite replicar dados NFS ou SMB para exportações NFS ou compartilhamentos de SMB do Azure NetApp Files. 

Você também pode usar uma ampla gama de ferramentas gratuitas para copiar dados. Para o NFS, você pode usar ferramentas de carga de trabalho, como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem em um volume de Arquivos Do Azure NetApp. Para SMB, você pode usar cargas de trabalho [robocopia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma maneira.  Essas ferramentas também podem replicar permissões de arquivos ou pastas. 

Os requisitos para replicar um volume de arquivos do Azure NetApp para outra região do Azure são os seguintes: 
- Certifique-se de que os Arquivos Azure NetApp estejam disponíveis na região alvo do Azure.
- Valide a conectividade de rede entre VNets em cada região. Atualmente, o peering global entre VNets não é suportado.  Você pode estabelecer conectividade entre VNets ligando-se a um circuito ExpressRoute ou usando uma conexão S2S VPN. 
- Crie o volume de arquivos do Azure NetApp.
- Transfira os dados de origem para o volume de destino usando sua ferramenta de cópia de arquivo preferida.

### <a name="is-migration-with-azure-data-box-supported"></a>A migração com o Azure Data Box é suportada?

Não. A Azure Data Box não suporta arquivos Do Azure NetApp atualmente. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>A migração com o serviço De importação/exportação do Azure é suportada?

Não. O serviço Azure Import/Export não suporta arquivos Azure NetApp atualmente.

## <a name="next-steps"></a>Próximas etapas  

- [Perguntas frequentes sobre a Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Faq da rede virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Perguntas frequentes sobre o desempenho do SMB para arquivos do Azure NetApp](azure-netapp-files-smb-performance.md)
