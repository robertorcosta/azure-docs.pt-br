---
title: Migrar serviços de domínio azure AD a partir de uma rede virtual clássica | Microsoft Docs
description: Saiba como migrar uma instância de domínio gerenciada pelo Azure AD Domain Services do modelo de rede virtual Classic para uma rede virtual baseada no Gerenciador de recursos.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655005"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrar os serviços de domínio do Azure AD do modelo clássico de rede virtual para o Gerenciador de recursos

O Azure Active Directory Domain Services (AD DS) suporta um movimento único para clientes que atualmente usam o modelo de rede virtual Classic para o modelo de rede virtual do Resource Manager. Os domínios gerenciados do Azure AD DS que usam o modelo de implantação do Gerenciador de recursos fornecem recursos adicionais, como política de senha sinuosa, logs de auditoria e proteção contra bloqueio de contas.

Este artigo descreve os benefícios e considerações para a migração, em seguida, as etapas necessárias para migrar com sucesso uma instância Azure AD DS existente.

> [!NOTE]
> Em 2017, o Azure AD Domain Services tornou-se disponível para hospedagem em uma rede Azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro usando os recursos modernos do Azure Resource Manager. Como as implantações do Azure Resource Manager substituem totalmente as implantações clássicas, as implantações clássicas de rede virtual do Azure AD DS serão retiradas em 1º de março de 2023.
>
> Para obter mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Visão geral do processo de migração

O processo de migração pega uma instância AD DS existente do Azure que é executada em uma rede virtual Classic e a move para uma rede virtual do Gerenciador de Recursos existente. A migração é realizada usando o PowerShell, e tem duas etapas principais de execução - *preparação* e *migração*.

![Visão geral do processo de migração para o Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Na fase *de preparação,* o Azure AD DS faz um backup do domínio para obter o snapshot mais recente de usuários, grupos e senhas sincronizados com o domínio gerenciado. A sincronização é então desativada e o serviço de nuvem que hospeda o domínio gerenciado pelo Azure AD DS é excluído. Durante a fase de preparação, o domínio Gerenciado pelo Azure AD DS não consegue autenticar usuários.

![Fase de preparação para migrar Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

No estágio *de migração,* os discos virtuais subjacentes para os controladores de domínio do domínio Clássico Azure AD DS são copiados para criar as VMs usando o modelo de implantação do Gerenciador de recursos. O domínio gerenciado do Azure AD DS é então recriado, o que inclui a configuração LDAPS e DNS. A sincronização com o Azure AD é reiniciada e os certificados LDAP são restaurados. Não há necessidade de reunir nenhuma máquina em um domínio gerenciado pelo Azure AD DS – eles continuam a ser unidos ao domínio gerenciado e executados sem alterações.

![Migração do Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Benefícios migratórios

Quando você move um domínio gerenciado pelo Azure AD DS usando esse processo de migração, você evita a necessidade de reunir máquinas novamente ao domínio gerenciado ou excluir a instância Azure AD DS e criar uma a partir do zero. As VMs continuam a ser unidas ao domínio gerenciado pelo Azure AD DS no final do processo de migração.

Após a migração, o Azure AD DS fornece muitos recursos que só estão disponíveis para domínios usando redes virtuais do Resource Manager, tais como:

* Suporte à política de senhas finas.
* Proteção de bloqueio de contas ad.
* Notificações de e-mail de alertas no domínio gerenciado pelo Azure AD DS.
* Registros de auditoria usando o Monitor Azure.
* Integração de Arquivos Azure
* Integração hd Insights

Os domínios gerenciados do Azure AD DS que usam uma rede virtual do Resource Manager ajudam você a ficar atualizado com os novos recursos mais recentes. O suporte ao Azure AD DS usando redes virtuais Clássicas deve ser preterido no futuro.

## <a name="example-scenarios-for-migration"></a>Exemplos de cenários para migração

Alguns cenários comuns para migrar um domínio gerenciado pelo Azure AD DS incluem os seguintes exemplos.

> [!NOTE]
> Não converta a rede virtual Classic até que você tenha confirmado uma migração bem sucedida. A conversão da rede virtual remove a opção de reverter ou restaurar o domínio gerenciado pelo Azure AD DS se houver algum problema durante as etapas de migração e verificação.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrar o Azure AD DS para uma rede virtual do Gerenciador de Recursos existente (recomendado)

Um cenário comum é onde você já transferiu outros recursos clássicos existentes para um modelo de implantação do Resource Manager e uma rede virtual. O peering é então usado da rede virtual Resource Manager para a rede virtual Classic que continua a executar o Azure AD DS. Essa abordagem permite que os aplicativos e serviços do Gerenciador de Recursos usem a funcionalidade de autenticação e gerenciamento do domínio gerenciado pelo Azure AD DS na rede virtual Classic. Uma vez migrados, todos os recursos são executados usando o modelo de implantação do Resource Manager e a rede virtual.

![Migrar o Azure AD DS para uma rede virtual do Gerenciador de Recursos existente](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova os gateways VPN existentes ou o peering de rede virtual configurado na rede virtual Classic.
1. Migrar o domínio gerenciado do Azure AD DS usando as etapas descritas neste artigo.
1. Teste e confirme uma migração bem-sucedida e exclua a rede virtual Classic.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migre vários recursos, incluindo o Azure AD DS

Neste cenário de exemplo, você migra o Azure AD DS e outros recursos associados do modelo de implantação Classic para o modelo de implantação do Gerenciador de Recursos. Se alguns recursos continuarem a ser executados na rede virtual Classic ao lado do domínio gerenciado pelo Azure AD DS, todos eles podem se beneficiar da migração para o modelo de implantação do Gerenciador de Recursos.

![Migrar vários recursos para o modelo de implantação do Gerenciador de Recursos](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova os gateways VPN existentes ou o peering de rede virtual configurado na rede virtual Classic.
1. Migrar o domínio gerenciado do Azure AD DS usando as etapas descritas neste artigo.
1. Configure peering de rede virtual entre a rede virtual Classic e a rede Resource Manager.
1. Teste e confirme uma migração bem sucedida.
1. [Mova recursos clássicos adicionais como VMs][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrar Azure AD DS, mas manter outros recursos na rede virtual Classic

Com este cenário de exemplo, você tem a quantidade mínima de tempo de inatividade em uma sessão. Você só migra o Azure AD DS para uma rede virtual do Resource Manager e mantém os recursos existentes no modelo de implantação classic e na rede virtual. Em um período de manutenção seguinte, você pode migrar os recursos adicionais do modelo de implantação classic e da rede virtual conforme desejado.

![Migre apenas o Azure AD DS para o modelo de implantação do Gerenciador de Recursos](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova os gateways VPN existentes ou o peering de rede virtual configurado na rede virtual Classic.
1. Migrar o domínio gerenciado do Azure AD DS usando as etapas descritas neste artigo.
1. Configure peering de rede virtual entre a rede virtual Classic e a nova rede virtual Resource Manager.
1. Posteriormente, [migre os recursos adicionais][migrate-iaas] da rede virtual Classic conforme necessário.

## <a name="before-you-begin"></a>Antes de começar

À medida que você se prepara e depois migra um domínio gerenciado pelo Azure AD DS, existem algumas considerações em torno da disponibilidade de serviços de autenticação e gerenciamento. O domínio gerenciado pelo Azure AD DS não está disponível por um período de tempo durante a migração. Aplicativos e serviços que dependem do Azure AD DS experimentam tempo de inatividade durante a migração.

> [!IMPORTANT]
> Leia todos esses artigos de migração e orientação antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio Azure AD DS por períodos de tempo. Usuários, serviços e aplicativos não podem autenticar contra o domínio gerenciado durante o processo de migração.

### <a name="ip-addresses"></a>Endereços IP

Os endereços IP do controlador de domínio para uma alteração de domínio gerenciada pelo Azure AD DS após a migração. Essa alteração inclui o endereço IP público para o ponto final LDAP seguro. Os novos endereços IP estão dentro da faixa de endereços da nova sub-rede na rede virtual Resource Manager.

No caso de reversão, os endereços IP podem mudar após a reversão.

O Azure AD DS normalmente usa os dois primeiros endereços IP disponíveis na faixa de endereços, mas isso não é garantido. No momento, não é possível especificar os endereços IP para usar após a migração.

### <a name="downtime"></a>Tempo de inatividade

O processo de migração envolve os controladores de domínio ficando offline por um período de tempo. Os controladores de domínio estão inacessíveis enquanto o Azure AD DS é migrado para o modelo de implantação do Gerenciador de Recursos e rede virtual. Em média, o tempo de inatividade é de cerca de 1 a 3 horas. Este período de tempo é a partir do momento em que os controladores de domínio são retirados offline até o momento em que o primeiro controlador de domínio volta on-line. Essa média não inclui o tempo necessário para que o segundo controlador de domínio se replique, ou o tempo que pode levar para migrar recursos adicionais para o modelo de implantação do Gerenciador de recursos.

### <a name="account-lockout"></a>Bloqueio de conta

Os domínios gerenciados pelo Azure AD DS que são executados em redes virtuais Clássicas não possuem políticas de bloqueio de contas AD em vigor. Se as VMs forem expostas à internet, os invasores poderão usar métodos de spray de senha para forçar a entrada de contas. Não há política de bloqueio de contas para impedir essas tentativas. Para domínios gerenciados pelo Azure AD DS que usam o modelo de implantação do Gerenciador de recursos e redes virtuais, as políticas de bloqueio de contas AD protegem contra esses ataques de spray de senha.

Por padrão, 5 tentativas de senha ruins em 2 minutos bloqueiam uma conta por 30 minutos.

Uma conta bloqueada não pode ser usada para fazer login, o que pode interferir na capacidade de gerenciar o domínio gerenciado pelo Azure AD DS ou aplicativos gerenciados pela conta. Depois que um domínio gerenciado pelo Azure AD DS é migrado, as contas podem experimentar o que parece ser um bloqueio permanente devido a repetidas tentativas fracassadas de login. Dois cenários comuns após a migração incluem o seguinte:

* Uma conta de serviço que está usando uma senha expirada.
    * A conta de serviço tenta repetidamente entrar com uma senha expirada, que bloqueia a conta. Para corrigir isso, localize o aplicativo ou VM com credenciais vencidas e atualize a senha.
* Uma entidade maliciosa está usando tentativas de força bruta para entrar em contas.
    * Quando as VMs são expostas à internet, os atacantes geralmente tentam combinações comuns de nome de usuário e senha enquanto tentam assinar. Essas repetidas tentativas de login fracassadas podem bloquear as contas. Não é recomendável usar contas de administrador com nomes *genéricos,* como administrador ou *administrador,* por exemplo, para minimizar que as contas administrativas sejam bloqueadas.
    * Minimize o número de VMs que estão expostas à internet. Você pode usar [o Azure Bastion][azure-bastion] para se conectar com segurança às VMs usando o portal Azure.

Se você suspeitar que algumas contas podem ser bloqueadas após a migração, as etapas finais de migração descrevem como ativar a auditoria ou alterar as configurações da política de senha sinuosa.

### <a name="roll-back-and-restore"></a>Reverter e restaurar

Se a migração não for bem sucedida, haverá processo para reverter ou restaurar um domínio gerenciado pelo Azure AD DS. A reversão é uma opção de autoatendimento para retornar imediatamente o estado do domínio gerenciado para antes da tentativa de migração. Os engenheiros de suporte do Azure também podem restaurar um domínio gerenciado do backup como último recurso. Para obter mais informações, veja [como reverter ou restaurar uma migração falhada](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Restrições em redes virtuais disponíveis

Existem algumas restrições nas redes virtuais para as que um domínio gerenciado pelo Azure AD DS pode ser migrado. A rede virtual destination Resource Manager deve atender aos seguintes requisitos:

* A rede virtual Resource Manager deve estar na mesma assinatura do Azure que a rede virtual Classic em que o Azure AD DS está atualmente implantado.
* A rede virtual Resource Manager deve estar na mesma região que a rede virtual Classic em que o Azure AD DS está atualmente implantado.
* A sub-rede virtual do Resource Manager deve ter pelo menos 3-5 endereços IP disponíveis.
* A sub-rede virtual do Gerenciador de Recursos deve ser uma sub-rede dedicada para o Azure AD DS e não deve hospedar nenhuma outra carga de trabalho.

Para obter mais informações sobre os requisitos da rede virtual, consulte [considerações de design de rede virtual e opções de configuração][network-considerations].

## <a name="migration-steps"></a>Etapas da migração

A migração para o modelo de implantação do Resource Manager e a rede virtual é dividida em 5 etapas principais:

| Etapa    | Realizado através de  | Tempo estimado  | Tempo de inatividade  | Reverter/Restaurar? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Passo 1 - Atualizar e localizar a nova rede virtual](#update-and-verify-virtual-network-settings) | Portal do Azure | 15 minutos | Não é necessário tempo de inatividade | N/D |
| [Passo 2 - Preparar o domínio gerenciado do Azure AD DS para migração](#prepare-the-managed-domain-for-migration) | PowerShell | 15 a 30 minutos em média | O tempo de inatividade do Azure AD DS começa após a conclusão deste comando. | Reverter e restaurar disponível. |
| [Passo 3 - Mover o domínio gerenciado do Azure AD DS para uma rede virtual existente](#migrate-the-managed-domain) | PowerShell | 1 – 3 horas em média | Um controlador de domínio está disponível quando este comando estiver concluído, o tempo de inatividade termina. | Por falha, tanto a reversão (self service) quanto a restauração estão disponíveis. |
| [Passo 4 - Testar e esperar pelo controlador de domínio de réplica](#test-and-verify-connectivity-after-the-migration)| Portal PowerShell e Azure | 1 hora ou mais, dependendo do número de testes | Ambos os controladores de domínio estão disponíveis e devem funcionar normalmente. | N/D Uma vez que a primeira VM é migrada com sucesso, não há opção de reversão ou restauração. |
| [Passo 5 - Etapas de configuração opcionais](#optional-post-migration-configuration-steps) | Portal azure e VMs | N/D | Não é necessário tempo de inatividade | N/D |

> [!IMPORTANT]
> Para evitar tempo de inatividade adicional, leia todo este artigo de migração e orientação antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio Azure AD DS por um período de tempo. Usuários, serviços e aplicativos não podem autenticar contra o domínio gerenciado durante o processo de migração.

## <a name="update-and-verify-virtual-network-settings"></a>Atualize e verifique as configurações da rede virtual

Antes de iniciar o processo de migração, complete as seguintes verificações e atualizações iniciais. Essas etapas podem acontecer a qualquer momento antes da migração e não afetam a operação do domínio gerenciado pelo Azure AD DS.

1. Atualize o ambiente Azure PowerShell local para a versão mais recente. Para completar as etapas de migração, você precisa de pelo menos a versão *2.3.2*.

    Para obter informações sobre como verificar e atualizar sua versão do PowerShell, consulte [a visão geral do Azure PowerShell][azure-powershell].

1. Crie ou escolha uma rede virtual de gerenciador de recursos existente.

    Certifique-se de que as configurações de rede não bloqueiem as portas necessárias para o Azure AD DS. As portas devem ser abertas tanto na rede virtual Classic quanto na rede virtual Resource Manager. Essas configurações incluem tabelas de rotas (embora não seja recomendado usar tabelas de rota) e grupos de segurança de rede.

    Para exibir as portas necessárias, consulte [Grupos de segurança da rede e portas necessárias][network-ports]. Para minimizar os problemas de comunicação de rede, recomenda-se esperar e aplicar um grupo de segurança de rede ou uma tabela de rota à rede virtual do Gerenciador de recursos após a migração ser concluída com sucesso.

    Anote esse grupo de recursos de destino, direcione a rede virtual e direcione a sub-rede virtual. Esses nomes de recursos são usados durante o processo de migração.

1. Verifique o azure AD DS gerenciado saúde de domínio no portal Azure. Se você tiver algum alerta para o domínio gerenciado, resolva-os antes de iniciar o processo de migração.
1. Opcionalmente, se você planeja transferir outros recursos para o modelo de implantação do Resource Manager e rede virtual, confirme se esses recursos podem ser migrados. Para obter mais informações, consulte [a migração suportada pela plataforma de recursos IaaS do Classic para o Resource Manager][migrate-iaas].

    > [!NOTE]
    > Não converta a rede virtual Classic em uma rede virtual resource manager. Se você fizer isso, não há opção de reverter ou restaurar o domínio gerenciado pelo Azure AD DS.

## <a name="prepare-the-managed-domain-for-migration"></a>Prepare o domínio gerenciado para migração

O Azure PowerShell é usado para preparar o domínio gerenciado pelo Azure AD DS para migração. Essas etapas incluem fazer um backup, pausar a sincronização e excluir o serviço de nuvem que hospeda o Azure AD DS. Quando esta etapa é concluída, o Azure AD DS é retirado offline por um período de tempo. Se a etapa de preparação falhar, você pode [reverter para o estado anterior](#roll-back).

Para preparar o domínio gerenciado pelo Azure AD DS para migração, complete as seguintes etapas:

1. Instale `Migrate-Aaads` o script na [Galeria PowerShell][powershell-script]. Este script de migração PowerShell é um assinatura digital da equipe de engenharia azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Crie uma variável para manter as credenciais pelo script de migração usando o [cmdlet Get-Credential.][get-credential]

    A conta de usuário especificada precisa de privilégios *de administrador global* no seu inquilino Azure AD para habilitar o Azure AD DS e, em seguida, privilégios de *contribuinte* em sua assinatura Do Azure para criar os recursos Azure AD DS necessários.

    Quando solicitado, digite uma conta de usuário e senha apropriadas:

    ```powershell
    $creds = Get-Credential
    ```

1. Agora execute `Migrate-Aadds` o cmdlet usando o parâmetro *-Prepare.* Forneça o *-ManagedDomainFqdn* para seu próprio domínio gerenciado pelo Azure AD DS, como *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrar o domínio gerenciado

Com o domínio gerenciado pelo Azure AD DS preparado e feito backup, o domínio pode ser migrado. Esta etapa recria as VMs do controlador de domínio Azure AD Domain Services usando o modelo de implantação do Gerenciador de recursos. Este passo pode levar de 1 a 3 horas para ser concluído.

Execute `Migrate-Aadds` o cmdlet usando o parâmetro *-Commit.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerenciado do Azure AD DS preparado na seção anterior, como *aaddscontoso.com*:

Especifique o grupo de recursos de destino que contém a rede virtual para a qual deseja migrar o Azure AD DS, como *o myResourceGroup*. Forneça a rede virtual de destino, como *myVnet,* e a sub-rede, como *DomainServices*.

Depois que este comando é executado, você não pode reverter:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Depois que o script validar o domínio gerenciado estiver preparado para migração, *insira Y* para iniciar o processo de migração.

> [!IMPORTANT]
> Não converta a rede virtual Classic em uma rede virtual do Resource Manager durante o processo de migração. Se você converter a rede virtual, você não poderá reverter ou restaurar o domínio gerenciado do Azure AD DS, pois a rede virtual original não existirá mais.

A cada dois minutos durante o processo de migração, um indicador de progresso informa o status atual, conforme mostrado na saída de exemplo a seguir:

![Indicador de progresso da migração do Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

O processo de migração continua a ser executado, mesmo se você fechar o script PowerShell. No portal Azure, o status dos relatórios de domínio gerenciados como *Migração*.

Quando a migração for concluída com sucesso, você poderá visualizar o endereço IP do seu primeiro controlador de domínio no portal Azure ou através do Azure PowerShell. Uma estimativa de tempo sobre o segundo controlador de domínio disponível também é mostrada.

Nesta fase, você pode, opcionalmente, mover outros recursos existentes do modelo de implantação classic e rede virtual. Ou, você pode manter os recursos no modelo de implantação classic e peer the virtual networks uns com os outros depois que a migração do Azure AD DS estiver concluída.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Teste e verifique a conectividade após a migração

Pode levar algum tempo para que o segundo controlador de domínio seja implantado com sucesso e esteja disponível para uso no domínio gerenciado pelo Azure AD DS.

Com o modelo de implantação do Gerenciador de Recursos, os recursos de rede para o domínio gerenciado pelo Azure AD DS são mostrados no portal Azure ou no Azure PowerShell. Para saber mais sobre o que esses recursos de rede são e fazer, consulte [os recursos de rede usados pelo Azure AD DS][network-resources].

Quando houver pelo menos um controlador de domínio disponível, complete as seguintes etapas de configuração para conectividade de rede com VMs:

* **Atualizar as configurações do servidor DNS** Para permitir que outros recursos na rede virtual do Gerenciador de Recursos resolvam e usem o domínio gerenciado pelo Azure AD DS, atualize as configurações de DNS com os endereços IP dos novos controladores de domínio. O portal Azure pode configurar automaticamente essas configurações para você. Para saber mais sobre como configurar a rede virtual do Resource Manager, consulte [As configurações de Atualização dNS para a rede virtual Do Zure][update-dns].
* **Reiniciar VMs com domínio** - À medida que os endereços IP do servidor DNS para os controladores de domínio Azure AD DS mudam, reinicie mvs com adesão de domínio para que eles usem as novas configurações do servidor DNS. Se os aplicativos ou VMs tiverem configurações de DNS configuradas manualmente, atualize-as manualmente com os novos endereços IP do servidor DNS dos controladores de domínio mostrados no portal Azure.

Agora teste a conexão de rede virtual e a resolução de nomes. Em uma VM conectada à rede virtual do Resource Manager ou peered a ela, tente os seguintes testes de comunicação de rede:

1. Verifique se você pode pingar o endereço IP de um dos controladores de domínio, como`ping 10.1.0.4`
    * Os endereços IP dos controladores de domínio são mostrados na página **Propriedades** para o domínio gerenciado do Azure AD DS no portal Azure.
1. Verifique a resolução de nomes do domínio gerenciado, como`nslookup aaddscontoso.com`
    * Especifique o nome DNS para seu próprio domínio gerenciado pelo Azure AD DS para verificar se as configurações de DNS estão corretas e se resolve.

O segundo controlador de domínio deve estar disponível de 1 a 2 horas após o término do cmdlet de migração. Para verificar se o segundo controlador de domínio está disponível, consulte a página **Propriedades** para o domínio gerenciado do Azure AD DS no portal Azure. Se dois endereços IP forem mostrados, o segundo controlador de domínio estiver pronto.

## <a name="optional-post-migration-configuration-steps"></a>Etapas opcionais de configuração pós-migração

Quando o processo de migração é concluído com sucesso, algumas etapas de configuração opcionais incluem ativar registros de auditoria ou notificações de e-mail ou atualizar a política de senha de grãofino.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Inscreva-se em registros de auditoria usando o Azure Monitor

O Azure AD DS expõe registros de auditoria para ajudar a solucionar problemas e visualizar eventos nos controladores de domínio. Para obter mais informações, consulte [Habilitar e usar registros de auditoria][security-audits].

Você pode usar modelos para monitorar informações importantes expostas nos registros. Por exemplo, o modelo da pasta de trabalho de registro de auditoria pode monitorar possíveis bloqueios de conta no domínio gerenciado pelo Azure AD DS.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Configure notificações de e-mail do Azure AD Domain Services

Para ser notificado quando um problema for detectado no domínio gerenciado pelo Azure AD DS, atualize as configurações de notificação de e-mail no portal Azure. Para obter mais informações, consulte [Configurar configurações de notificação][notifications].

### <a name="update-fine-grained-password-policy"></a>Atualize a política de senha sinuosa

Se necessário, você pode atualizar a política de senha de grãofino para ser menos restritiva do que a configuração padrão. Você pode usar os registros de auditoria para determinar se uma configuração menos restritiva faz sentido e, em seguida, configure a diretiva conforme necessário. Use as seguintes etapas de alto nível para revisar e atualizar as configurações de diretiva para contas bloqueadas repetidamente após a migração:

1. [Configure a política de senha][password-policy] para menos restrições no domínio gerenciado pelo Azure AD DS e observe os eventos nos registros de auditoria.
1. Se alguma conta de serviço estiver usando senhas expiradas conforme identificado nos registros de auditoria, atualize essas contas com a senha correta.
1. Se uma VM estiver exposta à internet, revise nomes de contas genéricas como *administrador,* *usuário*ou *convidado* com tentativas de entrada alta. Sempre que possível, atualize essas VMs para usar contas menos genericamente nomeadas.
1. Use um rastreamento de rede na VM para localizar a origem dos ataques e bloqueie esses endereços IP de serem capazes de tentar fazer logins.
1. Quando houver problemas mínimos de bloqueio, atualize a política de senha de grãofino para ser tão restritiva quanto necessário.

### <a name="creating-a-network-security-group"></a>Como criar um grupo de segurança de rede

O Azure AD DS precisa de um grupo de segurança de rede para proteger as portas necessárias para o domínio gerenciado e bloquear todos os outros tráfegos de entrada. Esse grupo de segurança de rede atua como uma camada extra de proteção para bloquear o acesso ao domínio gerenciado e não é criado automaticamente. Para criar o grupo de segurança da rede e abrir as portas necessárias, revise as seguintes etapas:

1. No portal Azure, selecione o recurso Azure AD DS. Na página de exibição geral, um botão é exibido para criar um grupo de segurança de rede se não houver nenhum associado aos Serviços de Domínio Ad do Azure.
1. Se você usar LDAP seguro, adicione uma regra ao grupo de segurança da rede para permitir tráfego de entrada para a porta *TCP* *636*. Para obter mais informações, consulte [Configurar LDAP seguro][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Reverter e restaurar a migração

Até certo ponto do processo de migração, você pode optar por reverter ou restaurar o domínio gerenciado do Azure AD DS.

### <a name="roll-back"></a>Rolar para trás

Se houver um erro ao executar o cmdlet PowerShell para se preparar para a migração na etapa 2 ou para a migração em si na etapa 3, o domínio gerenciado pelo Azure AD DS pode reverter para a configuração original. Este retrocesso requer a rede virtual Classic original. Observe que os endereços IP ainda podem mudar após a reversão.

Execute `Migrate-Aadds` o cmdlet usando o parâmetro *-Abort.* Forneça o *-ManagedDomainFqdn* para seu próprio domínio gerenciado do Azure AD DS preparado em uma seção anterior, como *aaddscontoso.com*, e o nome de rede virtual Classic, como *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Restaurar

Como último recurso, os Serviços de Domínio Ad do Azure podem ser restaurados a partir do último backup disponível. Um backup é feito na etapa 1 da migração para garantir que o backup mais atual esteja disponível. Este backup é armazenado por 30 dias.

Para restaurar o domínio gerenciado pelo Azure AD DS a partir do backup, abra um ticket de caso de [suporte usando o portal Azure][azure-support]. Forneça seu ID de diretório, nome de domínio e motivo para restauração. O processo de suporte e restauração pode levar vários dias para ser concluído.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas após a migração para o modelo de implantação do Gerenciador de recursos, revise algumas das seguintes áreas comuns de solução de problemas:

* [Solucionar problemas de adesão ao domínio][troubleshoot-domain-join]
* [Solucionar problemas de bloqueio de contas][troubleshoot-account-lockout]
* [Solucionando problemas de login de conta][troubleshoot-sign-in]
* [Solucionar problemas de conectividade LDAP seguros][tshoot-ldaps]

## <a name="next-steps"></a>Próximas etapas

Com o domínio gerenciado pelo Azure AD DS migrado para o modelo de implantação do Gerenciador de recursos, [criar e criar um VM do Windows][join-windows] e, em seguida, instalar ferramentas de [gerenciamento][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
