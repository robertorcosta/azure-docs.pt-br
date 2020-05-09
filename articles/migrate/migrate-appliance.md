---
title: Dispositivo de Migrações para Azure
description: Fornece uma visão geral do dispositivo de migrações para Azure usado em avaliação e migração de servidor.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 439f6d9c80a0b93f071d30d580facc4604cabbac
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780327"
---
# <a name="azure-migrate-appliance"></a>Dispositivo de Migrações para Azure

Este artigo resume os pré-requisitos e os requisitos de suporte para o dispositivo de migrações para Azure. 

## <a name="deployment-scenarios"></a>Cenários de implantação

O dispositivo migrações para Azure é usado nos cenários a seguir.

**Cenário** | **Ferramenta** | **Usado para** 
--- | --- | ---
**Avaliação de VM VMware** | Migrações para Azure: avaliação do servidor | Descobrir VMs VMware<br/><br/> Descobrir aplicativos e dependências de computador<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.
**Migração sem agente de VM VMware** | Migrações para Azure: migração de servidor | Descobrir VMs VMware <br/><br/> Replique VMs VMware com migração sem agente.
**Avaliação de VM do Hyper-V** | Migrações para Azure: avaliação do servidor | Descobrir VMs do Hyper-V<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.
**Avaliação de máquina física** |  Migrações para Azure: avaliação do servidor |  Descobrir servidores físicos (ou VMs que você trata como servidores físicos).<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O dispositivo pode ser implantado usando dois métodos:

- O dispositivo pode ser implantado usando um modelo para VMs VMware e VMs do Hyper-V (modelo OVA para VMware ou VHD para Hyper-V).
- Se você não quiser usar um modelo, poderá implantar o dispositivo para VMware ou Hyper-V usando um script do PowerShell.
- No Azure governamental, você deve implantar o dispositivo usando um script.
- Para servidores físicos, você sempre implanta o dispositivo usando um script.
- Links de download estão disponíveis nas tabelas a seguir.


## <a name="appliance---vmware"></a>Dispositivo – VMware 

A tabela a seguir resume os requisitos do dispositivo de migração do Azure para VMware.

**Requisito** | **VMware** 
--- | ---
**Permissões** | Para acessar o aplicativo Web do dispositivo localmente ou remotamente, você precisa ser um administrador de domínio ou administrador local no computador do dispositivo.
**Componentes do dispositivo** | O dispositivo tem os seguintes componentes:<br/><br/> - **Aplicativo de gerenciamento**: Este é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.<br/> - **Agente de descoberta**: o agente reúne os dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure.<br/>- **Agente de avaliação**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.<br/>- **Serviço de atualização automática**: atualiza os componentes do dispositivo (é executado a cada 24 horas).<br/>- **Agente Dra**: Orquestra a replicação de VM e coordena a comunicação entre máquinas replicadas e o Azure. Usado somente ao replicar VMs VMware para o Azure usando a migração sem agente.<br/>- **Gateway**: envia dados replicados para o Azure. Usado somente ao replicar VMs VMware para o Azure usando a migração sem agente.
**Implantação com suporte** | Implante como VM do VMware usando o modelo OVA.<br/><br/> Implante como uma VM VMware ou uma máquina física usando o script de instalação do PowerShell.
**Suporte do projeto** |  Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um único vCenter Server.
**Modelo OVA** | Baixar do portal ou do https://aka.ms/migrate/appliance/vmware.<br/><br/> O tamanho do download é 11,2 GB.<br/><br/> O modelo de dispositivo baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Script do PowerShell** | [Download](https://go.microsoft.com/fwlink/?linkid=2105112)de script.<br/><br/> 
**Software/hardware** |  O dispositivo deve ser executado no computador com o Windows Server 2016, 32-GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo requer acesso à Internet, seja diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo em uma VM VMware, precisará de recursos suficientes no vCenter Server para alocar uma VM que atenda aos requisitos.<br/><br/> Se você executar o dispositivo em um computador físico, verifique se ele está executando o Windows Server 2016 e atende aos requisitos de hardware. 
**Requisitos da VMware** | Se você implantar o dispositivo como uma VM VMware, ele deverá ser implantado em um host ESXi executando a versão 5,5 ou posterior.<br/><br/> vCenter Server executando 5,5, 6,0, 6,5 ou 6,7.
**VDDK (migração sem agente)** | Se você implantar o dispositivo como uma VM VMware e estiver executando uma migração sem agente, o VMware vSphere VDDK deverá ser instalado na VM do dispositivo.
**Valor de hash-OVA** | [Verifique](tutorial-assess-vmware.md#verify-security) os valores de hash do modelo ova.
**Valor de hash-script do PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash de script do PowerShell.




## <a name="appliance---hyper-v"></a>Dispositivo – Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Permissões** | Para acessar o aplicativo Web do dispositivo localmente ou remotamente, você precisa ser um administrador de domínio ou administrador local no computador do dispositivo.
**Componentes do dispositivo** | O dispositivo tem os seguintes componentes:<br/><br/>- **Aplicativo de gerenciamento**: Este é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.<br/> - **Agente de descoberta**: o agente reúne os dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure.<br/>- **Agente de avaliação**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.<br/>- **Serviço de atualização automática**: atualiza os componentes do dispositivo (é executado a cada 24 horas).
**Implantação com suporte** | Implante como VM Hyper-V usando um modelo VHD.<br/><br/> Implante como uma VM ou máquina física do Hyper-V usando um script de instalação do PowerShell.
**Suporte do projeto** |  Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 5000 VMs do Hyper-V.<br/> Um dispositivo pode se conectar a até 300 hosts Hyper-V.
**Modelo VHD** | Pasta compactada, incluindo VHD. Baixar do portal ou do https://aka.ms/migrate/appliance/hyperv.<br/><br/> O tamanho do download é 10 GB.<br/><br/> O modelo de dispositivo baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Script do PowerShell** | [Download](https://go.microsoft.com/fwlink/?linkid=2105112)de script.<br/><br/> 
**Software/hardware***   |  O dispositivo deve ser executado no computador com o Windows Server 2016, 32-GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, seja diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo como uma VM do Hyper-V, precisará de recursos suficientes no host do Hyper-V para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo para a VM do dispositivo.<br/><br/> Se você executar o dispositivo em um computador físico, verifique se ele está executando o Windows Server 2016 e atende aos requisitos de hardware. 
**Requisitos do Hyper-V** | Se você implantar o dispositivo com o modelo VHD, a VM do dispositivo fornecida pela migração do Azure será a VM Hyper-V versão 5,0.<br/><br/> O host Hyper-V deve estar executando o Windows Server 2012 R2 ou posterior. 
**Valor de hash-VHD** | [Verifique](tutorial-assess-hyper-v.md#verify-security) os valores de hash do modelo VHD.
**Valor de hash-script do PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash de script do PowerShell.


## <a name="appliance---physical"></a>Dispositivo-físico

**Requisito** | **Físico** 
--- | ---
**Permissões** | Para acessar o aplicativo Web do dispositivo localmente ou remotamente, você precisa ser um administrador de domínio ou administrador local no computador do dispositivo.
**Componentes do dispositivo** | O dispositivo tem os seguintes componentes: <br/><br/> - **Aplicativo de gerenciamento**: Este é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.<br/> - **Agente de descoberta**: o agente reúne os dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure.<br/>- **Agente de avaliação**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.<br/>- **Serviço de atualização automática**: atualiza os componentes do dispositivo (é executado a cada 24 horas).
**Implantação com suporte** | Implante como uma máquina física dedicada ou uma VM usando um script de instalação do PowerShell. O script está disponível para download no Portal.
**Suporte do projeto** |  Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 250 servidores físicos.
**Script do PowerShell** | Baixe o script (AzureMigrateInstaller. ps1) em uma pasta compactada do Portal. [Saiba mais](tutorial-assess-physical.md#set-up-the-appliance). Como alternativa, [Baixe diretamente](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> O tamanho do download é 59,7 MB.
**Software/hardware** |  O dispositivo deve ser executado no computador com o Windows Server 2016, 32-GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, seja diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo em um computador físico, verifique se ele está executando o Windows Server 2016 e atende aos requisitos de hardware.<br/> Não há suporte para a execução do dispositivo em um computador com o Windows Server 2019.
**Valor de hash** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash de script do PowerShell.

## <a name="url-access"></a>acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure realizam uma verificação de conectividade para as URLs necessárias.
- Você precisa permitir o acesso a todas as URLs na lista. Se você estiver apenas fazendo a avaliação, poderá ignorar as URLs marcadas como necessárias somente para a migração sem agente do VMware.
-  Se você estiver usando um proxy baseado em URL para se conectar à Internet, certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*. portal.azure.com  | Navegue até o Portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Entre em sua assinatura do Azure.
*.microsoftonline.com <br/> *.microsoftonline p.com | Crie aplicativos de Azure Active Directory (AD) para que o dispositivo se comunique com as migrações para Azure.
management.azure.com | Crie aplicativos do Azure AD para o dispositivo se comunicar com o serviço migrações para Azure.
*.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
aka.ms/* | Permitir acesso a links conhecidos. Usado para atualizações do dispositivo de migração do Azure.
download.microsoft.com/download | Permitir downloads do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente do VMware**<br/><br/> Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net |  **Usado para migração sem agente do VMware**<br/><br/>Carregar dados no armazenamento para migração.

### <a name="government-cloud-urls"></a>URLs de nuvem do governo

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.us  | Navegue até o Portal do Azure.
graph.windows.net | Entre em sua assinatura do Azure.
login.microsoftonline.us  | Crie aplicativos de Azure Active Directory (AD) para que o dispositivo se comunique com as migrações para Azure.
management.usgovcloudapi.net | Crie aplicativos do Azure AD para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*. vault.usgovcloudapi.net | Gerenciar segredos no Azure Key Vault.
aka.ms/* | Permitir acesso a links conhecidos. Usado para atualizações do dispositivo de migração do Azure.
download.microsoft.com/download | Permitir downloads do download da Microsoft.
*. servicebus.usgovcloudapi.net  | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Conecte-se às URLs de serviço de migrações para Azure.
*.hypervrecoverymanager.windowsazure.us | **Usado para migração sem agente do VMware**<br/><br/> Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.usgovcloudapi.net  |  **Usado para migração sem agente do VMware**<br/><br/>Carregar dados no armazenamento para migração.
*. applicationinsights.us | Carregar logs de aplicativo usados para monitoramento interno.





## <a name="collected-data---vmware"></a>Dados coletados-VMware

O dispositivo coleta metadados, dados de desempenho e dados de análise de dependência (se a [análise de dependência](concepts-dependency-visualization.md) sem agente for usada).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo dispositivo de migrações para Azure ajudam a descobrir se os computadores e aplicativos estão prontos para migração para o Azure, para computadores e aplicativos de tamanho adequado, planos de custos e para analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados de VM do VMware que o dispositivo coleta e envia para o Azure.

**DADO** | **NEUTRALIZA**
--- | --- 
**Detalhes do computador** | 
ID da VM | vm.Config.InstanceUuid 
Nome da VM | vm.Config.Name
ID do vCenter Server | VMwareClient. Instance. UUID
Descrição da VM | vm.Summary.Config.Annotation
Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operacional | VM. SummaryConfig.GuestFullName
Tempo de inicialização | vm.Config.Firmware
Número de núcleos | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Número de discos | VM. Config. hardware. Device. ToList (). FindAll (x => é VirtualDisk). Count
Lista de tamanhos de disco | VM. Config. hardware. Device. ToList (). FindAll (x => é VirtualDisk)
Lista de adaptadores de rede | VM. Config. hardware. Device. ToList (). FindAll (x => é VirtualEthernet). Count
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.average
**Detalhes de por disco** | 
Valor de chave do disco | disk.Key
Número de Dikunit | disk.UnitNumber
Valor de chave de controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk.DeviceInfo.Summary
Nome do disco | Valor gerado usando disco. UnitNumber, disco. Chave, disco. ControllerKey. VAlue
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Taxa de transferência de leitura (MB por segundo) | virtualDisk.read.average
Taxa de transferência de gravação (MB por segundo) | virtualDisk.write.average
**Por detalhes da NIC** | 
Nome do adaptador de rede | nic.Key
Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Taxa de transferência de leitura (MB por segundo) | net.received.average
Taxa de transferência de gravação (MB por segundo) | net.transmitted.average
**Detalhes do caminho de inventário** | 
Nome | container.GetType().Name
Tipo de objeto filho | container.ChildType
Detalhes de referência | container.MoRef
Detalhes do pai | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)container).VmFolder
Detalhes do datacenter por pasta de host | ((Datacenter)container).HostFolder
Detalhes do cluster por host | (Contêiner (ClusterComputeResource)). Hospedeira
Detalhes do host por VM | (Contêiner (HostSystem)). VM

### <a name="performance-data"></a>Dados de desempenho


Aqui estão os dados de desempenho da VM VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Neutraliza** | **Impacto de avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo da VM recomendado
Utilização da memória | mem.usage.average | Tamanho/custo da VM recomendado
Taxa de transferência de leitura de disco (MB por segundo) | virtualDisk.read.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de gravações de disco (MB por segundo) | virtualDisk.write.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Operações de leitura de disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Operações de gravações de disco por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de leitura de NIC (MB por segundo) | net.received.average | Cálculo para o tamanho da VM
Taxa de transferência de gravações da NIC (MB por segundo) | net.transmitted.average  |Cálculo para o tamanho da VM

### <a name="app-dependencies-metadata"></a>Metadados de dependências de aplicativo

A análise de dependência sem agente coleta dados de conexão e de processo.

#### <a name="connection-data"></a>Dados de conexão

Aqui estão os dados de conexão que o dispositivo coleta de cada VM habilitada para análise de dependência sem agente. Esses dados são enviados para o Azure.

**Dados** | **Comando usado** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado da conexão TCP | netstat
ID do Processo | netstat
Não. de conexões ativas | netstat

#### <a name="process-data"></a>Processar dados
Aqui estão os dados de processo que o dispositivo coleta de cada VM habilitada para análise de dependência sem agente. Esses dados são enviados para o Azure.

**Dados** | **classe WMI** | **Propriedade de classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | ExecutablePath
Argumentos de processo | Win32_Process | CommandLine
Nome do aplicativo | Win32_Process | Parâmetro VersionInfo. ProductName da Propriedade ExecutablePath

#### <a name="linux-vm-data"></a>Dados da VM do Linux

Aqui estão os dados de conexão e de processo que o dispositivo coleta de cada VM Linux habilitada para análise de dependência sem agente. Esses dados são enviados para o Azure.

**Dados** | **Comando usado** 
--- | ---
Porta local | netstat 
Endereço IP local | netstat 
Porta remota | netstat 
Endereço IP remoto | netstat 
Estado da conexão TCP | netstat 
Não. de conexões ativas | netstat
ID do Processo  | netstat 
Nome do processo | ps
Argumentos de processo | ps
Nome do aplicativo | dpkg ou RPM



## <a name="collected-data---hyper-v"></a>Dados coletados-Hyper-V

O dispositivo coleta metadados, dados de desempenho e dados de análise de dependência (se a [análise de dependência](concepts-dependency-visualization.md) sem agente for usada).

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo dispositivo de migrações para Azure ajudam a descobrir se os computadores e aplicativos estão prontos para migração para o Azure, para computadores e aplicativos de tamanho adequado, planos de custos e para analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados de VM do Hyper-V que o dispositivo coleta e envia para o Azure.

**DADO** | **CLASSE WMI** | **PROPRIEDADE DE CLASSE WMI**
--- | --- | ---
**Detalhes do computador** | 
Número de série de BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo de VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição da VM | Msvm_VirtualSystemSettingData | ElementName
Versão da VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica habilitada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão/FQDN do sistema operacional | Msvm_KvpExchangeComponent | Dados do nome do GuestIntrinsicExchangeItems
Status de energia da VM | Msvm_ComputerSystem | EnabledState
**Detalhes de por disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Type
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Pai do disco rígido virtual | Msvm_VirtualHardDiskSettingData | ParentPath
**Por detalhes da NIC** | 
Endereços IP (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP habilitado (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID da NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço MAC NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID da NIC (NICs herdadas) | Dados do MsvmEmulatedEthernetPortSetting | InstanceID
ID de NIC MAC (NICs herdadas) | Dados do MsvmEmulatedEthernetPortSetting | Endereço

### <a name="performance-data"></a>Dados de desempenho

Aqui estão os dados de desempenho do Hyper VM que o dispositivo coleta e envia para o Azure.

**Classe de contador de desempenho** | **Neutraliza** | **Impacto de avaliação**
--- | --- | ---
Processador virtual do hipervisor do Hyper-V | % De tempo de execução de convidado | Tamanho/custo da VM recomendado
VM Memória Dinâmica Hyper-V | Pressão atual (%)<br/> Memória física visível de convidado (MB) | Tamanho/custo da VM recomendado
Dispositivo de armazenamento virtual do Hyper-V | Bytes de leitura/segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Dispositivo de armazenamento virtual do Hyper-V | Bytes de gravação/segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Adaptador de Rede Virtual Hyper-V | Bytes recebidos/segundo | Cálculo para o tamanho da VM
Adaptador de Rede Virtual Hyper-V | Bytes enviados/segundo | Cálculo para o tamanho da VM

- A utilização da CPU é a soma de todo o uso, para todos os processadores virtuais anexados a uma VM.
- A utilização de memória é (pressão atual * memória física visível de convidado)/100.
- Os valores de utilização de disco e rede são coletados dos contadores de desempenho do Hyper-V listados.


## <a name="collected-data---physical"></a>Dados coletados-físicos

O dispositivo coleta metadados, dados de desempenho e dados de análise de dependência (se a [análise de dependência](concepts-dependency-visualization.md) sem agente for usada).

### <a name="windows-metadata"></a>Metadados do Windows

Os metadados descobertos pelo dispositivo de migrações para Azure ajudam a descobrir se os computadores e aplicativos estão prontos para migração para o Azure, para computadores e aplicativos de tamanho adequado, planos de custos e para analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados do Windows Server que o dispositivo coleta e envia para o Azure.

**DADO** | **CLASSE WMI** | **PROPRIEDADE DE CLASSE WMI**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domínio, nome, PartOfDomain
Contagem de núcleos do processador | Win32_PRocessor | NumberOfCores
Memória alocada | Win32_ComputerSystem | TotalPhysicalMemory
Número de série do BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
GUID DO BIOS | Win32_ComputerSystemProduct | UUID
Tempo de inicialização | Win32_DiskPartition | Verifique se há partição com tipo = **GPT: System** para efi/BIOS
Nome do SO | Win32_OperatingSystem | Legenda
Versão do SO |Win32_OperatingSystem | Versão
Arquitetura do so | Win32_OperatingSystem | OSArchitecture
Contagem de discos | Win32_DiskDrive | Modelo, tamanho, DeviceID, MediaType, nome
Tamanho do disco | Win32_DiskDrive | Tamanho
Lista de NICs | Win32_NetworkAdapterConfiguration | Descrição, índice
Endereço IP da NIC | Win32_NetworkAdapterConfiguration | IPAddress
Endereço MAC da NIC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Metadados do Linux

Aqui está a lista completa de metadados do servidor Linux que o dispositivo coleta e envia para o Azure.

**DADO** | **LINUX** 
--- | --- 
FQDN | cat/proc/sys/kernel/hostname, hostname-f
Contagem de núcleos do processador |  /proc/cpuinfo \| awk '/^ processador/{Print $3} ' \| wc-l
Memória alocada | cat/proc/meminfo \| grep MemTotal \| awk ' {printf "%. 0f", $2/1024} '
Número de série do BIOS | lshw \| grep "serial:" \| cabeça-N1 \| awk ' {print $2} ' <br/> /usr/sbin/dmidecode-t 1 \| grep "serial" \| awk "{$1 =" "; $2 =" "; Print}"
GUID DO BIOS | Cat/sys/Class/DMI/ID/product_uuid
Tempo de inicialização | [-d/sys/firmware/EFI]  && Echo EFI \| \| Echo BIOS
Nome/versão do so | Acessamos esses arquivos para a versão e o nome do sistema operacional:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> debian_version/etc/
Arquitetura do so | Uname-m
Contagem de discos | fdisk-l \| egrep "Disk. * bytes" \| awk "{Print $2}" \| recortar-F1-d ":"
Disco de inicialização | DF/boot \| sed-n 2P \| awk ' {Print $1} '
Tamanho do disco | fdisk-l \| egrep "Disk. * bytes" \| egrep $Disk: \| awk "{Print $5}"
Lista de NICs | IP-o-4 addr mostra \| awk ' {print $2} '
Endereço IP da NIC | Endereço IP mostrar $nic \| grep inet \| awk ' {print $2} ' \| recortar-F1-d "/" 
Endereço MAC da NIC | Endereço IP mostrar $nic \| grep ether \| awk ' {print $2} '

### <a name="windows-performance-data"></a>Dados de desempenho do Windows

Aqui estão os dados de desempenho do Windows Server que o dispositivo coleta e envia para o Azure.

**Dados** | **classe WMI** | **Propriedade de classe WMI**
--- | --- | ---
Uso da CPU | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Uso de memória | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Contagem de NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface | Obter a contagem de dispositivos de rede.
Dados recebidos por NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dados transmitidos por NIC | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Contagem de discos | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Contagem de discos
Detalhes do disco | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dados de desempenho do Linux

Aqui estão os dados de desempenho do servidor Linux que o dispositivo coleta e envia para o Azure.

**Dados** | **Linux** 
--- | --- 
Uso da CPU | cat/proc/stat/| /proc/stat ' CPU ' grep
Uso de memória | com \| grep + \| awk livre ' {print $3/$ 2 * 100,0} '
Contagem de NIC | grep-lshw de \| rede de classe cruzada ETH [ \| 0-60] wc-l
Dados recebidos por NIC | Cat/sys/Class/net/ETH $ NIC/Statistics/rx_bytes
Dados transmitidos por NIC | Cat/sys/Class/net/ETH $ NIC/Statistics/tx_bytes
Contagem de discos | fdisk-l \| egrep "Disk. * bytes" \| awk "{Print $2}" \| recortar-F1-d ":"
Detalhes do disco | cat/proc/DiskStats


## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado conforme os agentes de migração do Azure em execução no dispositivo são atualizados. Isso ocorre automaticamente, pois a atualização automática está habilitada no dispositivo por padrão. Você pode alterar essa configuração padrão para atualizar os serviços de dispositivo manualmente.

### <a name="turn-off-auto-update"></a>Desativar atualização automática

1. No computador que executa o dispositivo, abra o editor do registro.
2. Navegue até **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**.
3. Para desativar a atualização automática, crie uma chave de **atualização** de chave de registro com o valor DWORD de 0.

    ![Definir chave do registro](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Ativar atualização automática

Você pode ativar a atualização automática usando um destes métodos:

- Excluindo a chave do registro de atualização automática de HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance.
- Após a conclusão da descoberta, no Configuration Manager do dispositivo.

Para excluir a chave do registro:

1. No computador que executa o dispositivo, abra o editor do registro.
2. Navegue até **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**.
3. Exclua a chave do registro **AutoUpdate**, que foi criada anteriormente para desativar a atualização automática.

Para ativar o dispositivo Configuration Manager, após a conclusão da descoberta:

1. No computador do dispositivo, abra o dispositivo Configuration Manager.
2. Em **serviço** > **de dispositivo a atualização automática de componentes de migração do Azure está desativada**, clique para ativar a atualização automática.

    ![Ativar atualizações automáticas](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>Verificar a versão dos serviços de dispositivo

Você pode verificar a versão dos serviços de dispositivo usando um destes métodos:

- Em Configuration Manager do dispositivo, após a conclusão da descoberta.
- No computador do dispositivo, nos**programas e recursos**do **painel** > de controle.

Para fazer check-in no Configuration Manager do dispositivo:

1. Após a conclusão da descoberta, abra o dispositivo Configuration Manager (no aplicativo Web do dispositivo).
2. Em **serviços de dispositivo**, verifique as versões dos serviços de dispositivo.

    ![Verificar a versão](./media/migrate-appliance/version.png)

Para fazer check-in no painel de controle:

1. No dispositivo, clique em **Iniciar** > **painel** > **de controle programas e recursos**
2. Verifique as versões dos serviços de dispositivo na lista.

    ![Verificar a versão no painel de controle](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Atualizar manualmente uma versão mais antiga

Se você estiver executando uma versão mais antiga para qualquer um dos componentes, deverá desinstalar o serviço e atualizar manualmente para a versão mais recente.

1. Para verificar as versões mais recentes do serviço de dispositivo, [Baixe](https://aka.ms/latestapplianceservices) o arquivo LatestComponents. JSON.
2.  Após o download, abra o arquivo LatestComponents. JSON no bloco de notas.
3. Localize a versão de serviço mais recente no arquivo e o link de download para ela. Por exemplo: 

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.  Baixe a versão mais recente de um serviço desatualizado, usando o link de download no arquivo.
5. Após o download, execute o comando a seguir em uma janela de comando do administrador para verificar a integridade do MSI baixado.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```Por exemplo: C:\>certutil-hashfile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.msi MD5

5. Verifique se a saída do comando corresponde à entrada de valor de hash para o serviço no arquivo (por exemplo, o valor de hash MD5 acima).
6. Agora, execute o MSI para instalar o serviço. É uma instalação silenciosa e a janela de instalação é fechada após a conclusão.
7. Após a conclusão da instalação, verifique a versão do serviço em **Control panel** > **programas e recursos**do painel de controle. A versão do serviço agora deve ser atualizada para a mais recente mostrada no arquivo JSON.



## <a name="next-steps"></a>Próximas etapas

- [Saiba como](how-to-set-up-appliance-vmware.md) configurar o dispositivo para VMware.
- [Saiba como](how-to-set-up-appliance-hyper-v.md) configurar o dispositivo para o Hyper-V.
- [Saiba como](how-to-set-up-appliance-physical.md) configurar o dispositivo para servidores físicos.

