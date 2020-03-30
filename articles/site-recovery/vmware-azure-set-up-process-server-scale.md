---
title: Configure um servidor de processo de escala durante a recuperação de desastres de VMware VMs e servidores físicos com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar o servidor de processo de escala durante a recuperação de desastres de VMware VMs e servidores físicos.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257141"
---
# <a name="scale-with-additional-process-servers"></a>Escala com servidores de processo adicionais

Por padrão, quando você estiver replicando VMs do VMware ou servidores físicos no Azure usando o [Site Recovery](site-recovery-overview.md), um servidor de processo será instalado no computador do servidor de configuração e usado para coordenar a transferência de dados entre o Site Recovery e sua infraestrutura local. Para aumentar a capacidade e escalar horizontalmente a implantação de replicação, você pode adicionar outros servidores de processo autônomos. Este artigo descreve como configurar um servidor de processo de escala.

## <a name="before-you-start"></a>Antes de começar

### <a name="capacity-planning"></a>planejamento de capacidade

Verifique se você realizou o [planejamento de capacidade](site-recovery-plan-capacity-vmware.md) para replicação de VMware. Isso ajuda a identificar como e quando você deve implantar servidores de processo adicionais.

A partir da versão 9.24, a orientação é adicionada durante a seleção do servidor de processo para novas replicações. O servidor de processo será marcado como Saudável, Aviso e Crítico com base em determinados critérios. Para entender diferentes cenários que podem influenciar o estado do servidor de processo, revise os [alertas](vmware-physical-azure-monitor-process-server.md#process-server-alerts)do servidor de processo .

> [!NOTE]
> Não há suporte para o uso de um componente do Servidor de Processo clonado. Siga as etapas deste artigo para cada expansão de PS.

### <a name="sizing-requirements"></a>Requisitos de dimensionamento 

Verifique os requisitos de dimensionamento resumidos na tabela. Em geral, se for necessário escalar horizontalmente sua implantação para além de 200 computadores de origem ou se você tiver uma taxa de rotatividade diária total acima de 2 TB, serão necessários servidores de processo adicionais para lidar com o volume do tráfego.

| **Servidor de processo adicional** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- |
|4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória |300 GB |250 GB ou menos |Replique 85 computadores ou menos. |
|8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória |600 GB |250 GB a 1 TB |Replique entre 85 e 150 computadores. |
|12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz), 24 GB de memória |1 TB |1 TB a 2 TB |Replique entre 150 e 225 computadores. |

Onde cada computador de origem protegido é configurado com três discos de 100 GB cada.

### <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para o servidor de processo adicional são resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Baixar o arquivo de instalação

Baixe o arquivo de instalação para o servidor de processo da seguinte maneira:

1. Faça login no portal Azure e navegue até o cofre dos serviços de recuperação.
2. **Infra-estrutura** > de recuperação de local aberto**VMWare e servidores de** > configuração de**máquinas físicas** (em Para VMware & Máquinas Físicas).
3. Selecione o servidor de configuração para fazer drill down nos detalhes do servidor. Clique em **+ Servidor de processo**.
4. Em **Adicionar servidor de processo** >  Escolha onde deseja implantar seu servidor de**processo,** **selecione Implantar um servidor de processo de saída de escala no local**.

   ![Página Adicionar Servidores](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Clique em **Baixar a Configuração Unificada do Microsoft Azure Site Recovery**. A versão mais recente do arquivo de instalação será baixada.

   > [!WARNING]
   > A versão de instalação do servidor de processo deve ser a mesma ou anterior à versão do servidor de configuração executado. Uma maneira simples de garantir a compatibilidade de versão é usar o mesmo instalador usado recentemente para instalar ou atualizar o servidor de configuração.

## <a name="install-from-the-ui"></a>Instalar da interface do usuário

Instale da seguinte maneira. Depois de configurar o servidor, você migrará os computadores de origem para usá-lo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalar usando a linha de comando

Instale o executando o seguinte comando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Em que os parâmetros de linha de comando são os seguintes:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Por exemplo: 

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Criar um arquivo de configurações de proxy

Se for necessário configurar um proxy, o parâmetro ProxySettingsFilePath utilizará um arquivo como entrada. É possível criar o arquivo da seguinte maneira, bem como passá-lo como parâmetro de entrada ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [gerenciar as configurações do servidor de processo](vmware-azure-manage-process-server.md)
