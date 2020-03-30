---
title: Contêiner de perfil FSLogix de armazenamento Windows Virtual Desktop - Azure
description: Opções para armazenar seu perfil FSLogix de desktop virtual do Windows no Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127526"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opções de armazenamento para contêineres de perfil FSLogix no Windows Virtual Desktop

O Azure oferece várias soluções de armazenamento que você pode usar para armazenar seu contêiner de perfil FSLogix. Este artigo compara as soluções de armazenamento que o Azure oferece para contêineres de perfil de usuário do Windows Virtual Desktop FSLogix.

O Windows Virtual Desktop oferece os contêineres de perfil FSLogix como a solução recomendada de perfil de usuário. O FSLogix foi projetado para vagar perfis em ambientes de computação remota, como o Windows Virtual Desktop. No login, este contêiner é conectado dinamicamente ao ambiente de computação usando um Disco Rígido Virtual (VHD) com suporte nativo e um Disco Rígido Virtual Hyper-V (VHDX). O perfil do usuário está disponível imediatamente e aparece no sistema exatamente como um perfil de usuário nativo.

As tabelas a seguir comparam as soluções de armazenamento que o Azure Storage oferece para os perfis de usuários de contêineres do perfil Windows Virtual Desktop FSLogix.

## <a name="azure-platform-details"></a>Detalhes da plataforma Azure

|Recursos|Arquivos do Azure|Azure NetApp Files|Espaços de armazenamento Diretos|
|--------|-----------|------------------|---------------------|
|Caso de uso|Propósito geral|Ultra desempenho ou migração do NetApp no local|Multiplataforma|
|Serviço de plataforma|Sim, solução nativa do Azure|Sim, solução nativa do Azure|Não, auto-gerido|
|Disponibilidade regional|Todas as regiões|[Selecionar regiões](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Todas as regiões|
|Redundância|Localmente redundante/zona-redundante/geo-redundante|Localmente redundante|Localmente redundante/zona-redundante/geo-redundante|
|Níveis e desempenho|Standard<br>Premium<br>Até o máximo 100k IOPS por ação com 5 GBps por ação a cerca de 3 ms latência|Standard<br>Premium<br>Ultra<br>IOPS de até 320k (16K) com 4,5 GBps por volume a cerca de 1 ms de latência|HDD padrão: até 500 limites de IOPS por disco<br>SSD padrão: até 4k limites de IOPS por disco<br>SSD premium: até 20k limites de IOPS por disco<br>Recomendamos discos Premium para espaços de armazenamento direto|
|Capacity|100 TiB por ação|100 TiB por volume, até 12,5 PiB por assinatura|Máximo de 32 TiB por disco|
|Infra-estrutura necessária|Tamanho mínimo de ação 1 GiB|Piscina de capacidade mínima 4 TiB, tamanho do volume min 100 GiB|Duas VMs no Azure IaaS (+ Cloud Witness) ou pelo menos três VMs sem e custos para discos|
|Protocolos|SMB 2.1/3. e REST|NFSv3, NFSv4.1 (visualização), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Detalhes da gestão do Azure

|Recursos|Arquivos do Azure|Azure NetApp Files|Espaços de armazenamento Diretos|
|--------|-----------|------------------|---------------------|
|Acesso|Nuvem, local e híbrido (sincronização de arquivos Azure)|Nuvem, no local (via ExpressRoute)|Nuvem, no local|
|Backup|Integração de instantâneo de backup do Azure|Instantâneos de arquivos do Azure NetApp|Integração de instantâneo de backup do Azure|
|Segurança e conformidade|[Todos os certificados suportados pelo Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO concluída|[Todos os certificados suportados pelo Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integração do Microsoft Azure Active Directory|[Diretório ativo nativo e serviços de domínio de diretório ativo do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Serviços de domínio do diretório ativo do Azure e diretório ativo nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|O Native Active Directory ou o Azure Active Directory Domain Services são suportados apenas|

Depois de escolher seu método de armazenamento, confira os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) para obter informações sobre nossos planos de preços.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os contêineres de perfil do FSLogix, os discos de perfil do usuário e outras tecnologias de perfil do usuário, consulte a tabela em [contêineres de perfil FSLogix e arquivos Azure](fslogix-containers-azure-files.md).

Se você estiver pronto para criar seus próprios contêineres de perfil FSLogix, comece com um desses tutoriais:

- [Começando com os contêineres de perfil FSLogix em Arquivos Azure no Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Crie um contêiner de perfil FSLogix para um pool de hostusando arquivos do Azure NetApp](create-fslogix-profile-container.md)
- As instruções em Implantar um servidor de arquivos de escala direta de [dois nós Para armazenamento em UPD no Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) também se aplicam quando você usa um contêiner de perfil FSLogix em vez de um disco de perfil do usuário

Você também pode começar desde o início e configurar sua própria solução de Área de Trabalho Virtual do Windows no [Create a tenant in Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
