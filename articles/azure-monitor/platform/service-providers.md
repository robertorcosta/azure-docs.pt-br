---
title: Logs de monitores do Azure para provedores de serviços | Microsoft Docs
description: Os Logs do Monitor do Azure podem ajudar os MSPs (Managed Service Providers, grandes empresas, fornecedores independentes de software e provedores de serviços de hospedagem) a gerenciar e monitorar servidores no local ou na infra-estrutura de nuvem do cliente.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658873"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Logs de monitor do Azure para provedores de serviços

Os espaços de trabalho do Log Analytics no Azure Monitor podem ajudar os provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores independentes de software (ISVs) e provedores de serviços de hospedagem gerenciam e monitoram servidores no local ou na infra-estrutura de nuvem do cliente.

Grandes empresas compartilham várias semelhanças com provedores de serviços, especialmente quando há uma equipe de TI centralizada que é responsável por gerenciar a TI para muitas unidades de negócios diferentes. Para simplificar, este documento usa o termo *provedor*, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e provedores de serviços que fazem parte do programa [Cloud Solution Provider (CSP),](https://partner.microsoft.com/Solutions/cloud-reseller-overview) o Log Analytics no Azure Monitor é um dos serviços do Azure disponíveis nas assinaturas do Azure CSP.

O Log Analytics no Azure Monitor também pode ser usado por um provedor de serviços gerenciando os recursos do cliente através do recurso de gerenciamento de recursos delegado do Azure no [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Arquiteturas para provedores de serviços

Os espaços de trabalho do Log Analytics fornecem um método para o administrador controlar o fluxo e o isolamento dos dados de [log](data-platform-logs.md) e criar uma arquitetura que atenda às suas necessidades específicas de negócios. [Este artigo](design-logs-deployment.md) explica as considerações de design, implantação e migração para um espaço de trabalho, e o artigo [de acesso gerenciado](manage-access.md) discute como aplicar e gerenciar permissões para registrar dados. Os provedores de serviços têm considerações adicionais.

Há três arquiteturas possíveis para os provedores de serviços em relação aos workspaces do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuído - Os registros são armazenados em espaços de trabalho localizados no inquilino do cliente

Nesta arquitetura, um workspace é implantado no locatário do cliente que é usado para todos os logs desse cliente.

Existem duas maneiras pelas quais os administradores de provedores de serviços podem obter acesso a um espaço de trabalho do Log Analytics em um inquilino do cliente:

- Um cliente pode adicionar usuários individuais do provedor de serviços como [usuários convidados do Azure Active Directory (B2B).](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) Os administradores do provedor de serviços terão que fazer login no diretório de cada cliente no portal Azure para poder acessar esses espaços de trabalho. Isso também exige que os clientes gerenciem o acesso individual para cada administrador do provedor de serviços.
- Para maior escalabilidade e flexibilidade, os provedores de serviços podem usar o recurso de gerenciamento de [recursos delegado](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) do [Azure para](https://docs.microsoft.com/azure/lighthouse/overview) acessar o inquilino do cliente. Com esse método, os administradores do provedor de serviços são incluídos em um grupo de usuários Azure AD no inquilino do provedor de serviços, e esse grupo tem acesso durante o processo de onboarding para cada cliente. Esses administradores podem então acessar os espaços de trabalho de cada cliente a partir de seu próprio inquilino provedor de serviços, em vez de ter que fazer login no inquilino de cada cliente individualmente. Acessar os recursos dos espaços de trabalho log analytics de seus clientes reduz assim o trabalho necessário no lado do cliente e pode facilitar a coleta e análise de dados em vários clientes gerenciados pelo mesmo provedor de serviços através de ferramentas como [o Azure Monitor Workbooks](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Para obter mais informações, consulte [Monitorar os recursos do cliente em escala](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

As vantagens da arquitetura distribuída são:

* O cliente pode confirmar níveis específicos de permissões via gerenciamento de recursos delegados do [Azure,](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)ou pode gerenciar o acesso aos logs usando seu próprio [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Os logs podem ser coletados de todos os tipos de recursos, não apenas dados vm baseados em agentes. Por exemplo, os Logs de Auditoria do Azure.
* Cada cliente pode ter configurações diferentes para seu workspace, como retenção e limitação de dados.
* Isolamento entre os clientes para fins de regulamentação e conformidade.
* O encargo de cada workspace será distribuído para a assinatura do cliente.

As desvantagens da arquitetura distribuída são:

* Visualizar e analisar dados centralmente entre os inquilinos dos clientes com ferramentas como o Azure Monitor Workbooks pode resultar em experiências mais lentas, especialmente quando se analisa dados em mais de 50 espaços de trabalho.
* Se os clientes não estiverem a bordo para o gerenciamento de recursos delegado do Azure, os administradores dos provedores de serviços devem ser provisionados no diretório do cliente, e é mais difícil para o provedor de serviços gerenciar um grande número de inquilinos de clientes de uma só vez.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central - Os logs são armazenados em um espaço de trabalho localizado no inquilino do provedor de serviços

Nessa arquitetura, os logs não são armazenados nos locatários do cliente, mas apenas em um local central em uma das assinaturas do provedor de serviços. Os agentes que estão instalados nas VMs do cliente são configurados para enviar seus logs para esse workspace usando a ID de workspace e a chave secreta.

As vantagens da arquitetura centralizada são:

* É fácil gerenciar um grande número de clientes e integrá-los aos vários sistemas de back-end.
* O provedor de serviços tem propriedade total sobre os logs e os vários artefatos, como funções e consultas salvas.
* O provedor de serviços pode executar a análise em todos os clientes.

As desvantagens da arquitetura centralizada são:

* Essa arquitetura é aplicável somente a dados de VM baseados em agente; ela não aborda fontes de dados de PaaS, SaaS e de malha do Azure.
* Talvez seja difícil separar os dados entre os clientes quando eles são mesclados em um único workspace. O único método satisfatório para fazer isso é usar o FQDN (nome de domínio totalmente qualificado) do computador ou por meio da ID da assinatura do Azure. 
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única fatura e as mesmas definições de retenção e configuração.
* Os serviços de PaaS e de malha do Azure, como o Diagnóstico do Azure e os Logs de Auditoria do Azure, exigem que o workspace esteja no mesmo locatário do recurso; portanto, eles não podem enviar os logs para o workspace central.
* Todos os agentes de VM de todos os clientes serão autenticados no workspace central usando a mesma ID e chave do workspace. Não há nenhum método para bloquear os logs de um cliente específico sem interromper outros clientes.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido - Os registros são armazenados em um espaço de trabalho localizado no inquilino do cliente e alguns deles são puxados para um local central.

A terceira combinação de arquitetura entre as duas opções. Ela se baseia na primeira arquitetura distribuída, na qual os logs são locais para cada cliente, mas usando um mecanismo para criar um repositório central de logs. Uma parte dos logs é inserida em um local central para relatórios e análise. Essa parte pode ser uma pequena quantidade de tipos de dados ou um resumo da atividade, como estatísticas diárias.

Existem duas opções para implementar logs em um local central:

1. Workspace central: o provedor de serviços pode criar um workspace em seu locatário e usar um script que utiliza a [API de Consulta](https://dev.loganalytics.io/) com a [API de Coleta de Dados](../../azure-monitor/platform/data-collector-api.md) para levar os dados de vários workspaces para esse local central. Outra opção, além de um script, é usar os [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI como um local central: O POWER BI pode atuar como o local central quando os vários espaços de trabalho exportam dados para ele usando a integração entre o espaço de trabalho Log Analytics e o [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Próximas etapas

* Automatizar a criação e configuração de workspaces usando [modelos do Gerenciador de recursos](template-workspace-configuration.md)

* Automatizar a criação de workspaces usando o [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Use [Alertas](../../azure-monitor/platform/alerts-overview.md) para integrar com sistemas existentes

* Gerar relatórios de resumo usando o [Power BI](../../azure-monitor/platform/powerbi.md)

* Clientes a bordo do [Azure delegam gerenciamento de recursos](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
