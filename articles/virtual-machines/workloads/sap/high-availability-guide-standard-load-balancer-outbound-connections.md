---
title: Conectividade de ponto final público para VMs Azure&Standard ILB em cenários SAP HA
description: Conectividade de ponto final público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293906"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Conectividade de ponto final público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP

O escopo deste artigo é descrever configurações que permitirão a conectividade de saída para pontos finais públicos. As configurações estão principalmente no contexto de Alta Disponibilidade com Marcapasso para SUSE/RHEL.  

Se você estiver usando o Pacemaker com o agente de cerca DoZure em sua solução de alta disponibilidade, as VMs devem ter conectividade de saída com a API de gerenciamento do Azure.  
O artigo apresenta várias opções para que você selecione a opção mais adequada para o seu cenário.  

## <a name="overview"></a>Visão geral

Ao implementar alta disponibilidade para soluções SAP via clustering, um dos componentes necessários é o [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). O Azure oferece duas SKUs balanceador de carga: padrão e básico.

O balanceador de carga Standard Azure oferece algumas vantagens sobre o balanceador de carga Basic. Por exemplo, ele funciona em todas as regiões de disponibilidade do Azure, tem melhores recursos de monitoramento e registro para facilitar a solução de problemas, reduzir a latência. O recurso "portas HA" abrange todas as portas, ou seja, não é mais necessário listar todas as portas individuais.  

Existem algumas diferenças importantes entre o básico e o SKU padrão do balanceador de carga Azure. Uma delas é o manuseio do tráfego de saída para o ponto final público. Para comparação completa do balanceador de carga Básico versus Padrão SKU, consulte [comparação SKU do Balancer de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
Quando as VMs sem endereços IP públicos são colocadas no pool de backend do balanceador de carga Padrão Azure,não há conectividade de saída para pontos finais públicos, a menos que a configuração adicional seja feita.  

Se uma VM for atribuída a um endereço IP público ou a VM estiver no pool de backend de um balanceador de carga com endereço IP público, ela terá conectividade de saída para pontos finais públicos.  

Os sistemas SAP geralmente contêm dados de negócios confidenciais. Raramente é aceitável que as VMs que hospedam sistemas SAP tenham endereços IP públicos. Ao mesmo tempo, há cenários, que exigiriam conectividade de saída da VM para pontos finais públicos.  

Exemplos de cenários, que exigem acesso ao ponto final público do Azure são:  
- Usando o Azure Fence Agent como um mecanismo de esgrima em clusters Pacemaker
- Serviço de Backup do Azure
- Azure Site Recovery  
- Usando o repositório público para corrigir o sistema operacional
- O fluxo de dados do aplicativo SAP pode exigir conectividade de saída ao ponto final público

Se a implantação do SAP não exigir conectividade de saída para pontos finais públicos, você não precisará implementar a configuração adicional. É suficiente para criar o SKU Azure Load Balancer padrão interno para o seu cenário de alta disponibilidade, assumindo que também não há necessidade de conectividade de entrada a partir de pontos finais públicos.  

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de backend do balanceador de carga Padrão Azure(sem endereço IP público), não haverá conectividade de saída da Internet, a menos que a configuração adicional seja executada para permitir o roteamento para pontos finais públicos.  
>Se as VMs tiverem endereços IP públicos ou estiverem no pool de backend do balanceador de carga do Azure com endereço IP público, a VM já terá conectividade de saída para pontos finais públicos.


Leia primeiro os seguintes artigos:

* Balanceador de carga padrão azure
  * [Visão geral do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) - visão geral abrangente do balanceador de carga padrão do Azure, princípios, conceitos e tutoriais importantes 
  * [Conexões de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) - cenários sobre como alcançar conectividade de saída no Azure
  * [Regras de saída do balanceador](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)de carga - explica os conceitos de regras de saída do balanceador de carga e como criar regras de saída
* Firewall do Azure
  * [Visão geral do Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview)- visão geral do Firewall do Azure
  * [Tutorial: Implantar e configurar o Firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) - instruções sobre como configurar o Firewall Azure através do portal Azure
* [Redes Virtuais - Regras definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Conceitos e regras de roteamento do Azure  
* [Tags de serviço de grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) - como simplificar sua configuração de Grupos de Segurança de Rede e Firewall com tags de serviço

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Balancer de carga padrão externo adicional do Azure para conexões de saída à internet

Uma opção para obter conectividade de saída aos pontos finais públicos, sem permitir a conectividade de entrada à VM a partir do ponto final público, é criar um segundo balanceador de carga com endereço IP público, adicionar as VMs ao pool de backend do segundo balanceador de carga e definir apenas [regras de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
Use [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para controlar os pontos finais públicos, acessíveis para chamadas de saída da VM.  
Para obter mais informações, consulte o Cenário 2 em [conexões de saída de documento](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios).  
A configuração se pareceria com:  

![Controle a conectividade a pontos finais públicos com grupos de segurança de rede](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerações importantes

- Você pode usar um Balancer de Carga Pública adicional para várias VMs na mesma sub-rede para obter conectividade de saída ao ponto final público e otimizar o custo  
- Use [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para controlar quais pontos finais públicos são acessíveis a partir das VMs. Você pode atribuir o Grupo de Segurança de Rede à sub-rede ou a cada VM. Sempre que possível, use [tags de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para reduzir a complexidade das regras de segurança.  
- O balanceador de carga padrão do Azure com endereço IP público e regras de saída permite acesso direto ao ponto final público. Se você tem requisitos de segurança corporativa para ter todos os passes de tráfego de saída através de uma solução corporativa centralizada para auditoria e registro, você pode não ser capaz de cumprir o requisito com este cenário.  

>[!TIP]
>Sempre que possível, use [tags de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para reduzir a complexidade do Grupo de Segurança de Rede . 

### <a name="deployment-steps"></a>Etapas de implantação.

1. Criar balanceador de carga  
   1. No [portal Azure,](https://portal.azure.com) clique em Todos os recursos, Adicione e procure por **Load Balancer**  
   1. Clique **em Criar** 
   1. Nome do balanceador de carga **MyPublicILB**  
   1. Selecione **Public** como um Tipo, **Padrão** como SKU  
   1. Selecione **Criar endereço IP público** e especifique como um nome **MyPublicILBFrondEndIP**  
   1. Selecione **Zona Redundante** como zona de disponibilidade  
   1. Clique em Revisar e Criar, em seguida, clique em Criar  
2. Crie o pool de backend **MyBackendPoolOfPublicILB** e adicione as VMs.  
   1. Selecione a rede Virtual  
   1. Selecione as VMs e seus endereços IP e adicione-os ao pool de back-end  
3. [Criar regras de saída](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Atualmente não é possível criar regras de saída a partir do portal Azure. Você pode criar regras de saída com [o Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Crie regras do grupo de segurança de rede para restringir o acesso a pontos finais públicos específicos. Se houver o Grupo de Segurança de Rede existente, você pode ajustá-lo. O exemplo abaixo mostra como habilitar o acesso à API de gerenciamento do Azure: 
   1. Navegue até o Grupo de Segurança de Rede
   1. Clique em Regras de segurança de saída
   1. Adicione uma regra para **negar** todo o acesso de saída à **Internet**.
   1. Adicione uma regra para **permitir o** acesso ao **AzureCloud**, com prioridade inferior à prioridade da regra para negar todo o acesso à Internet.


   As regras de segurança de saída seriam como: 

   ![Conexão de saída com o Second Load Balancer com IP público](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Para obter mais informações sobre os grupos de segurança da Rede Azure, consulte [Grupos de segurança ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall do Azure para conexões de saída à internet

Outra opção para obter conectividade de saída para pontos finais públicos, sem permitir conectividade de entrada à VM a partir de pontos finais públicos, é com o Azure Firewall. O Azure Firewall é um serviço gerenciado, com alta disponibilidade incorporada e pode abranger várias Zonas de Disponibilidade.  
Você também precisará implantar a [Rota Definida pelo Usuário,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)associada à sub-rede onde as VMs e o balanceador de carga do Azure são implantados, apontando para o firewall do Azure, para direcionar o tráfego através do Firewall do Azure.  
Para obter detalhes sobre como implantar o Firewall Do Azure, consulte [Implantar e configurar o Firewall Do Zure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

A arquitetura seria semelhante a:

![Conexão de saída com o Firewall Do Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerações importantes

- O Firewall Azure é um serviço nativo em nuvem, com alta disponibilidade incorporada e suporta implantação zonal.
- Requer uma sub-rede adicional que deve ser chamada de AzureFirewallSubnet. 
- Se transferir grandes conjuntos de dados da rede virtual onde as VMs SAP estão localizadas, para uma VM em outra rede virtual ou para o ponto final público, pode não ser uma solução econômica. Um exemplo disso é copiar grandes backups em redes virtuais. Para obter detalhes, consulte os preços do Firewall Do Azure.  
- Se a solução corporativa de Firewall não for o Firewall Do Azure, e você tiver requisitos de segurança para ter todos os passes de tráfego de saída embora uma solução corporativa centralizada, essa solução pode não ser prática.  

>[!TIP]
>Sempre que possível, use [tags de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para reduzir a complexidade das regras do Firewall do Azure.  

### <a name="deployment-steps"></a>Etapas de implantação.

1. As etapas de implantação supõem que você já tem rede virtual e sub-rede definidas para suas VMs.  
2. Crie a Subnet **AzureFirewallSubnet** na mesma Rede Virtual, onde o VMS e o Balancer de Carga Padrão são implantados.  
   1. No portal Azure, navegue até a Rede Virtual: Clique em Todos os Recursos, Procure a Rede Virtual, Clique na Rede Virtual, Selecione Subredes.  
   1. Clique em Adicionar sub-rede. Digite **AzureFirewallSubnet** como Nome. Digite o intervalo de endereços apropriado. Salve.  
3. Crie o Firewall Azure.  
   1. No portal Azure selecione Todos os recursos, clique em Adicionar, Firewall, Criar. Selecione o grupo Recurso (selecione o mesmo grupo de recursos, onde está a Rede Virtual).  
   1. Digite o nome do recurso Do Firewall do Azure. Por exemplo, **MyAzureFirewall**.  
   1. Selecione Região e selecione pelo menos duas zonas de disponibilidade, alinhadas com as regiões de Disponibilidade onde suas VMs estão implantadas.  
   1. Selecione sua Rede Virtual, onde os VMs SAP e o balanceador De carga Padrão Azure são implantados.  
   1. Endereço IP público: Clique em criar e digitar um nome. Por **exemplo, MyFirewallPublicIP**.  
4. Crie a regra de firewall do Azure para permitir conectividade de saída a pontos finais públicos especificados. O exemplo mostra como permitir o acesso ao ponto final público da Api de gerenciamento do Azure.  
   1. Selecione Regras, Coleta de regras de rede e clique em Adicionar a coleta de regras de rede.  
   1. Nome: **MyOutboundRule**, insira Priority, Select Action **Allow**.  
   1. Serviço: Nome **ToAzureAPI**.  Protocolo: Selecione **Qualquer**. Endereço de origem: insira o intervalo para sua sub-rede, onde as VMs e o Balancer de Carga Padrão são implantados por exemplo: **11.97.0.0/24**. Portos de <b>*</b>destino: enter .  
   1. Salvar
   1. Como você ainda está posicionado no Firewall do Azure, selecione visão geral. Anote o endereço IP privado do Firewall Do Azure.  
5. Criar rota para o Firewall Do Azure  
   1. No portal Azure, selecione Todos os recursos e clique em Adicionar, Tabela de rota, Criar.  
   1. Digite Nome MyRouteTable, selecione Assinatura, Grupo de recursos e Localização (correspondendo à localização de sua rede virtual e firewall).  
   1. Salvar  

   A regra do firewall ![se pareceria: Conexão de saída com o Firewall Do Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Crie a rota definida pelo usuário da sub-rede de suas VMs para o IP privado do **MyAzureFirewall**.
   1. Ao se posicionar na Tabela de Rotas, clique em Rotas. Selecione Adicionar. 
   1. Nome da rota: ToMyAzureFirewall, prefixo de endereço: **0.0.0.0/0**. Próximo tipo de salto: Selecione O Aparelho Virtual. Próximo endereço de salto: digite o endereço IP privado do firewall que você configurou: **11.97.1.4**.  
   1. Salvar

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>O uso de proxy para marcapasso chama a API de gerenciamento do Azure

Você pode usar proxy para permitir chamadas pacemaker para o ponto final público da API de gerenciamento do Azure.  

### <a name="important-considerations"></a>Considerações importantes

  - Se já houver proxy corporativo em vigor, você pode encaminhar chamadas de saída para pontos finais públicos através dele. As chamadas de saída para os pontos finais públicos passarão pelo ponto de controle corporativo.  
  - Certifique-se de que a configuração proxy permite conectividade de saída à API de gerenciamento do Azure:`https://management.azure.com`  
  - Certifique-se de que há uma rota das VMs para o Proxy  
  - O proxy lidará apenas com chamadas HTTP/HTTPS. Se houver necessidade adicional de fazer chamadas de saída para o ponto final público sobre diferentes protocolos (como RFC), uma solução alternativa será necessária  
  - A solução Proxy deve estar altamente disponível, para evitar instabilidade no cluster Pacemaker  
  - Dependendo da localização do proxy, ele pode introduzir latência adicional nas chamadas do Azure Fence Agent para a API de gerenciamento do Azure. Se o seu proxy corporativo ainda estiver no local, enquanto seu cluster Pacemaker estiver no Azure, meça a latência e considere, se essa solução é adequada para você  
  - Se ainda não houver proxy corporativo altamente disponível, não recomendamos essa opção, pois o cliente estaria incorrendo em custo extra e complexidade. No entanto, se você decidir implantar uma solução proxy adicional, com o objetivo de permitir a conectividade de saída do Pacemaker para a API pública do Azure Management, certifique-se de que o proxy está altamente disponível e a latência das VMs para o proxy é baixa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configuração do marcapasso com proxy 

Existem muitas opções diferentes de Proxy disponíveis no setor. As instruções passo a passo para a implantação do proxy estão fora do escopo deste documento. No exemplo abaixo, assumimos que seu proxy está respondendo ao **MyProxyService** e ouvindo a porta **MyProxyPort**.  
Para permitir que o marca-passo se comunique com a API de gerenciamento do Azure, execute as seguintes etapas em todos os nós de cluster:  

1. Edite o arquivo de configuração do marca-passo /etc/sysconfig/marca-passo e adicione as seguintes linhas (todos os nós de cluster):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Reinicie o serviço do marca-passo em **todos os** nós de cluster.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Próximas etapas

* [Saiba como configurar o Pacemaker no SUSE no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Saiba como configurar pacemaker no Red Hat no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
