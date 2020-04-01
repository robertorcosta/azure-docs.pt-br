---
title: Dispositivo de Migrações para Azure
description: Fornece uma visão geral do aparelho Azure Migrate usado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: bccf4738d46b65f2d149eafc8e69591141d7d073
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437581"
---
# <a name="azure-migrate-appliance"></a>Dispositivo de Migrações para Azure

Este artigo resume os pré-requisitos e os requisitos de suporte para o aparelho Azure Migrate. 

## <a name="deployment-scenarios"></a>Cenários de implantação

O aparelho Azure Migrate é usado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Usado para** 
--- | --- | ---
**Avaliação vMware VM** | Migração do Azure:Avaliação do servidor | Descubra VMs VMware<br/><br/> Descubra aplicativos e dependências de máquinas<br/><br/> Coletar metadados da máquina e metadados de desempenho para avaliações.
**VMware VM migração sem agente** | Migração do Azure:Migração do servidor | Descubra VMs VMware <br/><br/> Replique VMware VMs com migração sem agente.
**Avaliação de HiperV VM** | Migração do Azure:Avaliação do servidor | Descubra Hiper-VVs<br/><br/> Coletar metadados da máquina e metadados de desempenho para avaliações.
**Avaliação da máquina física** |  Migração do Azure:Avaliação do servidor |  Descubra servidores físicos (ou VMs que você trata como servidores físicos).<br/><br/> Coletar metadados da máquina e metadados de desempenho para avaliações.

## <a name="appliance---vmware"></a>Aparelho - VMware 

A tabela a seguir resume os requisitos do aparelho Azure Migrate para VMware.

**Exigência** | **Vmware** 
--- | ---
**Componentes do aparelho** | O aparelho possui os seguintes componentes:<br/><br/> - **Aplicativo de gerenciamento**: Este é um aplicativo web para entrada do usuário durante a implantação do aparelho. Usado na avaliação de máquinas para migração para o Azure.<br/> - **Agente de descoberta**: O agente coleta dados de configuração da máquina. Usado na avaliação de máquinas para migração para o Azure.<br/>- **Agente de avaliação**: O agente coleta dados de desempenho. Usado na avaliação de máquinas para migração para o Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).<br/>- **Agente DRA**: Orquestra a replicação da VM e coordena a comunicação entre máquinas replicadas e o Azure. Usado apenas ao replicar VMs VMware para o Azure usando migração sem agente.<br/>- **Gateway**: Envia dados replicados para o Azure. Usado apenas ao replicar VMs VMware para o Azure usando migração sem agente.
**Implantação suportada** | Implante como VMware VM usando o modelo OVA.<br/><br/> Implante como um VMware VM ou máquina física usando o script de instalação PowerShell.
**Suporte ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um aparelho pode se conectar a um único servidor vCenter.
**Modelo ova** | Baixe do portal https://aka.ms/migrate/appliance/vmwareou de .<br/><br/> O tamanho do download é de 11,2 GB.<br/><br/> O modelo do aparelho baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo do vencimento, recomendamos que você baixe e implante um novo aparelho, ou que você ative a licença do sistema operacional do aparelho VM.
**Script PowerShell** | Download de [script](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware** |  O aparelho deve funcionar em máquina com Windows Server 2016, MEMÓRIA RAM de 32 GB, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um switch virtual externo.<br/> O aparelho requer acesso à internet, diretamente ou através de um proxy.<br/><br/> Se você executar o aparelho em uma VMware VM, você precisará de recursos suficientes no vCenter Server para alocar uma VM que atenda aos requisitos.<br/><br/> Se você executar o aparelho em uma máquina física, certifique-se de que ele está executando o Windows Server 2016 e atenda aos requisitos de hardware. 
**Requisitos da VMware** | Se você implantar o aparelho como uma VMware VMware, ele deve ser implantado em um host ESXi em execução versão 5.5 ou posterior.<br/><br/> vCenter Server executando 5.5, 6.0, 6.5 ou 6.7.
**VDDK (migração sem agente)** | Se você implantar o aparelho como uma VMware VMm e estiver executando uma migração sem agente, o VMware vSphere VDDK deve ser instalado na VM do aparelho.
**Hash value-OVA** | [Verifique](tutorial-assess-vmware.md#verify-security) os valores de hash do modelo OVA.
**Hash value-PowerShell script** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.




## <a name="appliance---hyper-v"></a>Aparelho - Hiper-V

**Exigência** | **Hyper-V** 
--- | ---
**Componentes do aparelho** | O aparelho possui os seguintes componentes:<br/><br/>- **Aplicativo de gerenciamento**: Este é um aplicativo web para entrada do usuário durante a implantação do aparelho. Usado na avaliação de máquinas para migração para o Azure.<br/> - **Agente de descoberta**: O agente coleta dados de configuração da máquina. Usado na avaliação de máquinas para migração para o Azure.<br/>- **Agente de avaliação**: O agente coleta dados de desempenho. Usado na avaliação de máquinas para migração para o Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação suportada** | Implante como Hyper-V VM usando um modelo VHD.<br/><br/> Implante como um VM Hiper-V ou máquina física usando um script de instalação PowerShell.
**Suporte ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 5000 VMs Hiper-V.<br/> Um aparelho pode se conectar a até 300 hosts Hyper-V.
**Modelo VHD** | Pasta com zíper, incluindo VHD. Baixe do portal https://aka.ms/migrate/appliance/hypervou de .<br/><br/> O tamanho do download é de 10 GB.<br/><br/> O modelo do aparelho baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo do vencimento, recomendamos que você baixe e implante um novo aparelho, ou que você ative a licença do sistema operacional do aparelho VM.
**Script PowerShell** | Download de [script](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware***   |  O aparelho deve funcionar em máquina com Windows Server 2016, MEMÓRIA RAM de 32 GB, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um switch virtual externo.<br/> O aparelho precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou através de um proxy.<br/><br/> Se você executar o aparelho como um Hyper-V VM, você precisa de recursos suficientes no host Hyper-V para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um interruptor externo para o aparelho VM.<br/><br/> Se você executar o aparelho em uma máquina física, certifique-se de que ele está executando o Windows Server 2016 e atenda aos requisitos de hardware. 
**Requisitos de hiper-V** | Se você implantar o aparelho com o modelo VHD, o aparelho VM fornecido pelo Azure Migrate é a versão 5.0 do Hyper-V VM.<br/><br/> O host Hyper-V deve estar executando o Windows Server 2012 R2 ou posterior. 
**Valor hash -VHD** | [Verifique](tutorial-assess-hyper-v.md#verify-security) os valores de hash do modelo VHD.
**Hash value-PowerShell script** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.


## <a name="appliance---physical"></a>Aparelho - Físico

**Exigência** | **Físico** 
--- | ---
**Componentes do aparelho** | O aparelho possui os seguintes componentes: <br/><br/> - **Aplicativo de gerenciamento**: Este é um aplicativo web para entrada do usuário durante a implantação do aparelho. Usado na avaliação de máquinas para migração para o Azure.<br/> - **Agente de descoberta**: O agente coleta dados de configuração da máquina. Usado na avaliação de máquinas para migração para o Azure.<br/>- **Agente de avaliação**: O agente coleta dados de desempenho. Usado na avaliação de máquinas para migração para o Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação suportada** | Implante como uma máquina física dedicada, ou uma VM, usando um script de instalação PowerShell.
**Suporte ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 250 servidores físicos.
**Script PowerShell** | Baixe o script (AzureMigrateInstaller.ps1) em uma pasta fechada do portal. [Saiba mais](tutorial-assess-physical.md#set-up-the-appliance). Alternativamente, [baixe diretamente](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> O tamanho do download é de 59,7 MB.
**Software/hardware** |  O aparelho deve funcionar em máquina com Windows Server 2016, MEMÓRIA RAM de 32 GB, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um switch virtual externo.<br/> O aparelho precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou através de um proxy.<br/><br/> Se você executar o aparelho em uma máquina física, certifique-se de que ele está executando o Windows Server 2016 e atenda aos requisitos de hardware. 
**Valor de hash** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.

## <a name="url-access"></a>acesso à URL

O aparelho Azure Migrate precisa de conectividade com a internet.

- Quando você implanta o aparelho, o Azure Migrate faz uma verificação de conectividade para as URLs resumidas na tabela abaixo.
- Se você estiver usando um proxy baseado em URL para se conectar à Internet, você precisa permitir o acesso a essas URLs, certificando-se de que o proxy resolva quaisquer registros CNAME recebidos ao procurar as URLs.

**URL** | **Detalhes**  
--- | --- |
*. portal.azure.com  | Navegue até o Portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Entre em sua assinatura do Azure.
*.microsoftonline.com <br/> *.microsoftonline p.com | Crie aplicativos Azure Active Directory (AD) para que o aparelho se comunique com o Azure Migrate.
management.azure.com | Crie aplicativos Azure AD para que o aparelho se comunique com o serviço Azure Migrate.
dc.services.visualstudio.com | Faça upload de registros de aplicativos usados para monitoramento interno.
*.vault.azure.net | Gerencie segredos no Cofre de Chaves Do Azure.
aka.ms/* | Permitir acesso a links também conhecidos. Usado para atualizações de aparelhos Do Azure Migrate.
download.microsoft.com/download | Permitir downloads a partir do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Conecte-se às URLs de serviço do Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente VMware**<br/><br/> Conecte-se às URLs de serviço do Azure Migrate.
*.blob.core.windows.net |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.




## <a name="collected-data---vmware"></a>Dados coletados - VMware

O aparelho coleta metadados, dados de desempenho e dados de análise de dependência (se a [análise de dependência](concepts-dependency-visualization.md) sem agente for usada).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo aparelho Azure Migrate ajudam você a descobrir se máquinas e aplicativos estão prontos para migração para o Azure, máquinas e aplicativos de tamanho certo, custos de planos e análise de dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados VMware VMque que o aparelho coleta e envia para o Azure.

**Dados** | **Counter**
--- | --- 
**Detalhes da máquina** | 
ID da VM | vm.Config.InstanceUuid 
Nome da VM | vm.Config.Name
ID do vCenter Server | VMwareClient.Instance.Uuid
Descrição da VM | vm.Summary.Config.Annotation
Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operacional | Vm. ResumoConfig.GuestFullName
Tempo de inicialização | vm.Config.Firmware
Número de núcleos | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Número de discos | Vm. config.hardware.device.tolist(). FindAll(x => é VirtualDisk).contagem
Lista de tamanhos de disco | Vm. config.hardware.device.tolist(). FindAll(x => é VirtualDisk)
Lista de adaptadores de rede | Vm. config.hardware.device.tolist(). FindAll(x => é VirtualEthernet).contagem
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.average
**Por detalhes de disco** | 
Valor de chave do disco | disk.Key
Número de Dikunit | disk.UnitNumber
Valor de chave de controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk.DeviceInfo.Summary
Nome do disco | Valor gerado usando disco. UnitNumber, disco. Chave, disco. ControllerKey.VAlue
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Leitura de throughput (MB por segundo) | virtualDisk.read.average
Gravar throughput (MB por segundo) | virtualDisk.write.average
**Por detalhes da NIC** | 
Nome do adaptador de rede | nic.Key
Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Leitura de throughput (MB por segundo) | net.received.average
Gravar throughput (MB por segundo) | net.transmitted.average
**Detalhes do caminho do inventário** | 
Nome | container.GetType().Name
Tipo de objeto filho | container.ChildType
Detalhes de referência | container.MoRef
Detalhes do pai | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)container).VmFolder
Detalhes do datacenter por pasta host | ((Datacenter)container).HostFolder
Detalhes de cluster por host | ((ClusterComputeResource)contêiner). Host
Detalhes do host por VM | ((HostSystem)contêiner). Vm

### <a name="performance-data"></a>Dados de desempenho


Aqui estão os dados de desempenho da VMware VMque que o aparelho coleta e envia para o Azure.

**Dados** | **Counter** | **Impacto de avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo/custo/vm recomendado
Utilização da memória | mem.usage.average | Tamanho/custo/custo/vm recomendado
Throughput de leitura de disco (MB por segundo) | virtualDisk.read.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho de VM
Throughput de gravações de disco (MB por segundo) | virtualDisk.write.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho de VM
Operações de leitura de disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho de VM
O disco grava operações por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo para tamanho do disco, custo de armazenamento, tamanho de VM
Throughput de leitura de NIC (MB por segundo) | net.received.average | Cálculo para tamanho de VM
NIC grava throughput (MB por segundo) | net.transmitted.average  |Cálculo para tamanho de VM

### <a name="app-dependencies-metadata"></a>Metadados de dependências de aplicativos

A análise de dependência sem agente coleta dados de conexão e processo.

#### <a name="connection-data"></a>Dados de conexão

Aqui estão os dados de conexão que o aparelho coleta de cada VM habilitado para análise de dependência sem agente. Esses dados são enviados ao Azure.

**Dados** | **Comando usado** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado de conexão TCP | netstat
ID do Processo | netstat
Não. de conexões ativas | netstat

#### <a name="process-data"></a>Processar dados
Aqui estão os dados do processo que o aparelho coleta de cada VM habilitado para análise de dependência sem agente. Esses dados são enviados ao Azure.

**Dados** | **classe WMI** | **Propriedade da classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | Executablepath
Argumentos de processo | Win32_Process | CommandLine
Nome do aplicativo | Win32_Process | Parâmetro VersionInfo.ProductName da propriedade ExecutablePath

#### <a name="linux-vm-data"></a>Dados do Linux VM

Aqui estão os dados de conexão e processo que o aparelho coleta de cada VM Linux habilitado para análise de dependência sem agente. Esses dados são enviados ao Azure.

**Dados** | **Comando usado** 
--- | ---
Porta local | netstat 
Endereço IP local | netstat 
Porta remota | netstat 
Endereço IP remoto | netstat 
Estado de conexão TCP | netstat 
Não. de conexões ativas | netstat
ID do Processo  | netstat 
Nome do processo | ps
Argumentos de processo | ps
Nome do aplicativo | dpkg ou rpm



## <a name="collected-data---hyper-v"></a>Dados coletados - Hyper-V

O aparelho coleta metadados, dados de desempenho e dados de análise de dependência (se a [análise de dependência](concepts-dependency-visualization.md) sem agente for usada).

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo aparelho Azure Migrate ajudam você a descobrir se máquinas e aplicativos estão prontos para migração para o Azure, máquinas e aplicativos de tamanho certo, custos de planos e análise de dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados Hyper-V VM que o aparelho coleta e envia para o Azure.

**Dados* | **Classe WMI** | **Propriedade da Classe WMI**
--- | --- | ---
**Detalhes da máquina** | 
Número de série de BIOS _ Msvm_BIOSElement | NÚMERO DE SÉRIE DO BIOSSerial
Tipo VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubtype
Nome de exibição de VM | Msvm_VirtualSystemSettingData | ElementName
Versão VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica ativada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome do sistema operacional/versão/FQDN | Msvm_KvpExchangeComponent | Dados de nome do GuestIntrinsicExchangeItems
Status de energia vm | Msvm_ComputerSystem | EnabledState
**Por detalhes de disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Type
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalsize
Pai de disco rígido virtual | Msvm_VirtualHardDiskSettingData | PaiPath
**Por detalhes da NIC** | 
Endereços IP (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | Ipaddresses
Habilitado para DHCP (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Nic ID (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço NIC MAC (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | Endereço
NIC ID (NICs legados) | MsvmEmdEthernetPortSetting data | InstanceID
NIC MAC ID (NICs legados) | MsvmEmdEthernetPortSetting data | Endereço

### <a name="performance-data"></a>Dados de desempenho

Aqui estão os dados de desempenho do Hyper VM que o aparelho coleta e envia para o Azure.

**Classe de contador de desempenho** | **Counter** | **Impacto de avaliação**
--- | --- | ---
Processador virtual hyper-V hypervisor | % Tempo de execução dos hóspedes | Tamanho/custo/custo/vm recomendado
VM de memória dinâmica hiper-v | Pressão atual (%)<br/> Memória Física Visível do Convidado (MB) | Tamanho/custo/custo/vm recomendado
Dispositivo de armazenamento virtual hyper-V | Leia Bytes/Segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho de VM
Dispositivo de armazenamento virtual hyper-V | Escrever Bytes/Segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho de VM
Adaptador de Rede Virtual Hyper-V | Bytes recebidos/segundo | Cálculo para tamanho de VM
Adaptador de Rede Virtual Hyper-V | Bytes Enviados/Segundo | Cálculo para tamanho de VM

- A utilização da CPU é a soma de todo o uso, para todos os processadores virtuais conectados a uma VM.
- Utilização da memória é (Pressão atual * Memória Física Visível do Convidado) / 100.
- Os valores de utilização de disco e rede são coletados nos contadores de desempenho Hyper-V listados.

## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes do Azure Migrate em execução no aparelho são atualizados. Isso acontece automaticamente porque a atualização automática é ativada no aparelho por padrão. Você pode alterar essa configuração padrão para atualizar os agentes manualmente.

- **Desativar a atualização automática**: Desativar a atualização automática no registro definindo HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" tecla 0 (DWORD). Se você decidir usar atualizações manuais, é importante atualizar todos os agentes no aparelho ao mesmo tempo, usando o botão **Atualizar** para cada agente desatualizado no aparelho.
- **Atualização manual**: Para atualizações manuais, certifique-se de atualizar todos os agentes no aparelho, usando o botão **Atualizar** para cada agente desatualizado no aparelho. Você pode mudar a configuração de atualização de volta para atualizações automáticas a qualquer momento.

![Atualize automaticamente o aparelho](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Próximas etapas

- [Saiba como](how-to-set-up-appliance-vmware.md) configurar o aparelho para VMware.
- [Saiba como](how-to-set-up-appliance-hyper-v.md) configurar o aparelho para Hyper-V.
- [Saiba como](how-to-set-up-appliance-physical.md) configurar o aparelho para servidores físicos.

