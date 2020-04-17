---
title: Dispositivo de replicação de Migrações para Azure
description: Saiba mais sobre o dispositivo de replicação Do Azure Migrate para migração VMWare baseada em agentes.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 85641f514fc4367f02901eb1dd394cfa204c3ec4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535206"
---
# <a name="replication-appliance"></a>Aparelho de replicação

Este artigo descreve o dispositivo de replicação usado pelo [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ferramenta ao migrar VMs, máquinas físicas e VMs de nuvem privada/pública para o Azure, usando migração baseada em agentes. 


## <a name="overview"></a>Visão geral

O dispositivo de replicação é implantado quando você configura a migração baseada em agentes de VMms ou servidores físicos. É implantado como uma única máquina no local, seja como um VMware VMou um servidor físico. Ele funciona:

- **Aparelho de replicação**: O aparelho de replicação coordena as comunicações e gerencia a replicação de dados para VMMs VMware e servidores físicos no local, replicando-se no Azure.
- **Servidor de processo**: O servidor de processo, que é instalado por padrão no aparelho de replicação, e faz o seguinte:
    - **Gateway de replicação**: Ele atua como um gateway de replicação. Ele recebe dados de replicação de máquinas habilitadas para replicação. Ele otimiza os dados de replicação com cache, compactação e criptografia e os envia para o Azure.
    - **Instalador do agente**: Realiza uma instalação push do Serviço de Mobilidade. Este serviço deve ser instalado e executado em cada máquina local que você deseja replicar para migração.

## <a name="appliance-deployment"></a>Implantação do aparelho

**Usado para** | **Detalhes**
--- |  ---
**Migração baseada em agente VMware VM** | Você baixa o modelo OVA do hub Azure Migrate e importa para o vCenter Server para criar a VM do aparelho.
**Migração baseada em agentes de máquina física** | Se você não tiver uma infra-estrutura VMware ou se não puder criar um VMware VMM usando um modelo OVA, você baixa um instalador de software do hub Azure Migrate e executa-o para configurar a máquina do aparelho.

> [!NOTE]
> Se você estiver implantando no Azure Government, use o arquivo de instalação para implantar o dispositivo de replicação.

## <a name="appliance-requirements"></a>Requisitos do aparelho

Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no hub Azure Migrate, o aparelho executa o Windows Server 2016 e cumpre com os requisitos de suporte. Se você configurar o aparelho de replicação manualmente em um servidor físico, certifique-se de que ele está em conformidade com os requisitos.

**Componente** | **Requisito**
--- | ---
 | **Aparelho VMware VM**
PowerCLI | [A versão 6.0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o aparelho de replicação estiver em execução em uma VMware VMware.
Tipo de NIC | VMXNET3 (se o aparelho for um VMware VMware)
 | **Configurações de hardware**
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | Três: o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção.
Espaço livre em disco (cache) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
**Configurações do software** |
Sistema operacional | Windows Server 2016 ou Windows Server 2012 R2
Licença | O aparelho vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias.<br/><br/> Se o período de avaliação estiver próximo do vencimento, recomendamos que você baixe e implante um novo aparelho, ou que você ative a licença do sistema operacional do aparelho VM.
Localidade do sistema operacional | Inglês (en-us)
TLS | O TLS 1.2 deve ser ativado.
.NET Framework | .NET Framework 4.6 ou posterior deve ser instalado na máquina (com criptografia forte ativada.
MySQL | O MySQL deve ser instalado no aparelho.<br/> MySQL deve ser instalado. Você pode instalá-lo manualmente, ou a Recuperação do Site pode instalá-lo durante a implantação do aparelho.
Outros aplicativos | Não execute outros aplicativos no aparelho de replicação.
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nenhum site da Web padrão já existente <br> – Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Configurações de rede** |
Tipo de endereço IP | Estático
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados)
Tipo de NIC | VMXNET3

## <a name="mysql-installation"></a>Instalação do MySQL 

O MySQL deve ser instalado na máquina do aparelho de replicação. Pode ser instalado usando um desses métodos.

**Método** | **Detalhes**
--- | ---
Baixe e instale manualmente | Baixe o aplicativo MySQL & colocá-lo na pasta C:\Temp\ASRConfiguração e, em seguida, instale manualmente.<br/> Quando estiver configurado, o aparelho MySQL aparecerá como já está instalado.
Sem download online | Coloque o aplicativo instalador MySQL na pasta C:\Temp\ASRSetup. Quando você instalar o aparelho e clicar para baixar e instalar o MySQL, a configuração usará o instalador que você adicionou.
Baixe e instale no Azure Migrate | Ao instalar o aparelho e for solicitado para mysql, selecione **Baixar e instalar**.

## <a name="url-access"></a>acesso à URL

O aparelho de replicação precisa ter acesso a essas URLs na nuvem pública do Azure.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Usado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.azure.com | Usado para operações de gerenciamento de replicação e coordenação
*.services.visualstudio.com | Usado para fins de telemetria (é opcional)
time.windows.com | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A configuração do aparelho precisa ter acesso a esses URLs. Elas são usadas pelo Azure Active Directory para o gerenciamento de identidade e controle de acesso
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para completar o download do MySQL. Em algumas regiões, o download pode ser redirecionado para a URL do CDN. Certifique-se de que a URL do CDN também é permitida, se necessário.


## <a name="azure-government-url-access"></a>Acesso à URL do governo azure

O aparelho de replicação precisa ter acesso a esses URLs no Governo Azure.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.us | Usado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.us | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.usgovcloudapi.net | Usado para operações de gerenciamento de replicação e coordenação
*.services.visualstudio.com | Usado para fins de telemetria (é opcional)
time.nist.gov | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A configuração do aparelho com OVA precisa de acesso a essas URLs. Eles são usados para controle de acesso e gerenciamento de identidade pelo Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para completar o download do MySQL. Em algumas regiões, o download pode ser redirecionado para a URL do CDN. Certifique-se de que a URL do CDN também é permitida, se necessário.

## <a name="port-access"></a>Acesso portuário

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço mobility em execução em VMs comunica-se com o dispositivo de replicação local (servidor de configuração) na porta HTTPS 443 de entrada, para gerenciamento de replicação.<br/><br/> As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
Aparelho de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza e criptografa e os envia para o armazenamento Azure na porta 443.<br/> Por padrão, o servidor de processo é executado no aparelho de replicação.


## <a name="replication-process"></a>Processo de replicação

1. Ao habilitar a replicação para uma VM, a replicação inicial para o armazenamento do Azure começa, usando a política de replicação especificada. 
2. O tráfego é replicado para pontos de extremidade públicos do Armazenamento do Microsoft Azure pela Internet. Não há suporte para replicação de tráfego através de uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações rastreadas para uma máquina são registradas.
4. A comunicação ocorre da seguinte maneira:
    - As VMs se comunicam com o aparelho de replicação na porta HTTPS 443 de entrada, para o gerenciamento de replicação.
    - O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
    - As VMs enviam dados de replicação para o servidor de processo (em execução no aparelho de replicação) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza e criptografa e os envia para o armazenamento Azure na porta 443.
5. Os dados de replicação registram primeiro a terra em uma conta de armazenamento de cache no Azure. Esses logs são processados e os dados são armazenados em um disco gerenciado pelo Azure.

![Arquitetura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado manualmente a partir do hub Azure Migrate. Recomendamos que você sempre execute a versão mais recente.

1. No Azure Migrate > Servidores > Azure Migrate: Avaliação do servidor, servidores de infra-estrutura, clique **em Servidores de configuração**.
2. Em **Servidores de configuração,** um link aparece na **Versão do Agente** quando uma nova versão do dispositivo de replicação estiver disponível. 
3. Baixe o instalador na máquina do aparelho de replicação e instale a atualização. O instalador detecta a corrente da versão em execução no aparelho.
 
## <a name="next-steps"></a>Próximas etapas

- [Saiba como](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) configurar o aparelho de replicação para migração vmm baseada em agente.
- [Saiba como](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) configurar o aparelho de replicação para servidores físicos.
