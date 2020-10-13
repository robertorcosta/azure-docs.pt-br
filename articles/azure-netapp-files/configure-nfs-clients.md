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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449657"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurar um cliente NFS para o Azure NetApp Files

A configuração de cliente NFS descrita neste artigo faz parte da configuração quando você [configura a criptografia Kerberos do nfsv 4.1](configure-kerberos-encryption.md) ou [cria um volume de protocolo duplo](create-volumes-dual-protocol.md). Uma ampla variedade de distribuições do Linux está disponível para uso com o Azure NetApp Files. Este artigo descreve as configurações para dois dos ambientes mais comumente usados: RHEL 8 e Ubuntu 18, 4. 

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
Esta seção descreve a configuração do Ubuntu para os clientes NFS.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Se você estiver usando a criptografia Kerberos do NFSv 4.1 

1. Instalar pacotes:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configure o cliente NTP.  
    O Ubuntu 18, 4 usa `chrony` por padrão.  Seguindo as diretrizes de configuração no [Ubuntu Bionic: usando o chrony para configurar o NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Junte-se ao Domínio do Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Se você estiver usando o protocolo dual  

1. Execute o seguinte comando para atualizar os pacotes instalados:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Exemplo:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Execute o seguinte comando para reiniciar e habilitar o serviço:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

O exemplo a seguir consulta o servidor LDAP do AD do cliente do Ubuntu LDAP para um usuário LDAP `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Próximas etapas  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de protocolo duplo para Azure NetApp Files](create-volumes-dual-protocol.md)

