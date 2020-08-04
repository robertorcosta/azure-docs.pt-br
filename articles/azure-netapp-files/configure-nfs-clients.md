---
title: Configurar um cliente NFS para Azure NetApp Files | Microsoft Docs
description: Descreve como configurar clientes NFS para usar com o Azure NetApp Files.
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
ms.topic: how-to
ms.date: 07/27/2020
ms.author: b-juche
ms.openlocfilehash: bdcffcea8d695b6a3d49272ffa135187d77aae1e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535312"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurar um cliente NFS para Azure NetApp Files

Uma ampla variedade de distribuições do Linux está disponível para uso com o Azure NetApp Files. Este artigo descreve as configurações para dois dos ambientes mais comumente usados: RHEL 8 e Ubuntu 18, 4.

Independentemente do tipo de Linux que você usa, as seguintes configurações são necessárias:
* Configure um cliente NTP para evitar problemas com distorção de tempo.
* Configurar entradas DNS do cliente Linux para resolução de nomes.  
    Essa configuração inclui o registro "A" (encaminhar) e o registro de PTR (reverso). 
* Para ingresso no domínio, crie uma conta de computador no Active Directory de destino (que é criado durante o comando de junção de realm). 
    > [!NOTE] 
    > A `$SERVICEACCOUNT` variável usada nos comandos abaixo deve ser uma conta de usuário com permissões ou delegação para criar uma conta de computador na unidade organizacional de destino.
* Habilite o cliente para montar volumes de NFS e outras ferramentas de monitoramento relevantes.

## <a name="rhel-8-configuration"></a>Configuração do RHEL 8 

1. Instalar pacotes:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Configurar o cliente NTP:  
    O RHEL 8 usa `chrony` por padrão.  Seguindo as diretrizes de configuração no [uso do chrony Suite para configurar o NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Ingresse no domínio de Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Configuração do Ubuntu 

1. Instalar pacotes:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configure o cliente NTP.  
    O Ubuntu 18, 4 usa `chrony` por padrão.  Seguindo as diretrizes de configuração no [Ubuntu Bionic: usando o chrony para configurar o NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Junte-se ao Domínio do Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Próximas etapas  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de protocolo duplo para Azure NetApp Files](create-volumes-dual-protocol.md)

