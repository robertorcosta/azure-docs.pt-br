---
title: Azure AD Domain Services para provedores de soluções em nuvem | Microsoft Docs
description: Saiba como ativar e gerenciar os domínios gerenciados do Azure Active Directory Domain Services para provedores de soluções de nuvem do Azure
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519097"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Implantação e gerenciamento de serviços de domínio do azure Active Directory para provedores de soluções em nuvem do Azure

O Azure Cloud Solution Providers (CSP) é um programa para parceiros da Microsoft e fornece um canal de licença para vários serviços de nuvem da Microsoft. O Azure CSP permite que os parceiros gerenciem vendas, tenham a propriedade da relação de cobrança, ofereçam suporte técnico e de cobrança e sejam o único ponto de contato dos clientes. Além disso, o Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal de autoatendimento e APIs que o acompanha. Essas ferramentas permitem que os parceiros CSP facilmente provisionem e gerenciem recursos do Azure e forneçam a cobrança para os clientes e suas assinaturas.

O [portal Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) é o ponto de entrada para todos os parceiros CSP do Azure e fornece recursos ricos de gerenciamento de clientes, processamento automatizado e muito mais. Parceiros Azure CSP podem usar recursos do Centro de Parceiros usando uma interface de usuário baseada na Web ou usando o PowerShell e várias chamadas de API.

O diagrama a seguir ilustra como o modelo CSP funciona em um alto nível. Aqui, Contoso tem um inquilino do Azure Active Directory (Azure AD). Eles têm uma parceria com um CSP, que implanta e gerencia os recursos na sua assinatura do Azure CSP. A Contoso também pode ter assinaturas regulares (diretas) do Azure, que são cobradas diretamente para a Contoso.

![Visão geral do modelo CSP](./media/csp/csp_model_overview.png)

O inquilino do parceiro CSP tem três grupos de agentes especiais - agentes *admin,* agentes *de helpdesk* e agentes *de vendas.*

O grupo de agentes *administradores* é designado para a função de administrador inquilino no inquilino Azure AD de Contoso. Como resultado, um usuário pertencente ao grupo de agentes administrativos do parceiro CSP tem privilégios de administrador de inquilinos no inquilino Azure AD de Contoso.

Quando o parceiro CSP provisiona uma assinatura do Azure CSP para a Contoso, seu grupo de agentes administradores é atribuído à função de proprietário para essa assinatura. Como resultado, os agentes administradores do parceiro CSP possuem os privilégios necessários para provisionar recursos do Azure como máquinas virtuais, redes virtuais e Azure Active Directory Domain Services em nome da Contoso.

Para saber mais, confira a [Visão geral do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Benefícios de usar o Azure AD DS em uma assinatura CSP do Azure

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como participação em domínios, política de grupo, autenticação LDAP, Kerberos/NTLM totalmente compatível com os Serviços de Domínio do Diretório Ativo do Windows Server. Por décadas, muitos aplicativos foram criados para funcionar no AD usando esses recursos. Muitos fornecedores de software independentes (ISVs) têm criado e implantado aplicativos nos locais de seus clientes. Esses aplicativos são difíceis de suportar, uma vez que muitas vezes você exige acesso aos diferentes ambientes onde os aplicativos são implantados. Com assinaturas de Azure CSP, você tem uma alternativa mais simples com a escala e a flexibilidade do Azure.

O Azure AD DS suporta assinaturas Do Azure CSP. Você pode implantar seu aplicativo em uma assinatura CSP do Azure vinculada ao inquilino Azure AD do seu cliente. Como resultado, seus funcionários (equipe de suporte) podem gerenciar, administrar e atender as VMs nas quais seu aplicativo é implantado usando as credenciais corporativas da sua organização.

Você também pode implantar um domínio gerenciado pelo Azure AD DS no inquilino Azure AD do seu cliente. Seu aplicativo é então conectado ao domínio gerenciado do seu cliente. Os recursos dentro do seu aplicativo que dependem do Kerberos / NTLM, LDAP ou da [API System.DirectoryServices](/dotnet/api/system.directoryservices) funcionam perfeitamente contra o domínio do seu cliente. Os clientes finais se beneficiam de consumir seu aplicativo como um serviço, sem precisar se preocupar em manter a infra-estrutura em que o aplicativo está implantado.

Todo o faturamento pelos recursos do Azure que você consome nessa assinatura, incluindo o Azure AD DS, é cobrado de volta para você. Você mantém o controle total sobre o relacionamento com o cliente quando se trata de vendas, faturamento, suporte técnico etc. Com a flexibilidade da plataforma CSP do Azure, uma pequena equipe de agentes de suporte pode atender muitos desses clientes que têm instâncias de seu aplicativo implantados.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modelos de implantação de CSP para Azure AD DS

Existem duas maneiras pelas quais você pode usar o Azure AD DS com uma assinatura CSP do Azure. Escolha a correta com base nas considerações com segurança e simplicidade que seus clientes têm.

### <a name="direct-deployment-model"></a>Modelo de implantação direta

Neste modelo de implantação, o Azure AD DS está habilitado dentro de uma rede virtual que pertence à assinatura CSP do Azure. Agentes administradores do parceiro CSP têm os privilégios a seguir:

* *Privilégios de administrador* global no inquilino Azure AD do cliente.
* *Privilégios do proprietário da assinatura* na assinatura Do Azure CSP.

![Modelo de implantação direta](./media/csp/csp_direct_deployment_model.png)

Nesse modelo de implantação, os agentes administradores do provedor CSP podem administrar identidades para o cliente. Esses agentes de administrador podem executar tarefas como provisionar novos usuários ou grupos ou adicionar aplicativos dentro do inquilino Azure AD do cliente.

Esse modelo de implantação pode ser adequado para organizações menores que não possuem um administrador de identidade dedicado ou preferem que o parceiro CSP administre identidades em seu nome.

### <a name="peered-deployment-model"></a>Modelo de implantação emparelhada

Neste modelo de implantação, o Azure AD DS é habilitado dentro de uma rede virtual pertencente ao cliente - uma assinatura direta do Azure paga pelo cliente. O parceiro CSP pode implantar aplicativos dentro de uma rede virtual pertencente à assinatura CSP do cliente. As redes virtuais podem ser conectadas usando o emparelhamento de rede virtual do Azure.

Com essa implantação, as cargas de trabalho ou aplicativos implantados pelo parceiro CSP na assinatura CSP do Azure podem se conectar ao domínio gerenciado do cliente provisionado na assinatura direta do Azure do cliente.

![Modelo de implantação emparelhada](./media/csp/csp_peered_deployment_model.png)

Esse modelo de implantação fornece uma separação de privilégios e permite que os agentes de suporte técnico do parceiro CSP administrem a assinatura do Azure e implantem e gerenciar recursos dentro dele. No entanto, os agentes de helpdesk do parceiro CSP não precisam ter privilégios de administrador global no diretório Azure AD do cliente. Os administradores de identidades do cliente podem continuar a gerenciar identidades para sua organização.

Esse modelo de implantação pode ser adequado para cenários em que um ISV forneça uma versão hospedada de seu aplicativo local, que também precisa se conectar ao Azure AD do cliente.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrar o Azure AD DS em assinaturas de CSP

As considerações importantes a seguir se aplicam ao administrar um domínio gerenciado em uma assinatura do Azure CSP:

* **Os agentes admin CSP podem provisionamento de um domínio gerenciado usando suas credenciais:** O Azure AD DS suporta assinaturas Do Azure CSP. Os usuários pertencentes ao grupo de agentes de administrador esporárcidos de um parceiro CSP podem prover um novo domínio gerenciado pelo Azure AD DS.

* **Os CSPs podem script criar novos domínios gerenciados para seus clientes usando o PowerShell:** Veja [como ativar o Azure AD DS usando o PowerShell](powershell-create-instance.md) para obter detalhes.

* **Os agentes admin CSP não podem executar tarefas de gerenciamento contínuas no domínio gerenciado usando suas credenciais:** Os usuários de administradores de CSP não podem executar tarefas de gerenciamento de rotina dentro do domínio gerenciado usando suas credenciais. Esses usuários são externos ao inquilino Azure AD do cliente e suas credenciais não estão disponíveis dentro do inquilino Azure AD do cliente. O Azure AD DS não tem acesso aos hashes de senha Kerberos e NTLM para esses usuários, portanto os usuários não podem ser autenticados em domínios gerenciados pelo Azure AD DS.

  > [!WARNING]
  > Você deve criar uma conta de usuário no diretório do cliente para executar tarefas de administração contínuas no domínio gerenciado.
  >
  > Você não pode entrar no domínio gerenciado usando as credenciais de um usuário de admin CSP. Use as credenciais de uma conta de usuário pertencente ao inquilino Azure AD do cliente para fazê-lo. Você precisa dessas credenciais para tarefas como juntar VMs ao domínio gerenciado, administrar DNS ou administrar a Política de Grupo.

* **A conta de usuário criada para administração contínua deve ser adicionada ao grupo *Administradores AAD DC:* ** O grupo *Administradores AAD DC* tem privilégios para executar certas tarefas de administração delegadas no domínio gerenciado. Essas tarefas incluem a configuração do DNS, a criação de unidades organizacionais e a administração da política de grupo.
    
    Para que um parceiro CSP execute essas tarefas em um domínio gerenciado, uma conta de usuário deve ser criada dentro do inquilino Azure AD do cliente. As credenciais para esta conta devem ser compartilhadas com agentes administradores do parceiro CSP. Além disso, essa conta de usuário deve ser adicionada ao grupo *Administradores AAD DC* para permitir que as tarefas de configuração no domínio gerenciado sejam executadas usando essa conta de usuário.

## <a name="next-steps"></a>Próximas etapas

Para começar, [inscreva-se no programa Azure CSP](/partner-center/enrolling-in-the-csp-program). Em seguida, você pode habilitar os Serviços de Domínio Azure AD usando [o portal Azure](tutorial-create-instance.md) ou [o Azure PowerShell](powershell-create-instance.md).
