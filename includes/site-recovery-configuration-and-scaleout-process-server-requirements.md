---
title: incluir arquivo
description: incluir arquivo
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234134"
---
**Requisitos do servidor de configuração e processo**


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operacional | Windows Server 2012 R2 <br> Windows Server 2016
Localidade do sistema operacional | Inglês (EN-*)
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nenhum site da Web padrão já existente <br> – Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Federal Information Processing Standards) | Não habilitar o modo FIPS
|

## <a name="network-requirements"></a>Requisitos de rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados) 
Tipo de NIC | VMXNET3 (se o servidor de configuração for uma VM VMware)
 |
**Acesso à Internet** (o servidor precisa acessar as seguintes URLs, diretamente ou via proxy):|
\*.backup.windowsazure.com | Usado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.azure.com | Usado para operações de gerenciamento de replicação e coordenação 
*.services.visualstudio.com | Usado para fins de telemetria (opcional)
time.nist.gov | Usado para verificar a sincronização de horário entre o sistema e a hora global
time.windows.com | Usado para verificar a sincronização de horário entre o sistema e a hora global
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.Live.com </li><li> https:\//Graph.Windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.Live.com </li><li> https:\//www.Microsoft.com </li></ul> | A instalação do OVF precisa de acesso a essas URLs. Eles são usados para controle de acesso e gerenciamento de identidade por Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Para concluir o download do MySQL. </br> Em algumas regiões, o download pode ser redirecionado para a URL da CDN. Verifique se a URL da CDN também está na lista de permissões, se necessário.
|

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI 6.0 R3 | [PowerCLI versão 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalado se o servidor de configuração estiver em execução em uma VM do VMware.
MySQL | MySQL deve ser instalado. Você pode instalar manualmente ou o Azure Site Recovery pode instalá-lo. (Consulte [definir configurações](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) para obter mais informações)
|

## <a name="sizing-and-capacity-requirements"></a>Requisitos de dimensionamento e capacidade

A tabela a seguir resume os requisitos de capacidade do servidor de configuração. Se você estiver replicando várias VMs VMware, examine as [considerações de planejamento de capacidade](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) e execute a [ferramenta de planejador de implantações do Azure site Recovery](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPUs** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Computadores replicados**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores
12 vCPUs<br/><br/> 2 soquetes  * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB -1 TB | 100 a 150 computadores
16 vCPUs<br/><br/> 2 soquetes  * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 a 200 computadores
|

