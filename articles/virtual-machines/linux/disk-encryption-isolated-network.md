---
title: Criptografia de disco azure em uma rede isolada
description: Este artigo fornece dicas de solução de problemas para o Microsoft Azure Disk Encryption para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460113"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Criptografia de disco azure em uma rede isolada

Quando a conectividade estiver restrita por requisitos de proxy, firewall ou NSG (grupo de segurança de rede), a capacidade da extensão para executar as tarefas necessárias poderá ser interrompida. Essa interrupção pode resultar em mensagens de status como "Status da extensão não disponível na VM".

## <a name="package-management"></a>Gerenciamento de pacotes

A criptografia de disco do Azure depende de uma série de componentes, que normalmente são instalados como parte da ativação do ADE, se ainda não estiverem presentes. Quando atrás de um firewall ou isolado da Internet, esses pacotes devem ser pré-instalados ou disponíveis localmente.

Aqui estão os pacotes necessários para cada distribuição. Para obter uma lista completa de distros e tipos de volume [suportados, consulte VMs e sistemas operacionais suportados.](disk-encryption-overview.md#supported-vms-and-operating-systems)

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-partd, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

No Red Hat, quando um proxy é necessário, é preciso garantir que o gerenciador de assinaturas e o yum sejam configurados corretamente. Para saber mais, confira [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Como solucionar problemas do gerenciador de assinaturas e do yum).  

Quando os pacotes são instalados manualmente, eles também devem ser atualizados manualmente à medida que novas versões são lançadas.

## <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração do grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos pré-requisitos da configuração de rede documentada para criptografia de disco.  Consulte [a criptografia de disco do Azure: requisitos de rede](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Criptografia de disco azure com Azure AD (versão anterior)

Se usar [o Azure Disk Encryption com o Azure AD (versão anterior),](disk-encryption-overview-aad.md)a Biblioteca de Diretórios Ativos do [Azure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) precisará ser instalada manualmente para todas as distros (além dos pacotes apropriados para o distro, conforme [listado acima).](#package-management)

Quando a criptografia é habilitada com [credenciais do Azure AD](disk-encryption-linux-aad.md), a VM de destino deve permitir a conectividade com pontos de extremidade do Azure Active Directory e pontos de extremidade do Key Vault. Os pontos de extremidade de autenticação do Active Directory do Azure atuais são mantidos nas seções 56 e 59 da documentação [Intervalos de endereços IP e URLs do Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). As instruções do Key Vault são fornecidas na documentação sobre como [Acessar o Azure Key Vault por trás de um firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 

A máquina virtual deve ser capaz de acessar o ponto final do [serviço Azure Instance Metadata,](instance-metadata-service.md) que usa um endereço IP não routable bem conhecido (`169.254.169.254`) que pode ser acessado apenas a partir da VM.  As configurações proxy que alteram o tráfego HTTP local para este endereço (por exemplo, adicionando um cabeçalho X-Forwarded-For) não são suportadas.

## <a name="next-steps"></a>Próximas etapas

- Veja mais etapas para [solução de problemas de criptografia de disco do Azure](disk-encryption-troubleshooting.md)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
