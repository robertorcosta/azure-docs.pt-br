---
title: Usar o link privado do Azure para conectar redes com segurança à automação do Azure
description: Usar o link privado do Azure para conectar redes com segurança à automação do Azure
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: a7ff659eb6fc204208c84146a2fc33c8278f7154
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207279"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>Usar o link privado do Azure para conectar redes com segurança à automação do Azure (versão prévia)

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente o serviço de automação em sua VNet. O tráfego de rede entre os computadores na VNet e a conta de automação atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Por exemplo, você tem uma VNet na qual desabilitou o acesso à Internet de saída. No entanto, você deseja acessar sua conta de automação de forma privada e usar recursos de automação como WebHooks, configuração de estado e trabalhos de runbook em Hybrid runbook Workers. Além disso, você deseja que os usuários tenham acesso à conta de automação somente por meio da VNET.  A implantação de um ponto de extremidade privado atinge essas metas.

Este artigo aborda quando usar e como configurar um ponto de extremidade privado com sua conta de automação (versão prévia).

![Visão geral conceitual do link privado para a automação do Azure](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> O suporte ao link privado com a automação do Azure (versão prévia) está disponível somente nas nuvens do Azure comercial e do Azure no governo dos EUA.

## <a name="advantages"></a>Vantagens

Com o Link Privado, você pode:

- Conecte-se de forma privada à automação do Azure sem abrir nenhum acesso à rede pública.
- Conecte-se de forma privada a Azure Monitor Log Analytics espaço de trabalho sem abrir nenhum acesso à rede pública.

    >[!NOTE]
    >Isso será necessário se sua conta de automação estiver vinculada a um espaço de trabalho Log Analytics para encaminhar dados de trabalho e quando você tiver habilitado recursos como Gerenciamento de Atualizações, Controle de Alterações e inventário, configuração de estado ou Iniciar/Parar VMs fora do horário comercial. Para obter mais informações sobre o link privado para Azure Monitor, consulte [usar o link privado do Azure para conectar redes com segurança a Azure monitor](../../azure-monitor/platform/private-link-security.md).

- Verifique se os dados de automação são acessados somente por meio de redes privadas autorizadas.
- Evite vazamento de dados de suas redes privadas definindo o recurso de automação do Azure que se conecta por meio de seu ponto de extremidade privado.
- Conecte com segurança sua rede local privada à automação do Azure usando o ExpressRoute e o link privado.
- Mantenha todo o tráfego dentro da rede de backbone de Microsoft Azure.

Para mais informações, confira [Principais benefícios do Link Privado](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Como ele funciona

O link privado da automação do Azure conecta um ou mais pontos de extremidade privados (e, portanto, as redes virtuais nas quais eles estão contidos) ao recurso de conta de automação. Esses pontos de extremidade são computadores que usam WebHooks para iniciar um runbook, máquinas que hospedam a função de Hybrid Runbook Worker e nós DSC.

Depois de criar pontos de extremidade privados para automação, cada uma das URLs de automação voltadas para o público, que você ou um computador pode contatar diretamente, é mapeada para um ponto de extremidade privado em sua VNet.

Como parte da versão de visualização, uma conta de automação não pode acessar os recursos do Azure que são protegidos usando o ponto de extremidade privado. Por exemplo, Azure Key Vault, SQL do Azure, conta de armazenamento do Azure, etc.

### <a name="webhook-scenario"></a>Cenário de webhook

Você pode iniciar runbooks fazendo uma POSTAgem na URL do webhook. Por exemplo, a URL é semelhante a:`https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>Cenário de configuração de estado (AgentSvc)

A configuração de estado fornece o serviço de gerenciamento de configuração do Azure que permite que você grave, gerencie e compile configurações de DSC (configuração de estado desejado) do PowerShell para nós em qualquer datacenter local ou na nuvem.

O agente no computador é registrado com o serviço DSC e, em seguida, usa o ponto de extremidade de serviço para efetuar pull da configuração de DSC. O ponto de extremidade do serviço de agente é semelhante a: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

A URL para o ponto de extremidade público & privado seria a mesma, no entanto, ela seria mapeada para um endereço IP privado quando o link privado estiver habilitado.

## <a name="planning-based-on-your-network"></a>Planejamento com base na sua rede

Antes de configurar o recurso de conta de automação, considere os requisitos de isolamento de rede. Avalie o acesso de suas redes virtuais à Internet pública e as restrições de acesso à sua conta de automação (incluindo a configuração de um escopo de grupo de link privado para Azure Monitor logs, se integrados à sua conta de automação). Além disso, inclua uma revisão dos [registros DNS](./automation-region-dns-records.md) do serviço de automação como parte do seu plano para garantir que os recursos com suporte funcionem sem problemas.

### <a name="connect-to-a-private-endpoint"></a>Conectar-se a um ponto de extremidade privado

Crie um ponto de extremidade privado para conectar nossa rede. Você pode criá-lo no [centro de Link portal do Azure privado](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Depois que as alterações no publicNetworkAccess e no link privado forem aplicadas, pode levar até 35 minutos para que elas entrem em vigor.

Nesta seção, você criará um ponto de extremidade privado para sua conta de automação.

1. No lado superior esquerdo da tela, selecione **criar um recurso > rede > o centro de vínculo privado (versão prévia)**.

2. Em **Central de Link Privado – Visão Geral**, na opção **Criar uma conexão privada com um serviço**, selecione **Iniciar**.

3. Em **criar uma máquina virtual-noções básicas**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome | Insira seu *PrivateEndpoint*. |
    | Região | Selecione **YourRegion**. |
    |||

4. Selecione **Avançar: Recurso**.

5. Em **criar um ponto de extremidade privado-recurso**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione conectar-se a um recurso do Azure em meu diretório.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft. Automation/automationAccounts**. |
    | Recurso |Selecionar *myAutomationAccount*|
    |Sub-recurso de destino |Selecione *webhook* ou *DSCAndHybridWorker* dependendo do seu cenário.|
    |||

6. Selecione **Avançar: configuração**.

7. Em **criar um ponto de extremidade privado-configuração**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecione *(novo) privatelink. Azure-Automation.net* |
    |||

8. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

9. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

No **centro de links privado (versão prévia)**, selecione **pontos de extremidade privados** para exibir o recurso de link privado.

![Link privado do recurso de automação](./media/private-link-security/private-link-automation-resource.png)

Selecione o recurso para ver todos os detalhes. Isso cria um novo ponto de extremidade privado para sua conta de automação e atribui a ele um IP privado de sua rede virtual. O **status da conexão** é mostrado como **aprovado**.

Da mesma forma, um FQDN (nome de domínio totalmente qualificado) exclusivo é criado para a configuração de estado (AgentSvc) e para o tempo de execução de trabalho do Hybrid Runbook Worker (jrds). Cada um deles recebe um IP separado de sua VNet e o **status da conexão** é mostrado como **aprovado**.

Se o consumidor de serviço tiver permissões RBAC no recurso de automação, eles poderão escolher o método de aprovação automática. Nesse caso, quando a solicitação atinge o recurso do provedor de automação, nenhuma ação é necessária do provedor de serviços e a conexão é aprovada automaticamente.

## <a name="set-public-network-access-flags"></a>Definir sinalizadores de acesso à rede pública

Você pode configurar uma conta de automação para negar todas as configurações públicas e permitir somente conexões por meio de pontos de extremidade privados para aprimorar ainda mais a segurança da rede. Se você quiser restringir o acesso à conta de automação somente dentro da VNet e não permitir o acesso da Internet pública, poderá definir `publicNetworkAccess` a propriedade como `$true` .

Quando a configuração **negar acesso à rede pública** é definida como `true` , somente as conexões por meio de pontos de extremidade privados são permitidas e todas as conexões por meio de pontos de extremidade públicos são negadas com uma mensagem de erro.

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de acesso à **rede pública** no nível da conta de automação:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $true
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>Configuração de DNS

Ao se conectar a um recurso do link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços do Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Sua configuração de DNS deve ser revisada e atualizada para se conectar usando seu ponto de extremidade privado.

O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para um determinado recurso de link privado.

Você pode usar as seguintes opções para definir as configurações de DNS para pontos de extremidade privados:

* Use o arquivo de host (recomendado apenas para teste). Você pode usar o arquivo de host em uma máquina virtual para substituir o uso do DNS pela resolução de nome primeiro.

* Use uma [zona DNS privada](../../dns/private-dns-privatednszone.md). Você pode usar zonas DNS privadas para substituir a resolução DNS para um ponto de extremidade particular específico. Uma zona DNS privada pode ser vinculada à sua rede virtual para resolver domínios específicos. Para habilitar o agente em sua máquina virtual para se comunicar por meio do ponto de extremidade privado, crie um registro de DNS privado como `privatelink.azure-automation.net` . Adicione um novo mapeamento *de registro DNS a para* o IP do ponto de extremidade privado.

* Use o encaminhador DNS (opcional). Você pode usar o encaminhador DNS para substituir a resolução DNS para um recurso de link particular específico. Se o [servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado em uma rede virtual, você poderá criar uma regra de encaminhamento de DNS para usar uma zona DNS privada para simplificar a configuração para todos os recursos de link privado.

Para obter mais informações, consulte [configuração de DNS do ponto de extremidade privado do Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](../../private-link/private-endpoint-overview.md).