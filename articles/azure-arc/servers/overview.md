---
title: Visão geral do Azure Arc para servidores (versão prévia)
description: Saiba como usar o Azure Arc para servidores para gerenciar computadores que estão hospedados fora do Azure como se fosse um recurso do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 02/12/2020
ms.topic: overview
ms.openlocfilehash: 33681d5c9e296d7c292dabbd64560e3d95c45af2
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190314"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>O que é Azure Arc para servidores (versão prévia)

O Azure Arc para servidores (versão prévia) permite que você gerencie computadores Windows e Linux hospedados fora do Azure em sua rede corporativa ou em outro provedor de nuvem, semelhante à maneira como você gerencia máquinas virtuais do Azure nativas. Quando um computador híbrido é conectado ao Azure, ele se torna um computador conectado e é tratado como um recurso no Azure. Cada computador conectado tem uma ID de Recurso, é gerenciado como parte de um grupo de recursos dentro de uma assinatura e se beneficia de constructos padrão do Azure, tais como o Azure Policy e a aplicação de marcas.

Para oferecer essa experiência com seus computadores híbridos hospedados fora do Azure, o agente do Azure Connected Machine precisa ser instalado em cada computador que você planeja conectar ao Azure. Esse agente não oferece nenhuma outra funcionalidade e não substitui o [agente do Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).

>[!NOTE]
>Essa versão prévia destina-se a fins de avaliação. Recomendamos que você não gerencie computadores de produção críticos.
>

## <a name="supported-scenarios"></a>Cenários com suporte

O Azure Arc para servidores (versão prévia) é compatível com os seguintes cenários com computadores conectados:

- Atribua [configurações de convidado do Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de política para máquinas virtuais do Azure.
- Os dados de log coletados pelo agente do Log Analytics e armazenados no workspace do Log Analytics no qual o computador está registrado agora contêm propriedades específicas ao computador, como a ID de Recurso, que pode ser usada para dar suporte ao acesso do log do [contexto de recurso](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Regiões com suporte

Com o Azure Arc para servidores (versão prévia), só há suporte a determinadas regiões:

- WestUS2
- WestEurope
- WestAsia

Na maioria dos casos, o local selecionado durante a criação do script de instalação deve ser a região do Azure mais próxima à localização do seu computador. Os dados em repouso serão armazenados na geografia do Azure que contém a região especificada por você, o que também pode afetar sua escolha de região se você tiver requisitos de residência de dados. Se a região do Azure a qual seu computador está conectado for afetada por uma interrupção, a máquina conectada não será afetada, mas talvez as operações de gerenciamento que usam o Azure não possam terminar. Para obter resiliência no caso de uma interrupção regional, se houver várias localizações que fornecem um serviço com redundância geográfica, será melhor conectar os computadores em cada localização a uma região diferente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows e Linux para o agente do Azure Connected Machine: 

- Windows Server 2012 R2 e versões posteriores
- Ubuntu 16.04 e 18.04

>[!NOTE]
>Essa versão prévia do agente do Connected Machine para Windows só é compatível com o Windows Server configurado para usar o idioma inglês.
>

### <a name="required-permissions"></a>Permissões necessárias

- Para os computadores de integração, você é membro da função **Integração do Azure Connected Machine**.

- Para ler, modificar, reintegrar e excluir um computador, é necessário ser membro da função **Administrador de Recursos do Azure Connected Machine**. 

### <a name="azure-subscription-and-service-limits"></a>Limites de serviço e assinatura do Azure

Antes de configurar seus computadores com o Azure Arc para servidores (versão prévia), você deve examinar os [limites de assinatura](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) e os [limites do grupo de recursos](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) do Azure Resource Manager para planejar o número de computadores a ser conectado.

### <a name="networking-configuration"></a>Configuração de rede

O agente do Connected Machine para Linux e Windows comunica a saída com segurança ao Azure Arc pela porta TCP 443. Se o computador se conectar por meio de um firewall ou servidor proxy para se comunicar pela Internet, examine os requisitos abaixo para entender os requisitos da configuração de rede.

Se a conectividade de saída estiver restrita por seu firewall ou servidor proxy, verifique se as URLs listadas abaixo não estão bloqueadas. Se você permitir apenas os intervalos de IP ou nomes de domínio necessários para o agente se comunicar com o serviço, deverá permitir também o acesso às Marcas de Serviço e URLs a seguir.

Marcas de serviço:

- AzureActiveDirectory
- AzureTrafficManager

URLs:

| recurso de agente | Descrição |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Configuração de convidado|
|*-agentservice-prod-1.azure-automation.net|Configuração de convidado|
|*.his.hybridcompute.azure-automation.net|Serviço de identidade híbrida|

Para obter uma lista de endereços IP para cada tag de serviço/região, confira o arquivo JSON – [Intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft publica atualizações semanais que contêm cada serviço do Azure e os intervalos de IP que ele usa. Para obter mais informações, confira [Marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

As URLs na tabela anterior são necessárias, além das informações sobre o intervalo de endereços IP da Marca de Serviço, porque a maioria dos serviços não tem um registro de Marca de Serviço no momento. Como tal, os endereços IP estão sujeitos a alterações. Se os intervalos de endereço IP forem necessários para sua configuração de firewall, a Marca de Serviço **AzureCloud** deverá ser usada para permitir o acesso a todos os serviços do Azure. Não desabilite o monitoramento de segurança ou a inspeção dessas URLs. Permita-os como você faria com outro tráfego de Internet.

### <a name="register-azure-resource-providers"></a>Registrar provedores de recursos do Azure

O Azure Arc para servidores (versão prévia) depende dos seguintes provedores de recursos do Azure em sua assinatura para usar esse serviço:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se não estiverem registrados, você poderá registrá-los usando os seguintes comandos:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

CLI do Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Você também pode registrar os provedores de recursos no portal do Azure seguindo as etapas no [portal do Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Agente do Connected Machine

Você pode baixar o pacote do Agente do Azure Connected Machine para Windows e Linux dos locais listados abaixo.

- [Pacote do Windows Installer do agente do Windows](https://aka.ms/AzureConnectedMachineAgent) do Centro de Download da Microsoft.
- O pacote do agente do Linux é distribuído do [repositório de pacotes](https://packages.microsoft.com/) da Microsoft usando o formato de pacote preferencial para a distribuição (.RPM ou .DEB).

>[!NOTE]
>Durante essa versão prévia, apenas um pacote foi lançado, adequado para Ubuntu 16.04 ou 18.04.

## <a name="install-and-configure-agent"></a>Instalar e configurar o agente

Conectar computadores em seu ambiente híbrido diretamente com o Azure pode ser feito usando métodos diferentes, dependendo das suas necessidades. A tabela a seguir realça cada método para determinar qual funciona melhor para sua organização.

| Método | Descrição |
|--------|-------------|
| Interativamente | Instalar manualmente o agente em um número único ou pequeno de computadores seguindo as etapas em [Conectar computadores do portal do Azure](onboard-portal.md).<br> No portal do Azure, você pode gerar um script e executá-lo no computador para automatizar as etapas de instalação e configuração do agente.|
| Em escala | Instalar e configurar o agente para vários computadores seguindo [Conectar computadores usando uma Entidade de Serviço](onboard-service-principal.md).<br> Esse método cria uma entidade de serviço para conectar computadores de maneira não interativa.|


## <a name="next-steps"></a>Próximas etapas

- Para começar a avaliar o Azure Arc para servidores (versão prévia), siga o artigo [Conectar computadores híbridos ao Azure do portal do Azure](onboard-portal.md). 