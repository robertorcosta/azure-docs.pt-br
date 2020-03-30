---
title: Configurar grupo de disponibilidade (modelo de quickstart do Azure)
description: Use modelos de partida rápida do Azure para criar o cluster de failover do Windows, junte VMs do SQL Server ao cluster, crie o ouvinte e configure o balanceador de carga interna no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022374"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Use modelos de partida rápida do Azure para configurar um grupo de disponibilidade para SQL Server em uma VM Azure
Este artigo descreve como usar os modelos de quickstart do Azure para automatizar parcialmente a implantação de uma configuração de grupo de disponibilidade Always On para máquinas virtuais SQL Server no Azure. Dois modelos de quickstart do Azure são usados neste processo: 

   | Modelo | Descrição |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Cria o cluster de failover do Windows e junta as VMs do SQL Server a ele. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Cria o ouvinte do grupo de disponibilidade e configura o balanceador de carga interna. Este modelo só pode ser usado se o cluster failover do Windows foi criado com o modelo **de configuração 101-sql-vm-ag.** |
   | &nbsp; | &nbsp; |

Outras partes da configuração do grupo de disponibilidade devem ser feitas manualmente, como a criação do grupo de disponibilidade e a criação do balanceador de carga interna. Este artigo fornece a sequência de etapas manuais e automatizadas.
 

## <a name="prerequisites"></a>Pré-requisitos 
Para automatizar a configuração de um grupo de disponibilidade Always On usando modelos quickstart, você deve ter os seguintes pré-requisitos: 
- Uma [assinatura do Azure.](https://azure.microsoft.com/free/)
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs com domínio [no Azure executando o SQL Server 2016 (ou posterior) edição Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) que estão no mesmo conjunto de disponibilidade ou zona de disponibilidade e que foram [registradas no provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade): um para o balanceador de carga interna e outro para o ouvinte do grupo de disponibilidade dentro da mesma sub-rede do grupo de disponibilidade. Se um balanceador de carga existente estiver sendo usado, você precisará apenas de um endereço IP disponível.  

## <a name="permissions"></a>Permissões
As seguintes permissões são necessárias para configurar o grupo de disponibilidade Always On usando modelos de partida rápida do Azure: 

- Uma conta de usuário de domínio existente que tenha permissão **Criar objeto de computador** no domínio.  Por exemplo, uma conta de administração de domínio normalmente account@domain.comtem permissão suficiente (por exemplo: ). _Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster._
- A conta de usuário de domínio que controla o serviço SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Passo 1: Crie o cluster de failover e junte VMs do SQL Server ao cluster usando um modelo de partida rápida 
Depois que suas VMs do SQL Server tiverem sido registradas no provedor de recursos SQL VM, você pode juntar suas VMs do SQL Server ao *SqlVirtualMachineGroups*. Esse recurso define os metadados do cluster failover do Windows. Metadados incluem a versão, edição, nome de domínio totalmente qualificado, contas do Active Directory para gerenciar tanto o cluster quanto o serviço sql server e a conta de armazenamento como testemunha na nuvem. 

Adicionar VMs do SQL Server ao grupo de recursos *SqlVirtualMachineGroups* inicializa o Serviço de Cluster de Failover do Windows para criar o cluster e então une essas VMs do SQL Server àquele cluster. Esta etapa é automatizada com o modelo de partida rápida **de configuração de 101 sql-vm-ag.** Você pode implementá-lo usando as seguintes etapas:

1. Vá para o modelo de partida rápida de [**configuração de 101-sql-vm-ag..**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Em seguida, **selecione Implantar no Azure** para abrir o modelo quickstart no portal Azure.
1. Preencha os campos necessários para configurar os metadados para o cluster failover do Windows. Você pode deixar os campos opcionais em branco.

   A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   | **Assinatura** |  A assinatura em que estão suas VMs do SQL Server. |
   |**Grupo de recursos** | O grupo de recursos em que suas VMs do SQL Server residem. | 
   |**Nome do cluster de failover** | O nome que você deseja para o seu novo cluster failover do Windows. |
   | **Lista de VMs existentes** | As VMs do SQL Server que você deseja participar do grupo de disponibilidade e fazer parte deste novo cluster. Separe esses valores com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Versão do SQL Server** | A versão sql server de suas VMs do Servidor SQL. Selecione-o na lista de paradas. Atualmente, apenas imagens do SQL Server 2016 e do SQL Server 2017 são suportadas. |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio no qual residem suas VMs do SQL Server. |
   | **Conta de domínio existente** | Uma conta de usuário de domínio existente que tenha permissão **Criar objeto de computador** no domínio à medida que o [CNO](/windows-server/failover-clustering/prestage-cluster-adds) é criado durante a implantação do modelo. Por exemplo, uma conta de administração de domínio normalmente account@domain.comtem permissão suficiente (por exemplo: ). *Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster.*| 
   | **Senha da conta de domínio** | A senha da conta de usuário de domínio mencionada anteriormente. | 
   | **Conta do serviço SQL existente** | A conta de usuário de domínio que controla o serviço account@domain.com [sql server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante a implantação do grupo de disponibilidade (por exemplo: ). |
   | **Senha de serviço do SQL** | A senha usada pela conta de usuário de domínio que controla o serviço do SQL Server. |
   | **Nome de Testemunha de Nuvem** | Uma nova conta de armazenamento do Azure que será criada e usada para a testemunha na nuvem. Você pode modificar este nome. |
   | **\_artefatos Localização** | Este campo é definido por padrão e não deve ser modificado. |
   | **\_artefatos Localização Sas Token** | Este campo é intencionalmente deixado em branco. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e condições, selecione o **Concordo com os termos e condições indicados acima.** Em seguida, **selecione Comprar** para concluir a implantação do modelo quickstart. 
1. Para monitorar sua implantação, selecione a implantação do ícone de sino **Notificações** no banner de navegação superior ou vá para o **Grupo de recursos** no portal Azure. Selecione **Implantações** em **Configurações**e escolha a implantação **Microsoft.Template.** 

>[!NOTE]
> As credenciais fornecidas durante a implantação do modelo são armazenadas apenas durante o período de implantação. Após o término da implantação, essas senhas são removidas. Você será solicitado a fornecê-los novamente se adicionar mais VMs do SQL Server ao cluster. 


## <a name="step-2-manually-create-the-availability-group"></a>Passo 2: Crie manualmente o grupo de disponibilidade 
Crie manualmente o grupo de disponibilidade como você normalmente faria, usando [o SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento, porque o modelo de configuração rápida de configuração de **101-sql-vm-aglistener** faz isso automaticamente na etapa 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Passo 3: Crie manualmente o balanceador de carga interna
O ouvinte do grupo always on requer uma instância interna do Azure Load Balancer. O balanceador de carga interna fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite um failover e reconexão mais rápidos. Se as VMs do SQL Server em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, você poderá usar um balanceador de carga Básico. Caso contrário, você precisa usar um balanceador de carga Padrão. 

> [!IMPORTANT]
> O balanceador de carga interna deve estar na mesma rede virtual que as instâncias vm do Servidor SQL. 

Você só precisa criar o balanceador de carga interna. Na etapa 4, o modelo de configuração rápida de configuração de **configuração de 101 sql-vm-aglistener** lida com o resto da configuração (como o pool de back-end, o teste de saúde e as regras de balanceamento de carga). 

1. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 
2. No grupo de recursos, selecione **Adicionar**.
3. Pesquise pelo **balanceador de carga**. Nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.
4. Na **lâmina Do Balanceador de carga,** selecione **Criar**.
5. Na caixa de diálogo **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Digite um nome de texto que represente o balanceador de carga. Por exemplo, digite **sqlLB**. |
   | **Tipo** |**Interno**: a maioria das implementações usa um balanceador de carga interno que permite a conexão dos aplicativos dentro da mesma rede virtual ao grupo de disponibilidade.  </br> **Externo**: Permite que as aplicações se conectem ao grupo de disponibilidade através de uma conexão pública de internet. |
   | **Rede virtual** | Selecione a rede virtual na qual estão as instâncias do SQL Server. |
   | **Sub-rede** | Selecione a sub-rede na qual estão as instâncias do SQL Server. |
   | **Atribuição de endereço IP** |**Estático** |
   | **Endereço IP privado** | Especifique um endereço IP disponível na sub-rede. |
   | **Assinatura** |Se você tiver várias assinaturas, este campo poderá aparecer. Selecione a assinatura que você deseja associar a esse recurso. Normalmente é a mesma assinatura de todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual estão as instâncias do SQL Server. |
   | **Local** |Selecione o local do Azure no qual estão as instâncias do SQL Server. |
   | &nbsp; | &nbsp; |

6. Selecione **Criar**. 


>[!IMPORTANT]
> O recurso IP público para cada VM do Servidor SQL deve ter um SKU padrão para ser compatível com o balanceador de carga Padrão. Para determinar o SKU do recurso IP público da VM, vá para **O Grupo de Recursos,** selecione o recurso **Endereço IP Público** para o VM do Servidor SQL e localize o valor em **SKU** no painel **Visão Geral.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Passo 4: Crie o ouvinte do grupo de disponibilidade e configure o balanceador de carga interna usando o modelo quickstart

Crie o ouvinte do grupo de disponibilidade e configure o balanceador de carga interna automaticamente usando o modelo **quickstart de configuração de configuração de 101 sql-vm-aglistener.** O modelo provisiona o recurso Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. O modelo de início rápido **101-sql-vm-aglistener-setup**, por meio do provedor de recursos de VM do SQL, faz o seguintes:

- Cria um novo recurso IP de front-end (com base no valor do endereço IP fornecido durante a implantação) para o ouvinte. 
- Configura as configurações de rede para o cluster e o balanceador de carga interna. 
- Configura o pool de back-end para o balanceador de carga interna, o teste de saúde e as regras de balanceamento de carga.
- Cria o ouvinte do grupo de disponibilidade com o endereço ip e o nome dado.
 
>[!NOTE]
> Você só pode usar **a configuração de 101-sql-vm-aglistener** somente se o cluster failover do Windows for criado com o modelo **de configuração 101-sql-vm-ag.**
   
   
Para configurar o balanceador de carga interna e criar o ouvinte do grupo de disponibilidade, faça o seguinte:
1. Vá para o modelo de configuração rápida [de configuração de 101 sql-vm-aglistener](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selecione **Implantar no Azure** para iniciar o modelo de partida rápida no portal Azure.
1. Preencha os campos necessários para configurar o balanceador de carga interna e crie o ouvinte do grupo de disponibilidade. Você pode deixar os campos opcionais em branco. 

   A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupo de recursos** | O grupo de recursos em que estão suas VMs do SQL Server e o grupo de disponibilidade. | 
   |**Nome do cluster de failover existente** | O nome do cluster em que suas VMs do SQL Server ingressaram. |
   | **Grupo de disponibilidade do SQL existente**| O nome do grupo de disponibilidade de que suas VMs do SQL Server fazem parte. |
   | **Lista de VMs existentes** | Os nomes das VMs do SQL Server que fazem parte do grupo de disponibilidade mencionado anteriormente. Separe os nomes com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Ouvinte** | O nome DNS que você deseja atribuir ao ouvinte. Por padrão, este modelo especifica o nome "aglistener", mas você pode alterá-lo. O nome não deve exceder 15 caracteres. |
   | **Porta do Ouvinte** | A porta que você quer que o ouvinte use. Normalmente, esta porta deve ser padrão de 1433. Este é o número de porta que o modelo especifica. Mas se a porta padrão tiver sido alterada, a porta de ouvinte deve usar esse valor. | 
   | **IP do Ouvinte** | O endereço IP que você deseja que o ouvinte use. Este endereço será criado durante a implantação do modelo, portanto, forneça um que ainda não esteja em uso.  |
   | **Sub-rede existente** | O nome da sub-rede interna de suas VMs do servidor SQL (por exemplo: *padrão*). Você pode determinar esse valor indo para **o Grupo de recursos,** selecionando sua rede virtual, selecionando **Sub-redes** no painel **Configurações** e copiando o valor em **Nome**. |
   | **Balanceador de carga interno existente** | O nome do balanceador de carga interno que você criou na etapa 3. |
   | **Porta de Investigação** | A porta de sonda que você deseja que o balanceador de carga interna use. O modelo usa 59999 por padrão, mas você pode alterar esse valor. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e condições, selecione o **Concordo com os termos e condições indicados acima.** Selecione **Comprar** para concluir a implantação do modelo quickstart. 
1. Para monitorar sua implantação, selecione a implantação do ícone de sino **Notificações** no banner de navegação superior ou vá para o **Grupo de recursos** no portal Azure. Selecione **Implantações** em **Configurações**e escolha a implantação **Microsoft.Template.** 

>[!NOTE]
>Se sua implantação falhar no meio do caminho, você precisará remover manualmente [o ouvinte recém-criado](#remove-the-availability-group-listener) usando o PowerShell antes de reimplantar o modelo **de inicialinicialde configuração rápida de 101 sql-vm-aglistener.** 

## <a name="remove-the-availability-group-listener"></a>Remova o ouvinte do grupo de disponibilidade
Se você precisar remover o ouvinte do grupo de disponibilidade que o modelo configurou, você deve passar pelo provedor de recursos SQL VM. Como o ouvinte está registrado através do provedor de recursos SQL VM, excluí-lo através do SQL Server Management Studio é insuficiente. 

O melhor método é excluí-lo através do provedor de recursos SQL VM usando o seguinte trecho de código no PowerShell. Isso remove os metadados do ouvinte do grupo de disponibilidade do provedor de recursos SQL VM. Ele também exclui fisicamente o ouvinte do grupo de disponibilidade. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Erros comuns
Esta seção aborda alguns problemas conhecidos e suas soluções possíveis. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>O ouvinte de grupo de disponibilidade para o grupo de disponibilidade “\<AG-Name>” já existe
O grupo de disponibilidade selecionado usado no modelo de quickstart do Azure para o ouvinte do grupo de disponibilidade já contém um ouvinte. Ou ele está fisicamente dentro do grupo de disponibilidade, ou seus metadados permanecem dentro do provedor de recursos SQL VM. Remova o ouvinte usando [o PowerShell](#remove-the-availability-group-listener) antes de reimplantar o modelo de configuração rápida de configuração de **101-sql-vm-aglistener.** 

### <a name="connection-only-works-from-primary-replica"></a>A conexão funciona apenas na réplica primária
Esse comportamento é provavelmente de uma implantação de modelo de configuração de **101-sql-vm-aglistener-agrole** falha que deixou a configuração do balanceador de carga interna em um estado inconsistente. Verifique se o pool de back-end lista o conjunto de disponibilidade, e se há regras para a análise da integridade e o balanceamento de carga. Se algo estiver faltando, a configuração do balanceador de carga interna é um estado inconsistente. 

Para resolver esse comportamento, remova o ouvinte usando [o PowerShell,](#remove-the-availability-group-listener)exclua o balanceador de carga interna através do portal Azure e comece novamente na etapa 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - apenas a lista da máquina virtual do SQL pode ser atualizada
Esse erro pode ocorrer quando você está implantando o modelo **de configuração de 101-sql-vm-aglistener** se o ouvinte foi excluído via SQL Server Management Studio (SSMS), mas não foi excluído do provedor de recursos SQL VM. A exclusão do ouvinte via SSMS não remove os metadados do ouvinte do provedor de recursos SQL VM. O ouvinte deve ser excluído do provedor de recursos através do [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>A conta de domínio não existe
Este erro pode ter duas causas. Ou a conta de domínio especificada não existe ou está faltando os dados [upn (User Principal Name, nome principal do usuário).](/windows/desktop/ad/naming-properties#userprincipalname) O modelo **de configuração de 101-sql-vm-ag-ag** espera uma *user@domain.com*conta de domínio no formulário UPN (ou seja, ), mas algumas contas de domínio podem estar perdendo isso. Isso geralmente acontece quando um usuário local foi migrado para ser a primeira conta de administrador de domínio quando o servidor foi promovido a um controlador de domínio, ou quando um usuário foi criado através do PowerShell. 

Verifique se a conta existe. Se isso acontecer, você pode estar correndo para a segunda situação. Para resolvê-lo, faça o seguinte:

1. No controlador de domínio, abra a janela **Usuários e Computadores do Active Directory** na opção **Ferramentas** em **Gerenciador do Servidor**. 
2. Vá para a conta selecionando **Usuários** no painel esquerdo.
3. Clique com o botão direito do mouse na conta e selecione **Propriedades**.
4. Selecione a guia **Conta.** Se a caixa **de nome de logon** do Usuário estiver em branco, esta é a causa do seu erro. 

    ![Conta de usuário em branco indica UPN ausente](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Preencha a caixa **de nome de logon** do usuário para corresponder ao nome do usuário e selecione o domínio apropriado na lista de baixa. 
6. Selecione **Aplicar** para salvar suas alterações e fechar a caixa de diálogo selecionando **OK**. 

Depois de fazer essas alterações, tente implantar o modelo de quickstart do Azure mais uma vez. 



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral das VMs do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para VMs do servidor SQL](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para VMs do Servidor SQL](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para VMs do Servidor SQL](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Alternar modelos de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md)



