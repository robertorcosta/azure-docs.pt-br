---
title: Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure | Microsoft Docs
description: " A migração das cargas de trabalho para o Azure IaaS traz oportunidades para reavaliar nosso designs "
services: security
documentationcenter: na
author: barclayn
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 3368f72aeb7909c3e0a8653bb5b094729c4c45ed
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228028"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure
Este artigo descreve as melhores práticas de segurança para VMs e sistemas operacionais.

As recomendações baseiam-se um consenso de opinião, e trabalhar com recursos da plataforma Windows Azure atuais e conjuntos de recursos. Como as opiniões e tecnologias podem mudar com o tempo, este artigo será atualizado para refletir essas mudanças.

Na maioria dos cenários de IaaS (Infraestrutura como Serviço), as [máquinas virtuais (VMs) do Azure](/azure/virtual-machines/) são a carga de trabalho principal para as organizações que usam a computação em nuvem. Esse fato é evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), nos quais as organizações desejam migrar cargas de trabalho para a nuvem de forma lenta. Nesses cenários, siga o [considerações gerais de segurança para IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplicar práticas recomendadas de segurança para todas as suas VMs.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Proteger VMs usando autenticação e controle de acesso
A primeira etapa na proteção de VMs é garantir que apenas usuários autorizados possam configurar novas VMs e acessar VMs.

> [!NOTE]
> To improve the security of Linux VMs on Azure, you can integrate with Azure AD authentication. When you use [Azure AD authentication for Linux VMs](/azure/virtual-machines/linux/login-using-aad), you centrally control and enforce policies that allow or deny access to the VMs.
>
>

**Melhor prática**: controle o acesso à VM.   
**Detalhe**: utilize as [políticas do Azure](/azure/azure-policy/azure-policy-introduction) para estabelecer convenções para recursos na organização e criar políticas personalizadas. Aplique essas políticas a recursos, como [grupos de recursos](/azure/azure-resource-manager/resource-group-overview). VMs que pertencem a um grupo de recursos herdam suas políticas.

Se a organização tiver muitas assinaturas, talvez seja necessário gerenciar de maneira eficiente o acesso, as políticas e a conformidade dessas assinaturas. [Os grupos de gerenciamento do Azure](/azure/azure-resource-manager/management-groups-overview) fornecem um nível de escopo acima das assinaturas. Você organiza assinaturas em grupos de gerenciamento (contêineres) e aplica as condições de governança a esses grupos. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo. Os grupos de gerenciamento fornecem gerenciamento de nível empresarial em larga escala, independentemente do tipo de assinaturas que você possa ter.

**Melhor prática**: reduza a variabilidade na configuração e implantação de VMs.   
**Detalhe**: utilize modelos do [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) para fortalecer as opções de implantação e facilitar o reconhecimento e inventário das VMs no ambiente.

**Melhor prática**: acesso privilegiado protegido.   
**Detalhe**: utilize uma [abordagem de privilégios mínimos](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e funções internas do Azure para permitir que os usuários acessem e configurem as VMs:

- [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): pode gerenciar VMs, mas não a rede virtual nem a conta de armazenamento à qual ele está conectado.
- [Colaborador de Máquina Virtual Clássica](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): pode gerenciar VMs criadas usando o modelo de implantação clássica, mas não a rede ou armazenamento de conta virtual para o qual as VMs estão conectadas.
- [Administrador de Segurança](../../role-based-access-control/built-in-roles.md#security-admin): na Central de Segurança somente: Pode visualizar as políticas de segurança, estados de segurança, editar políticas de segurança, visualizar alertas e recomendações, ignorar alertas e recomendações.
- [Usuário do DevTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): pode exibir tudo e se conectar a VMs, iniciá-las, reiniciá-las e desligá-las.

Os administradores de assinatura e coadministradores podem alterar essa configuração, tornando-os administradores de todas as VMs em uma assinatura. Certifique-se de confiar em todos os administradores de assinatura e coadministradores para fazer logon em qualquer um dos computadores.

> [!NOTE]
> É recomendável que você consolide as VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Usando grupos de recursos, você pode implantar, monitorar e acumular custos para os seus recursos de cobrança.
>
>

As organizações que controlam o acesso e a configuração da VM melhoram sua segurança geral da VM.

## <a name="use-multiple-vms-for-better-availability"></a>Use várias VMs para melhor disponibilidade
Se a VM executar aplicativos críticos que precisam ter alta disponibilidade, será altamente recomendável usar várias VMs. For better availability, use an [availability set](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) or availability [zones](../../availability-zones/az-overview.md).

Um conjunto de disponibilidade é um agrupamento lógico que você pode usar no Azure para garantir que os recursos de VM inseridos nele sejam isolados uns dos outros quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou de software do Azure, somente um subconjunto das VMs será afetado e o aplicativo geral continuará disponível aos clientes. Os conjuntos de disponibilidade são uma funcionalidade essencial quando você quer compilar soluções de nuvem confiáveis.

## <a name="protect-against-malware"></a>Proteja-se contra malware
Você deve instalar a proteção antimalware para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados. Você pode instalar o [Antimalware da Microsoft](antimalware.md) ou uma solução de proteção de pontos de extremidade do parceiro da Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) e [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

O Antimalware da Microsoft inclui recursos como proteção em tempo real, verificação agendada, correção de malware, atualizações de assinatura, atualizações de mecanismo, relatórios de exemplos e coleção de eventos de exclusão. Para ambientes que são hospedados separadamente do ambiente de produção, é possível usar uma extensão antimalware para ajudar a proteger as VMs e os serviços de nuvem.

Você pode integrar o Antimalware da Microsoft e as soluções de parceiros com a [Central de Segurança do Azure](../../security-center/index.yml) para facilidade de implantação e detecções internas (alertas e incidentes).

**Melhor prática**: instale uma solução antimalware para proteger contra malware.   
**Detalhe**: [instale uma solução de parceiro da Microsoft ou Antimalware da Microsoft](../../security-center/security-center-install-endpoint-protection.md)

**Melhor prática**: integre a solução antimalware à Central de Segurança para monitorar o status da proteção.   
**Detalhe**: [gerencie problemas de proteção de ponto de extremidade com a Central de Segurança](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Gerenciar as atualizações de VM
As VMs do Azure, como todas as VMs locais, devem ser gerenciadas pelo usuário. O Azure não efetua push de atualizações do Windows para VMs. Você deve gerenciar as atualizações da VM.

**Melhor prática**: mantenha as VMs atualizadas.   
**Detalhe**: use a solução [Gerenciamento de Atualizações](../../automation/automation-update-management.md) na Automação do Azure para gerenciar atualizações do sistema operacional nos computadores Windows e Linux implantados no Azure, em ambientes locais ou em outros provedores de nuvem. Você pode avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar rapidamente o processo de instalação das atualizações necessárias para os servidores.

Os computadores que são gerenciados pelo Gerenciamento de Atualizações usam as configurações a seguir para realizar implantações de atualização e avaliação:

- Microsoft Monitoring Agent (MMA) para Windows ou Linux
- DSC (PowerShell Desired State Configuration) para Linux
- Hybrid Runbook Worker de Automação
- Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

Se você usa o Windows Update, deixe a configuração automática do Windows Update habilitada.

**Melhor prática**: garanta na implantação que as imagens compiladas incluem a rodada mais recente de atualizações do Windows.   
**Detalhe**: verifique e instale todas as atualizações do Windows como uma primeira etapa de toda implantação. Essa medida é especialmente importante para aplicar ao implantar imagens que vêm de você ou sua própria biblioteca. Embora as imagens do Microsoft Azure Marketplace sejam atualizadas automaticamente por padrão, pode haver um tempo de atraso (até algumas semanas) após um lançamento público.

**Melhor prática**: reimplemente as VMs periodicamente para forçar uma atualização da versão do sistema operacional.   
**Detalhe**: defina a VM com um [modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para que você possa reimplementá-la facilmente. Usar um modelo fornece uma VM corrigida e segura quando necessário.

**Best practice**: Rapidly apply security updates to VMs.   
**Detail**: Enable Azure Security Center (Free tier or Standard tier) to [identify missing security updates and apply them](../../security-center/security-center-apply-system-updates.md).

**Melhor prática**: instale as atualizações de segurança mais recentes.   
**Detalhes**: algumas das primeiras cargas de trabalho que os clientes migram para o Azure são laboratórios e sistemas externos. Se as VMs do Azure hospedarem aplicativos ou serviços que precisam ser acessados pela Internet, esteja atento ao patches. Patch além do sistema operacional. As vulnerabilidades sem patch em aplicativos de parceiros também podem levar a problemas que podem ser evitados com um bom gerenciamento de patches.

**Melhor prática**: implante e teste uma solução de backup.   
**Detalhe**: um backup precisa ser tratado da mesma maneira como você lida qualquer outra operação. Isso é verdadeiro para os sistemas que fazem parte de seu ambiente de produção que se estende para a nuvem.

Os sistemas de desenvolvimento e teste devem seguir as estratégias de backup que ofereçam recursos de restauração semelhantes aos que os usuários já utilizam com base em sua experiência com os ambientes locais. As cargas de trabalho de produção movidas para o Azure devem se integrar a soluções de backup existentes quando possível. Você também pode usar o [Backup do Azure](../../backup/backup-azure-vms-first-look-arm.md) para ajudar a solucionar suas necessidades de backup.

As organizações que não impõem diretivas de atualização de software estão mais expostas a ameaças que exploram vulnerabilidades conhecidas anteriormente fixas. Para cumprir as normas do setor, as empresas devem provar que são diligentes e usam controles de segurança corretos para ajudar a garantir a segurança de suas cargas de trabalho localizadas na nuvem.

As melhores práticas de atualização de software para um datacenter tradicional e IaaS do Azure têm muitas semelhanças. É recomendável que você avalie as políticas de atualização de software atuais para incluir VMs localizadas no Azure.

## <a name="manage-your-vm-security-posture"></a>Gerenciar sua postura de segurança de VM
As ameaças cibernéticas estão evoluindo. A proteção das VMs exige um recurso de monitoramento que possa detectar ameaças rapidamente, impedir o acesso não autorizado aos recursos, acionar alertas e reduzir falsos positivos.

Para monitorar a postura de segurança das suas [VMs Windows ](../../security-center/security-center-virtual-machine.md) e [Linux](../../security-center/security-center-linux-virtual-machine.md), use a [Central de Segurança do Azure](../../security-center/security-center-intro.md). Na Central de Segurança, proteja as VMs aproveitando os seguintes recursos:

- Aplicar configurações de segurança do sistema operacional com as regras de configuração recomendadas.
- Identificar e baixar a segurança do sistema e as atualizações críticas que podem estar faltando.
- Implantar recomendações para proteção antimalware de ponto de extremidade.
- Validar criptografia de disco.
- Avaliar e corrigir vulnerabilidades.
- Detectar ameaças.

A Central de Segurança pode monitorar ativamente as ameaças e essas possíveis ameaças são expostas em alertas de segurança. As ameaças correlacionadas são agregadas em uma única exibição chamada incidente de segurança.

Security Center stores data in [Azure Monitor logs](/azure/log-analytics/log-analytics-overview). Azure Monitor logs provides a query language and analytics engine that gives you insights into the operation of your applications and resources. Os dados também são coletados do [Azure Monitor](../../batch/monitoring-overview.md), das soluções de gerenciamento e dos agentes instalados em máquinas virtuais na nuvem ou no local. Essa funcionalidade compartilhada ajuda a formar uma imagem completa do seu ambiente.

As organizações que não reforçam a segurança das VMs permaneçam sem conhecimento das possíveis tentativas de usuários não autorizados de contornar os controles de segurança.

## <a name="monitor-vm-performance"></a>Monitorar o desempenho de VM
Abuso de recursos pode ser um problema quando os processos VM consomem mais recursos do que deveriam. Problemas de desempenho com uma máquina virtual podem levar a interrupção do serviço, o que viola o princípio de segurança de disponibilidade. Isso é particularmente importante para as VMs que hospedam IIS ou outros servidores Web, já que o alto uso de CPU ou de memória pode indicar um ataque de DoS (ataque de negação de serviço). É fundamental monitorar o acesso à VM não apenas de forma reativa durante a ocorrência de um problema, mas também de forma proativa em relação ao desempenho da linha de base, conforme medido durante a operação normal.

É recomendável usar o [Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para obter visibilidade da integridade do recurso. Recursos do Azure Monitor:

- [Arquivos de log de diagnóstico de recurso](../../azure-monitor/platform/resource-logs-overview.md): monitora os recursos da VM e identifica possíveis problemas que podem comprometer o desempenho e a disponibilidade.
- [Extensão de Diagnóstico do Azure](/azure/azure-monitor/platform/diagnostics-extension-overview): fornece recursos de monitoramento e diagnóstico em VMs do Windows. É possível habilitar essas funcionalidades ao incluir a extensão como parte do [modelo do Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

As organizações que não monitoram o desempenho da VM não podem estipular se determinadas alterações nos padrões de desempenho são normais ou anormais. Uma VM que consome mais recursos do que o normal pode indicar um ataque de um recurso externo ou um processo comprometido em execução na VM.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Criptografar os arquivos de disco rígido virtual
É recomendável criptografar os VHDs (discos rígidos virtuais) para ajudar a proteger o volume de inicialização e volumes de dados em repouso no armazenamento, juntamente com os segredos e chave de criptografia.

O [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) ajuda você a criptografar os discos da máquina virtual da IaaS do Windows ou Linux. O Azure Disk Encryption usa o recurso [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows padrão do setor e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer uma criptografia de volume para os discos do sistema operacional e de dados. A solução é integrada ao [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura de cofre de chaves. A solução também garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no Armazenamento do Microsoft Azure.

A seguir, são apresentadas as melhores práticas para usar o Azure Disk Encryption:

**Melhor prática**: habilite a criptografia nas VMs.   
**Detalhes**: o Azure Disk Encryption gera e grava as chaves de criptografia no cofre de chaves. O gerenciamento de chaves de criptografia no cofre de chaves requer a autenticação do Azure AD. Crie um aplicativo do Azure AD para essa finalidade. Para fins de autenticação, você pode usar a autenticação baseada em segredo do cliente ou a [autenticação do Azure AD baseada em certificado de cliente](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Melhor prática**: use uma KEK (chave de criptografia de chave) para uma camada adicional de segurança para chaves de criptografia. Adicione uma KEK ao cofre de chaves.   
**Detail**: Use the [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet to create a key encryption key in the key vault. Também é possível importar uma KEK do HSM (módulo de segurança de hardware) local para o gerenciamento de chaves. Para obter mais informações, consulte a [documentação do Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. Manter uma cópia de caução dessa chave em um HSM de gerenciamento de chaves local oferece proteção adicional contra exclusão acidental de chaves.

**Melhor prática**: tire um [instantâneo](../../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou faça backup antes que os discos sejam criptografados. Os backups fornecem uma opção de recuperação caso ocorra uma falha inesperada durante a criptografia.   
**Detalhe**: VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. After a backup is made, you can use the **Set-AzVMDiskEncryptionExtension** cmdlet to encrypt managed disks by specifying the *-skipVmBackup* parameter. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [Backup do Microsoft Azure](../../backup/backup-azure-vms-encryption.md).

**Melhor prática**: para garantir que os segredos de criptografia não ultrapassem os limites regionais, o Azure Disk Encryption precisa que o cofre de chaves e as VMs sejam colocados na mesma região.   
**Detalhe**: crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada.

Quando você aplica o Azure Disk Encryption, é possível atender às seguintes necessidades comerciais:

- As VMs da IaaS são protegidas em repouso por meio da tecnologia de criptografia padrão do setor para atender aos requisitos de segurança e conformidade da organização.
- As VMs de IaaS são iniciadas com políticas e chaves controladas pelo cliente, e você pode auditar o seu uso no cofre de chaves.

## <a name="restrict-direct-internet-connectivity"></a>Restrict direct internet connectivity
Monitor and restrict VM direct internet connectivity. Attackers constantly scan public cloud IP ranges for open management ports and attempt “easy” attacks like common passwords and known unpatched vulnerabilities. The following table lists best practices to help protect against these attacks:

**Best practice**: Prevent inadvertent exposure to network routing and security.   
**Detail**: Use RBAC to ensure that only the central networking group has permission to networking resources.

**Best practice**: Identify and remediate exposed VMs that allow access from “any” source IP address.   
**Detail**: Use Azure Security Center. Security Center will recommend that you restrict access through internet-facing endpoints if any of your network security groups has one or more inbound rules that allow access from “any” source IP address. Security Center will recommend that you edit these inbound rules to [restrict access](../../security-center/security-center-network-recommendations.md) to source IP addresses that actually need access.

**Best practice**: Restrict management ports (RDP, SSH).   
**Detail**: [Just-in-time (JIT) VM access](../../security-center/security-center-just-in-time.md) can be used to lock down inbound traffic to your Azure VMs, reducing exposure to attacks while providing easy access to connect to VMs when needed. When JIT is enabled, Security Center locks down inbound traffic to your Azure VMs by creating a network security group rule. Você seleciona as portas na VM para as quais o tráfego de entrada será bloqueado. These ports are controlled by the JIT solution.

## <a name="next-steps"></a>Próximos passos
Consulte [Melhores práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – o local em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou por email para secure@microsoft.com
