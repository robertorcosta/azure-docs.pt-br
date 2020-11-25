---
title: Notas sobre a versão da Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2020
ms.author: memildin
ms.openlocfilehash: 9b715ea890c7c85161a9e360bc16f9a2a608d64b
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95320994"
---
# <a name="whats-new-in-azure-security-center"></a>Novidades na Central de Segurança do Azure

A Central de Segurança está em desenvolvimento ativo e recebe aprimoramentos continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece informações sobre novos recursos, correções de bug e funcionalidades preteridas.

Esta página é atualizada com frequência, então acesse-a regularmente. 

Para saber mais sobre as alterações *planejadas* chegando em breve à Central de Segurança, consulte [Alterações importantes na Central de Segurança do Azure](upcoming-changes.md). 

> [!TIP]
> Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo na Central de Segurança do Azure](release-notes-archive.md).


## <a name="november-2020"></a>Novembro de 2020

As atualizações de novembro incluem:

- [29 recomendações de versão prévia adicionadas para aumentar a cobertura do Parâmetro de Comparação de Segurança do Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 adicionado ao painel de conformidade regulatória da Central de Segurança](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [A lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)
- [Experiência de provisionamento automático aprimorada e expandida](#auto-provisioning-experience-improved-and-expanded)
- [A classificação de segurança já está disponível na exportação contínua (versão prévia)](#secure-score-is-now-available-in-continuous-export-preview)
- [A recomendação "As atualizações do sistema devem ser instaladas nos computadores" agora inclui sub-recomendações](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 recomendações de versão prévia adicionadas para aumentar a cobertura do Parâmetro de Comparação de Segurança do Azure

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md).

As 29 recomendações de versão prévia a seguir foram adicionadas à Central de Segurança para aumentar a cobertura desse parâmetro de comparação.

As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

| Controle de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criptografar dados em trânsito              | – Impor conexão SSL deve ser habilitado para servidores de banco de dados PostgreSQL<br>– Impor conexão SSL deve ser habilitado para servidores de banco de dados MySQL<br>– O TLS deve estar atualizado com a última versão para o aplicativo de API<br>– O TLS deve estar atualizado com a última versão para o aplicativo de funções<br>– O TLS deve estar atualizado com a última versão para o aplicativo Web<br>– O FTPS deve ser exigido no aplicativo de API<br>– O FTPS deve ser exigido no aplicativo de funções<br>– O FTPS deve ser exigido no aplicativo Web                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gerenciar acesso e permissões        | – Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada<br>– A identidade gerenciada deve ser usada no aplicativo de API<br>– A identidade gerenciada deve ser usada no aplicativo de funções<br>– A identidade gerenciada deve ser usada no aplicativo Web                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restringir acesso não autorizado à rede | – O ponto de extremidade privado deve ser habilitado para servidores PostgreSQL<br>– O ponto de extremidade privado deve ser habilitado para servidores MariaDB<br>– O ponto de extremidade privado deve ser habilitado para servidores MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| – Habilitar a auditoria e o registro em log          | – Os logs de diagnóstico devem ser habilitados nos Serviços de Aplicativo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementar melhores práticas de segurança    | – O Backup do Azure deve ser habilitado para máquinas virtuais<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL<br>– O PHP deve estar atualizado com a última versão para o aplicativo de API<br>– O PHP deve estar atualizado com a última versão para o aplicativo Web<br>– O Java deve estar atualizado com a última versão para o aplicativo de API<br>– O Java deve estar atualizado com a última versão para o aplicativo de funções<br>– O Java deve estar atualizado com a última versão para o aplicativo Web<br>– O Python deve estar atualizado com a última versão para o aplicativo de API<br>– O Python deve estar atualizado com a última versão para o aplicativo de funções<br>– O Python deve estar atualizado com a última versão para o aplicativo Web<br>– A retenção de auditoria para servidores SQL deve ser definida como pelo menos 90 dias |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre aplicativos de API do Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicativos de funções do Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre aplicativos Web do Azure](../app-service/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 adicionado ao painel de conformidade regulatória da Central de Segurança

O padrão NIST SP 800-171 R2 já está disponível como uma iniciativa interna para uso com o painel de conformidade regulatória da Central de Segurança do Azure. Os mapeamentos para os controles são descritos em [Detalhes da iniciativa interna de Conformidade Regulatória do NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Para aplicar o padrão às suas assinaturas e monitorar continuamente o status de conformidade, use as instruções em [Como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="O padrão NIST SP 800 171 R2 no painel de conformidade regulatória da Central de Segurança":::

Para obter mais informações sobre esse padrão de conformidade, confira [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>A lista de recomendações agora inclui filtros

Agora você pode filtrar a lista de recomendações de segurança com base em uma gama de critérios. No seguinte exemplo, a lista de recomendações foi filtrada para mostrar recomendações que:

- estão **em disponibilidade geral** (ou seja, não estão em versão prévia)
- são voltadas para **contas de armazenamento**
- têm suporte para **correção rápida**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtros para a lista de recomendações":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Experiência de provisionamento automático aprimorada e expandida

O recurso de provisionamento automático ajuda a reduzir a sobrecarga de gerenciamento instalando as extensões necessárias em VMs novas e existentes do Azure, a fim de que elas possam se beneficiar das proteções da Central de Segurança. 

À medida que a Central de Segurança do Azure cresce, mais extensões são desenvolvidas e é possível monitorar uma lista maior de tipos de recursos. As ferramentas de provisionamento automático foram expandidas para dar suporte a extensões e tipos de recursos adicionais, aproveitando os recursos do Azure Policy.

Agora você pode configurar o provisionamento automático de:

- Agente do Log Analytics
- (Novo) Complemento do Azure Policy para Kubernetes
- (Novo) Microsoft Dependency Agent

Saiba mais em [Agentes e extensões de provisionamento automático da Central de Segurança do Azure](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A classificação de segurança já está disponível na exportação contínua (versão prévia)

Com a exportação contínua da classificação de segurança, você pode transmitir alterações à sua pontuação em tempo real para os Hubs de Eventos do Azure ou um workspace do Log Analytics. Use essa funcionalidade para:

- acompanhar sua classificação de segurança ao longo do tempo com relatórios dinâmicos
- exportar dados de classificação de segurança para o Azure Sentinel (ou qualquer outro SIEM)
- integrar esses dados a qualquer processo que você já esteja usando para monitorar a classificação de segurança em sua organização

Saiba mais sobre como [Exportar continuamente dados da Central de Segurança](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>A recomendação "As atualizações do sistema devem ser instaladas nos computadores" agora inclui sub-recomendações

A recomendação **As atualizações do sistema devem ser instaladas nos computadores** foi aprimorada. A nova versão inclui sub-recomendações para cada atualização ausente e traz os seguintes aprimoramentos:

- Uma experiência reformulada nas páginas da Central de Segurança do Azure do portal do Azure. A página de detalhes da recomendação para **As atualizações do sistema devem ser instaladas nos computadores** inclui a lista de conclusões, conforme mostrado abaixo. Quando você seleciona uma localização individual, o painel de detalhes é aberto com um link para as informações de correção e uma lista de recursos afetados.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Abrindo uma das sub-recomendações na experiência do portal para a recomendação atualizada":::

- Dados aprimorados para a recomendação do ARG (Azure Resource Graph). O ARG é um serviço do Azure que foi projetado para oferecer uma exploração eficiente de recursos. Você pode usar o ARG para consultar em escala um determinado conjunto de assinaturas a fim de controlar seu ambiente de maneira eficaz. 

    Para a Central de Segurança do Azure, você pode usar ARG e [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança.

    Anteriormente, se você consultasse essa recomendação no ARG, a única informação disponível seria que a recomendação precisava ser corrigida em um computador. A consulta a seguir da versão aprimorada retornará as atualizações de cada sistema ausente agrupadas por computador.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```



## <a name="october-2020"></a>Outubro de 2020

As atualizações de outubro incluem:
- [Avaliação de vulnerabilidade para computadores locais e em várias nuvens (versão prévia)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Recomendação do Firewall do Azure adicionada (versão prévia)](#azure-firewall-recommendation-added-preview)
- [Recomendação Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes atualizada com uma correção rápida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [O painel de conformidade regulatória agora inclui a opção de remoção de padrões](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabela Microsoft.Security/securityStatuses removida do ARG (Azure Resource Graph)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Avaliação de vulnerabilidade para computadores locais e em várias nuvens (versão prévia)

O avaliador de vulnerabilidades do [Azure Defender para servidores](defender-for-servers-introduction.md)' (da plataforma Qualys) agora verifica servidores habilitados para o Azure Arc.

Quando você tiver habilitado o Azure Arc em seus computadores que não são do Azure, a Central de Segurança oferecerá a implantação do verificador de vulnerabilidades integrado neles, manualmente e em escala.

Com essa atualização, você pode tirar proveito da eficiência do **Azure Defender para servidores** para consolidar seu programa de gerenciamento de vulnerabilidades em todos os seus ativos que são e que não são do Azure.

Principais recursos:

- Monitoramento do estado de provisionamento do verificador de VA (avaliação de vulnerabilidade) em computadores do Azure Arc
- Provisionamento do agente de VA integrado para computadores Windows e Linux do Azure Arc desprotegidos (manualmente e em escala)
- Recebimento e análise das vulnerabilidades detectadas por agentes implantados (manualmente e em escala)
- Experiência unificada para VMs do Azure e computadores do Azure Arc

[Saiba mais sobre como implantar o verificador de vulnerabilidades integrado em computadores híbridos](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Saiba mais sobre os servidores habilitados para Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Recomendação do Firewall do Azure adicionada (versão prévia)

Uma nova recomendação foi adicionada para proteger todas as suas redes virtuais com o Firewall do Azure.

A recomendação, **Redes virtuais devem ser protegidas pelo Firewall do Azure**, aconselha você a restringir o acesso às suas redes virtuais e evitar possíveis ameaças usando o Firewall do Azure.

Saiba mais sobre [Firewall do Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Recomendação Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes atualizada com uma correção rápida

A recomendação **Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes** agora tem uma opção de correção rápida.

Para obter mais detalhes sobre essa recomendação e sobre todas as outras recomendações da Central de Segurança, confira [Recomendações de segurança – um guia de referência](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Recomendação Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes com a opção de correção rápida":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>O painel de conformidade regulatória agora inclui a opção de remoção de padrões

O painel de conformidade regulatória da Central de Segurança fornece informações sobre sua postura de conformidade com base em como você está atendendo a requisitos e controles de conformidade específicos.

O painel inclui um conjunto padrão de padrões regulatórios. Se qualquer um dos padrões fornecidos não for relevante para sua organização, agora é um processo simples removê-los da interface do usuário para uma assinatura. Os padrões podem ser removidos somente no nível da *assinatura* não no escopo do grupo de gerenciamento.

Saiba mais em [Remover um padrão de seu painel](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabela Microsoft.Security/securityStatuses removida do ARG (Azure Resource Graph)

O Azure Resource Graph é um serviço no Azure desenvolvido para fornecer exploração de recursos eficiente, com a capacidade de consultar em escala um determinado conjunto de assinaturas, permitindo a você controlar o seu ambiente de maneira efetiva. 

Para a Central de Segurança do Azure, você pode usar ARG e [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança. Por exemplo:

- Utilizações de inventário de ativos (ARG)
- Documentamos um exemplo de consulta do ARG para saber como [Identificar contas sem a MFA (autenticação multifator) habilitada](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Dentro do ARG, há tabelas de dados que você pode usar em suas consultas.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer e as tabelas disponíveis":::

> [!TIP]
> A documentação do ARG lista todas as tabelas disponíveis na [Referência de tabela e tipo de recurso do Azure Resource Graph](../governance/resource-graph/reference/supported-tables-resources.md).

Nessa atualização, a tabela **Microsoft.Security/securityStatuses** foi removida. A API securityStatuses ainda está disponível.

A substituição de dados pode ser usada pela tabela Microsoft.Security/Assessments.

A principal diferença entre Microsoft.Security/securityStatuses e Microsoft.Security/Assessments é que, enquanto o primeiro mostra a agregação de avaliações, o segundo mantém um registro para cada uma.

Por exemplo, Microsoft.Security/securityStatuses retornaria um resultado com uma matriz de duas policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Por sua vez, Microsoft.Security/Assessments manterá um registro para cada avaliação de política, da seguinte maneira:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exemplo de conversão de uma consulta de ARG existente usando securityStatuses para usar a tabela de avaliações:**

Consulta que faz referência a SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Consulta de substituição para a tabela de Avaliações:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Saiba mais consultando os seguintes links:
- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Setembro de 2020

As atualizações de setembro incluem:
- [A Central de Segurança ganha uma nova aparência!](#security-center-gets-a-new-look)
- [Lançamento do Azure Defender](#azure-defender-released)
- [Azure Defender para Key Vault em disponibilidade geral](#azure-defender-for-key-vault-is-generally-available)
- [Proteção do Azure Defender para Armazenamento para os Arquivos e o ADLS Gen2 em disponibilidade geral](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Ferramentas de inventário de ativos em disponibilidade geral](#asset-inventory-tools-are-now-generally-available)
- [Desabilitar uma descoberta de vulnerabilidade específica nas verificações de registros de contêiner e máquinas virtuais](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Isentar um recurso de uma recomendação](#exempt-a-resource-from-a-recommendation)
- [Os conectores da AWS e do GCP na Central de Segurança trazem uma experiência de várias nuvens](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Pacote de recomendações da proteção de cargas de trabalho do Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Disponibilidade das descobertas da avaliação de vulnerabilidades na exportação contínua](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Impedir configurações incorretas de segurança impondo recomendações durante a criação de recursos](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aprimoramento das recomendações de grupo de segurança de rede](#network-security-group-recommendations-improved)
- [Reprovação da recomendação da versão prévia do AKS "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Aprimoramento nas notificações por email da Central de Segurança do Azure](#email-notifications-from-azure-security-center-improved)
- [A classificação de segurança não inclui recomendações de versão prévia](#secure-score-doesnt-include-preview-recommendations)
- [As recomendações agora incluem um indicador de severidade e o intervalo de atualização](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>A Central de Segurança ganha uma nova aparência!

Lançamos uma interface do usuário atualizada para as páginas do portal da Central de Segurança. As novas páginas incluem uma nova página de visão geral, bem como painéis para a classificação de segurança, o inventário de ativos e o Azure Defender.

A página de visão geral remodelada agora traz um bloco para acessar os painéis da classificação de segurança, do inventário de ativos e do Azure Defender. Também traz um bloco vinculado ao painel de conformidade regulatória.

Saiba mais sobre a [página de visão geral](overview-page.md).


### <a name="azure-defender-released"></a>Lançamento do Azure Defender

O **Azure Defender** é a PPCTN (plataforma de proteção de cargas de trabalho na nuvem) integrada à Central de Segurança para proteção avançada e inteligente das suas cargas de trabalho híbridas e do Azure. Ele substitui a opção de tipo de preço Standard da Central de Segurança. 

Quando você habilita o Azure Defender na área **Preços e configurações** da Central de Segurança do Azure, os seguintes planos do Defender são todos habilitados simultaneamente e fornecem proteções abrangentes para as camadas de computação, dados e serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender for Serviço de Aplicativo](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)

Cada um desses planos é explicado separadamente na documentação da Central de Segurança.

Com um painel dedicado, o Azure Defender fornece alertas de segurança e Proteção Avançada contra Ameaças para máquinas virtuais, bancos de dados SQL, contêineres, aplicativos Web, sua rede, entre outros.

[Saiba mais sobre o Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender para Key Vault em disponibilidade geral

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

O **Azure Defender para Key Vault** fornece proteção avançada e nativa do Azure contra ameaças para o Azure Key Vault, oferecendo uma camada adicional de inteligência de segurança. Por extensão, o Azure Defender para Key Vault está, consequentemente, protegendo muitos dos recursos dependentes das suas contas do Key Vault.

Agora, o plano opcional é GA. Esse recurso estava em versão prévia como "Proteção Avançada contra Ameaças para Azure Key Vault".

Além disso, as páginas do Key Vault no portal do Azure agora incluem uma página **Segurança** dedicada para as recomendações e os alertas da **Central de Segurança**.

Saiba mais em [Azure Defender para Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Proteção do Azure Defender para Armazenamento para os Arquivos e o ADLS Gen2 em disponibilidade geral 

O **Azure Defender para Armazenamento** detecta atividades potencialmente prejudiciais nas contas do Armazenamento do Azure. Seus dados podem ser protegidos independentemente de estarem armazenados como contêineres de blob, compartilhamentos de arquivos ou data lakes.

O suporte para os [Arquivos do Azure](../storage/files/storage-files-introduction.md) e o [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) já está em disponibilidade geral.

Desde 1º de outubro de 2020, começamos a cobrar pela proteção dos recursos nesses serviços.

Saiba mais em [Azure Defender para Armazenamento](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Ferramentas de inventário de ativos em disponibilidade geral

A página de inventário de ativos da Central de Segurança do Azure fornece uma só página para exibição da postura de segurança dos recursos que você conectou à Central de Segurança.

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades.

Quando qualquer recurso tiver recomendações pendentes, eles serão exibidos no inventário.

Saiba mais em [Explorar e gerenciar seus recursos com as ferramentas de gerenciamento e inventário de ativos](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Desabilitar uma descoberta de vulnerabilidade específica nas verificações de registros de contêiner e máquinas virtuais

O Azure Defender inclui verificadores de vulnerabilidade para examinar imagens no seu Registro de Contêiner do Azure e nas suas máquinas virtuais.

Caso você tenha uma necessidade organizacional para ignorar uma descoberta, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As descobertas desabilitadas não afetam sua classificação de segurança nem geram um ruído indesejado.

Quando uma descoberta corresponde aos critérios definidos nas regras de desabilitação, ela não será exibida na lista de descobertas.

Essa opção está disponível nas páginas de detalhes de recomendações para:

- **As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas**
- **As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**

Saiba mais em [Desabilitar descobertas específicas nas imagens de contêiner](defender-for-container-registries-usage.md#disable-specific-findings-preview) e [Desabilitar descobertas específicas nas máquinas virtuais](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Isentar um recurso de uma recomendação

Ocasionalmente, um recurso será listado como não íntegro em relação a uma recomendação específica (e, portanto, reduzindo sua classificação de segurança) mesmo que você ache que ele não deveria ser. Ele pode ter sido corrigido por um processo não acompanhado pela Central de Segurança. Ou, talvez, sua organização tenha decidido aceitar o risco para esse recurso específico. 

Nesses casos, você pode criar uma regra de isenção e garantir que o recurso não seja listado entre os recursos não íntegros no futuro. Essas regras podem incluir justificações documentadas, conforme descrito abaixo.

Saiba mais em [Isentar um recurso das recomendações e da classificação de segurança](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Os conectores da AWS e do GCP na Central de Segurança trazem uma experiência de várias nuvens

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure agora protege as cargas de trabalho no Azure, a AWS (Amazon Web Services) e o GCP (Google Cloud Platform).

A integração das suas contas da AWS e do GCP à Central de Segurança integra o AWS Security Hub, o GCP Security Command e a Central de Segurança do Azure. 

Saiba mais em [Conectar suas contas da AWS à Central de Segurança do Azure](quickstart-onboard-aws.md) e [Conectar suas contas do GCP à Central de Segurança do Azure](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Pacote de recomendações da proteção de cargas de trabalho do Kubernetes

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança está adicionando recomendações de proteção no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

Quando você instalar o complemento do Azure Policy para Kubernetes no seu cluster do AKS, todas as solicitações para o servidor de API do Kubernetes serão monitoradas em relação ao conjunto predefinido de melhores práticas antes de serem persistidas no cluster. Em seguida, você pode configurá-lo para impor as melhores práticas e exigir o uso dele em cargas de trabalho futuras.

Por exemplo, você poderá proibir a criação de contêineres privilegiados, e todas as futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Disponibilidade das descobertas da avaliação de vulnerabilidades na exportação contínua

Use a exportação contínua para transmitir alertas e recomendações em tempo real para os Hubs de Eventos do Azure, os workspaces do Log Analytics ou o Azure Monitor. Neles, você pode integrar esses dados aos SIEMs (como o Azure Sentinel, o Power BI, o Azure Data Explorer, entre outros).

As ferramentas integradas de avaliação de vulnerabilidades da Central de Segurança retornam descobertas sobre seus recursos como recomendações práticas em uma recomendação "pai", por exemplo, "As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas". 

As descobertas de segurança já estão disponíveis para exportação por meio da exportação contínua quando você seleciona as recomendações e habilita a opção **Incluir descobertas de segurança**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir a alternância de descobertas de segurança na configuração de exportação contínua" :::

Páginas relacionadas:

- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md)
- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para imagens do Registro de Contêiner do Azure](defender-for-container-registries-usage.md)
- [Exportação contínua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Impedir configurações incorretas de segurança impondo recomendações durante a criação de recursos

Configurações incorretas de segurança são uma das principais causas de incidentes de segurança. Agora, a Central de Segurança pode ajudar a *impedir* configurações incorretas de novos recursos, com relação a recomendações específicas. 

Esse recurso pode ajudar a manter suas cargas de trabalho seguras e estabilizar sua classificação de segurança.

A imposição de uma configuração segura, com base em uma recomendação específica, é oferecida em dois modos:

- Usando o efeito **Negar** do Azure Policy, você pode interromper a criação de recursos não íntegros

- Usando a opção **Impor**, você pode aproveitar o efeito **DeployIfNotExist** da política do Azure e corrigir automaticamente os recursos fora de conformidade após a criação
 
Isso está disponível para as recomendações de segurança selecionadas e pode ser encontrado na parte superior da página de detalhes do recurso.

Saiba mais em [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aprimoramento das recomendações de grupo de segurança de rede

As recomendações de segurança a seguir relacionadas aos grupos de segurança de rede foram aprimoradas para reduzir algumas instâncias de falsos positivos.

- Todas as portas de rede devem ser restritas no NSG associado à sua VM
- Portas de gerenciamento devem ser fechadas nas máquinas virtuais
- As máquinas virtuais para a Internet devem ser protegidas com Grupos de Segurança de Rede
- As sub-redes devem ser associadas a um Grupo de Segurança de Rede


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Reprovação da recomendação da versão prévia do AKS "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes"

A recomendação de versão prévia "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes" está sendo preterida, conforme descrito na documentação do [Serviço de Kubernetes do Azure](../aks/use-pod-security-policies.md).

O recurso de política de segurança de pods (versão prévia) está definido para reprovação e não estará mais disponível após 15 de outubro de 2020 em favor do Azure Policy para AKS.

Depois que a política de segurança de pods (versão prévia) for preterida, você precisará desabilitar o recurso em todos os clusters existentes usando o recurso preterido para realizar futuras atualizações de cluster e permanecer dentro do suporte do Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Aprimoramento nas notificações por email da Central de Segurança do Azure

As seguintes áreas dos emails relacionadas a alertas de segurança foram aprimoradas: 

- Adição da capacidade de enviar notificações por email sobre alertas para todos os níveis de severidade
- Adição da capacidade de notificar os usuários com diferentes funções do Azure na assinatura
- Por padrão, estamos notificando de maneira proativa os proprietários da assinatura em alertas de alta severidade (que têm uma alta probabilidade de serem violações autênticas)
- Removemos o campo de número de telefone da página de configuração das notificações de email

Saiba mais em [Configurar notificações por email para alertas de segurança](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A classificação de segurança não inclui recomendações de versão prévia 

A Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado.

À medida que novas ameaças são descobertas, novas orientações de segurança são disponibilizadas na Central de Segurança por meio de novas recomendações. Para evitar alterações de surpresa na sua classificação de segurança e fornecer um período de cortesia no qual você possa explorar novas recomendações antes que elas afetem suas pontuações, as recomendações sinalizadas como **Versão prévia** não serão mais incluídas nos cálculos da sua classificação de segurança. Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Além disso, as recomendações de **Versão prévia** não renderizam um recurso "Não íntegro".

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de versão prévia":::

[Saiba mais sobre a classificação de segurança](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>As recomendações agora incluem um indicador de severidade e o intervalo de atualização

A página de detalhes de recomendações agora inclui um indicador de intervalo de atualização (quando relevante) e uma exibição clara da severidade da recomendação.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Página de recomendações mostrando a atualização e a severidade":::



## <a name="august-2020"></a>Agosto de 2020

As atualizações de agosto incluem:

- [Inventário de ativos: nova exibição avançada da postura de segurança dos ativos](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Adição de suporte para padrões de segurança do Azure Active Directory (para autenticação multifator)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Adição da recomendação de entidades de serviço](#service-principals-recommendation-added)
- [Avaliação de vulnerabilidades em VMs – recomendações e políticas consolidadas](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Adição de novas políticas de segurança do AKS à iniciativa ASC_default – para uso somente dos clientes da versão prévia privada](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventário de ativos: nova exibição avançada da postura de segurança dos ativos

O inventário de ativos da Central de Segurança (atualmente em versão prévia) fornece uma forma de exibir a postura de segurança dos recursos que você conectou à Central de Segurança.

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades. Quando qualquer recurso tiver recomendações pendentes, eles serão exibidos no inventário.

Use a exibição e os respectivos filtros para explorar seus dados de postura de segurança e realizar ações adicionais com base nas descobertas.

Saiba mais sobre o [inventário de ativos](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Adição de suporte para padrões de segurança do Azure Active Directory (para autenticação multifator)

A Central de Segurança adicionou suporte completo para os [padrões de segurança](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), as proteções gratuitas de segurança de identidade da Microsoft.

Os padrões de segurança fornecem configurações de segurança de identidade pré-configuradas para defender sua organização contra ataques comuns relacionados à identidade. Os padrões de segurança já estão protegendo mais de 5 milhões de locatários em geral; 50 mil locatários também estão protegidos pela Central de Segurança.

Agora, a Central de Segurança fornece uma recomendação de segurança sempre que identifica uma assinatura do Azure sem os padrões de segurança habilitados. Até agora, a Central de Segurança recomenda habilitar a autenticação multifator usando o acesso condicional, que faz parte da licença Premium do Azure AD (Active Directory). Para os clientes que usam o Azure AD gratuito, agora recomendamos habilitar os padrões de segurança. 

Nossa meta é encorajar mais clientes a proteger ambientes de nuvem com a MFA e atenuar um dos maiores riscos que também é o mais impactante para a sua [classificação de segurança](secure-score-security-controls.md).

Saiba mais sobre segurança os [padrões de segurança](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Adição da recomendação de entidades de serviço

Uma nova recomendação foi adicionada a fim de aconselhar os clientes da Central de Segurança que usam certificados de gerenciamento a gerenciar as assinaturas alternem para as entidades de serviço.

A recomendação **As entidades de serviço devem ser usadas para proteger suas assinaturas em vez dos certificados de gerenciamento** aconselha você a usar as entidades de serviço ou o Azure Resource Manager para gerenciar suas assinaturas com mais segurança. 

Saiba mais sobre os [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Avaliação de vulnerabilidades em VMs – recomendações e políticas consolidadas

A Central de Segurança inspeciona suas VMs para detectar se estão executando uma solução de avaliação de vulnerabilidades. Se nenhuma solução de avaliação de vulnerabilidades for encontrada, a Central de Segurança fornecerá uma recomendação para simplificar a implantação.

Quando as vulnerabilidades são encontradas, a Central de Segurança fornece uma recomendação resumindo as descobertas para você investigar e corrigir, conforme necessário.

Para garantir uma experiência consistente para todos os usuários, independentemente do tipo de verificador que estão usando, unificamos quatro recomendações nas duas seguintes:

|Recomendação unificada|Descrição das alterações|
|----|:----|
|**Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais**|Substitui as duas seguintes recomendações:<br> **•** Habilitar a solução interna de avaliação de vulnerabilidades em máquinas virtuais (ativada pela Qualys) (agora preterida) (incluída na camada Standard)<br> **•** A solução de avaliação de vulnerabilidades deve ser instalada nas máquinas virtuais (agora preterida) (incluída nas camadas Standard e Gratuita)|
|**As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**|Substitui as duas seguintes recomendações:<br>**•** Corrija as vulnerabilidades encontradas nas máquinas virtuais (ativada pela Qualys) (agora preterida)<br>**•** As vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidades (agora preterida)|
|||

Agora, você usará a mesma recomendação para implantar a extensão de avaliação de vulnerabilidades da Central de Segurança ou uma solução de licença privada ("BYOL") de um parceiro, como a Qualys ou a Rapid7.

Além disso, quando as vulnerabilidades forem encontradas e relatadas à Central de Segurança, uma só recomendação alertará você sobre as descobertas, independentemente da solução de avaliação de vulnerabilidades que as identificou.

#### <a name="updating-dependencies"></a>Como atualizar dependências

Se você tiver scripts, consultas ou automações referentes às recomendações ou aos nomes/às chaves de política anteriores, use as tabelas abaixo para atualizar as referências:

##### <a name="before-august-2020"></a>Antes de agosto de 2020

|Recomendação|Escopo|
|----|:----|
|**Habilitar a solução interna de avaliação de vulnerabilidades nas máquinas virtuais (ativada pela Qualys)**<br>Chave: 550e890b-e652-4d22-8274-60b3bdb24c63|Interno|
|**Corrija as vulnerabilidades encontradas em suas máquinas virtuais (da plataforma Qualys)**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Interno|
|**A solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais**<br>Chave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades**<br>Chave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Política|Escopo|
|----|:----|
|**A avaliação de vulnerabilidades deve estar habilitada nas máquinas virtuais**<br>ID da política: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Interno|
|**As vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidades**<br>ID da política: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>A partir de agosto de 2020

|Recomendação|Escopo|
|----|:----|
|**Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais**<br>Chave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Interno + BYOL|
|**As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Interno + BYOL|
||||

|Política|Escopo|
|----|:----|
|[**A avaliação de vulnerabilidades deve estar habilitada nas máquinas virtuais**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID da política: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Interno + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Adição de novas políticas de segurança do AKS à iniciativa ASC_default – para uso somente dos clientes da versão prévia privada

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança está adicionando recomendações de proteção e políticas no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

A fase inicial deste projeto inclui uma versão prévia privada e a adição de novas políticas (desabilitadas por padrão) à iniciativa ASC_default.

Você poderá ignorar essas políticas com segurança e não haverá nenhum impacto no seu ambiente. Se você quiser habilitá-las, inscreva-se na versão prévia em https://aka.ms/SecurityPrP e escolha uma das seguintes opções:

1. **Versão prévia única**: para ingressar somente nessa versão prévia privada. Mencione explicitamente “Verificação contínua do ASC” como a versão prévia na qual deseja ingressar.
1. **Programas em andamento** – para ser adicionado a esta e às futuras versões prévias privadas. Será necessário preencher um perfil e um contrato de privacidade.


## <a name="july-2020"></a>Julho de 2020

As atualizações de julho incluem:
- [A avaliação de vulnerabilidades para máquinas virtuais já está disponível para imagens que não são do marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Expansão da proteção contra ameaças para o Armazenamento do Azure para incluir os Arquivos do Azure e o Azure Data Lake Storage Gen2 (versão prévia)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Oito novas recomendações para habilitar os recursos de proteção contra ameaças](#eight-new-recommendations-to-enable-threat-protection-features)
- [Aprimoramentos de segurança do contêiner – verificação mais rápida de registro e atualização da documentação](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Atualização de controles de aplicativos adaptáveis com uma nova recomendação e suporte para curingas nas regras de caminho](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Seis políticas para a reprovação da Segurança de Dados Avançada do SQL](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>A avaliação de vulnerabilidades para máquinas virtuais já está disponível para imagens que não são do marketplace

Ao implantar uma solução de avaliação de vulnerabilidades, a Central de Segurança realizou anteriormente uma verificação de validação antes da implantação. O objetivo da verificação era confirmar um SKU do marketplace da máquina virtual de destino. 

Desta atualização em diante, a verificação foi removida e agora você pode implantar ferramentas de avaliação de vulnerabilidades em computadores Windows e Linux 'personalizados'. As imagens personalizadas são aquelas que você modificou dos padrões do marketplace.

Embora você possa implantar a extensão integrada de avaliação de vulnerabilidades (ativada pela Qualys) em muitos outros computadores, o suporte só estará disponível se você estiver usando um sistema operacional listado em [Implantar o verificador de vulnerabilidades integrado em VMs da camada Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Saiba mais sobre o [verificador de vulnerabilidades integrado para máquinas virtuais (exige o Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Saiba mais sobre como usar sua solução de avaliação de vulnerabilidades de licença privada da Qualys ou da Rapid7 em [Como implantar uma solução de verificação de vulnerabilidades do parceiro](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Expansão da proteção contra ameaças para o Armazenamento do Azure para incluir os Arquivos do Azure e o Azure Data Lake Storage Gen2 (versão prévia)

A proteção contra ameaças para o Armazenamento do Azure detecta atividades potencialmente prejudiciais nas suas contas do Armazenamento do Azure. A Central de Segurança exibe alertas quando detecta tentativas de acessar ou explorar suas contas de armazenamento. 

Seus dados podem ser protegidos independentemente de estarem armazenados como contêineres de blob, compartilhamentos de arquivos ou data lakes.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Oito novas recomendações para habilitar os recursos de proteção contra ameaças

Oito novas recomendações foram adicionadas para fornecer uma forma simples de habilitar os recursos de proteção contra ameaças da Central de Segurança do Azure para os seguintes tipos de recursos: máquinas virtuais, Planos do Serviço de Aplicativo, servidores do Banco de Dados SQL do Azure, SQL Servers em computadores, contas do Armazenamento do Azure, clusters do Serviço de Kubernetes do Azure, registros do Registro de Contêiner do Azure e cofres do Azure Key Vault.

As novas recomendações são:

- **A Segurança de Dados Avançada deve ser habilitada nos servidores do Banco de Dados SQL do Azure**
- **A Segurança de Dados Avançada deve ser habilitada nos servidores SQL em computadores**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos planos do Serviço de Aplicativo do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos registros do Registro de Contêiner do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos cofres do Azure Key Vault**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos clusters do Serviço de Kubernetes do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada em contas de Armazenamento do Azure**
- **A Proteção Avançada contra Ameaças deve estar habilitada nas máquinas virtuais**

Essas novas recomendações pertencem ao controle de segurança **Habilitar o Azure Defender**.

As recomendações também incluem a funcionalidade de correção rápida. 

> [!IMPORTANT]
> A correção de uma dessas recomendações resultará em custos para proteger os recursos relevantes. Esses custos serão iniciados imediatamente se você tiver recursos relacionados na assinatura atual. Ou, no futuro, se você adicioná-los em uma data posterior.
> 
> Por exemplo, se você não tiver nenhum cluster do Serviço de Kubernetes do Azure na sua assinatura e habilitar a proteção contra ameaças, nenhum custo será cobrado. Se, no futuro, você adicionar um cluster na mesma assinatura, ela será automaticamente protegida e os custos serão iniciados nesse momento.

Saiba mais sobre cada um deles na [página de referência de recomendações de segurança](recommendations-reference.md).

Saiba mais sobre a [proteção contra ameaças na Central de Segurança do Azure](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Aprimoramentos de segurança do contêiner – verificação mais rápida de registro e atualização da documentação

Como parte dos investimentos contínuos no domínio de segurança do contêiner, temos o prazer em compartilhar um aprimoramento significativo de desempenho nas verificações dinâmicas da Central de Segurança de imagens de contêiner armazenadas no Registro de Contêiner do Azure. Agora, as verificações normalmente são concluídas em aproximadamente dois minutos. Em alguns casos, elas podem levar até 15 minutos.

Para aprimorar a clareza e as diretrizes sobre as funcionalidades de segurança do contêiner da Central de Segurança do Azure, também atualizamos as páginas de documentação de segurança do contêiner. 

Saiba mais sobre a segurança de contêiner da Central de Segurança nos seguintes artigos:

- [Visão geral de recursos de segurança de contêiner da Central de Segurança](container-security.md)
- [Detalhes da integração ao Registro de Contêiner do Azure](defender-for-container-registries-introduction.md)
- [Detalhes da integração ao Serviço de Kubernetes do Azure](defender-for-kubernetes-introduction.md)
- [Como verificar os registros e proteger os hosts do Docker](container-security.md)
- [Alertas de segurança dos recursos de proteção contra ameaças para clusters do Serviço de Kubernetes do Azure](alerts-reference.md#alerts-akscluster)
- [Alertas de segurança dos recursos de proteção contra ameaças para hosts do Serviço de Kubernetes do Azure](alerts-reference.md#alerts-containerhost)
- [Recomendações de segurança para contêineres](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Atualização de controles de aplicativos adaptáveis com uma nova recomendação e suporte para curingas nas regras de caminho

O recurso de controles de aplicativos adaptáveis recebeu duas atualizações significativas:

* Uma nova recomendação identifica o comportamento potencialmente legítimo que não era permitido antes. A nova recomendação, **As regras de lista de permissões na sua política de controle de aplicativos adaptáveis deve ser atualizada**, solicita que você adicione novas regras à política existente para reduzir o número de falsos positivos em alertas de violação de controles de aplicativos adaptáveis.

* Agora, as regras de caminho dão suporte a curingas. A partir dessa atualização, você pode configurar as regras de caminho permitidas usando curingas. Há dois cenários compatíveis:

    * Usando um curinga no final de um caminho para permitir todos os executáveis dentro desta pasta e das subpastas

    * Usando um curinga no meio de um caminho para habilitar um nome executável conhecido com um nome de pasta alterado (por exemplo, pastas de usuário pessoais com um executável conhecido, nomes de pasta gerados automaticamente etc.).


[Saiba mais sobre controles de aplicativo adaptáveis](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Seis políticas para a reprovação da Segurança de Dados Avançada do SQL

Seis políticas relacionadas à Segurança de Dados Avançada para computadores SQL estão sendo preteridas:

- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como 'Todos' nas configurações da Segurança de Dados Avançada da Instância Gerenciada de SQL
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como 'Todos' nas configurações da Segurança de Dados Avançada do SQL Server
- As configurações da Segurança de Dados Avançada para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança
- As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança
- As notificações por email para administradores e proprietários de assinatura devem ser habilitadas nas configurações da Segurança de Dados Avançada da instância gerenciada do SQL
- As notificações por email para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações da Segurança de Dados Avançada do SQL Server

Saiba mais sobre as [políticas internas](./policy-reference.md).





## <a name="june-2020"></a>Junho de 2020

As atualizações de junho incluem:
- [API de classificação de segurança (versão prévia)](#secure-score-api-preview)
- [Segurança de dados avançada para computadores SQL (Azure, outras nuvens e local) (versão prévia)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Duas novas recomendações para implantar o agente do Log Analytics em computadores do Azure Arc (versão prévia)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Novas políticas para criar configurações de exportação contínua e automação de fluxo de trabalho em escala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nova recomendação de uso de NSGs para proteger máquinas virtuais que não são voltadas para a Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Novas políticas para habilitar a proteção contra ameaças e a Segurança de Dados Avançada](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API de classificação de segurança (versão prévia)

Agora você pode acessar sua classificação por meio da [API de classificação de segurança](/rest/api/securitycenter/securescores/) (atualmente em versão prévia). Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo, você pode usar a API **Classificações de Segurança** para obter a classificação de uma assinatura específica. Além disso, você pode usar a API **Controles de Classificação de Segurança** para listar os controles de segurança e a classificação atual das suas assinaturas.

Para obter exemplos de ferramentas externas possibilitadas com a API de classificação de segurança, confira [a área de classificação de segurança da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre os [controles de segurança e classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Segurança de dados avançada para computadores SQL (Azure, outras nuvens e local) (versão prévia)

A Segurança de Dados Avançada da Central de Segurança do Azure para computadores SQL agora protege os SQL Servers hospedados no Azure, em outros ambientes de nuvem e, até mesmo, em computadores locais. Isso estende as proteções para que os SQL Servers nativos do Azure deem suporte completo a ambientes híbridos.

A Segurança de Dados Avançada fornece avaliação de vulnerabilidades e Proteção Avançada contra Ameaças para computadores SQL onde quer que estejam.

A configuração envolve duas etapas:

1. Implantar o agente do Log Analytics no computador host do SQL Server para fornecer a conexão à conta do Azure.

1. Habilitar o pacote opcional na página de preços e configurações da Central de Segurança.

Saiba mais sobre a [Segurança de Dados Avançada para computadores SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Duas novas recomendações para implantar o agente do Log Analytics em computadores do Azure Arc (versão prévia)

Duas novas recomendações foram adicionadas para ajudar a implantar o [agente do Log Analytics](../azure-monitor/platform/log-analytics-agent.md) nos computadores do Azure Arc e garantir que eles estejam protegidos pela Central de Segurança do Azure:

- **O agente do Log Analytics deve ser instalado nos computadores do Azure Arc baseados no Windows (versão prévia)**
- **O agente do Log Analytics deve ser instalado nos computadores do Azure Arc baseados em Linux (versão prévia)**

Essas novas recomendações serão exibidas nos mesmos quatro controles de segurança da recomendação existente (relacionada), **O agente de monitoramento deve ser instalado nos computadores**: corrigir as configurações de segurança, aplicar o controle de aplicativo adaptável, aplicar atualizações do sistema e habilitar a proteção de ponto de extremidade.

As recomendações também incluem a capacidade de correção rápida para ajudar a acelerar o processo de implantação. 

Saiba mais sobre essas duas novas recomendações na tabela [Recomendações de computação e aplicativo](recommendations-reference.md#recs-computeapp).

Saiba mais sobre como a Central de Segurança do Azure usa o agente em [O que é o agente do Log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Saiba mais sobre as [extensões para computadores do Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Novas políticas para criar configurações de exportação contínua e automação de fluxo de trabalho em escala

A automatização dos processos de monitoramento e resposta a incidentes da sua organização pode aprimorar significativamente o tempo necessário para investigar e atenuar incidentes de segurança.

Para implantar suas configurações de automação na sua organização, use essas políticas internas do Azure 'DeployIfdNotExist' para criar e configurar procedimentos de [exportação contínua](continuous-export.md) e [automação de fluxo de trabalho](workflow-automation.md):

As políticas podem ser encontradas na política do Azure:


|Goal  |Política  |ID da Política  |
|---------|---------|---------|
|Exportação contínua para o hub de eventos|[Implantar a exportação para o Hub de Eventos para os alertas e as recomendações Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Exportação contínua para o workspace do Log Analytics|[Implantar a exportação para o workspace do Log Analytics para os alertas e as recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automação de fluxo de trabalho para alertas de segurança|[Implantar a Automação de Fluxo de Trabalho para alertas da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automação de fluxo de trabalho para recomendações de segurança|[Implantar a Automação de Fluxo de Trabalho para recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Introdução aos [modelos de automação de fluxo de trabalho](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Saiba mais sobre como usar as duas políticas de exportação em [Configurar a automação de fluxo de trabalho em escala usando as políticas fornecidas](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) e [Configurar uma exportação contínua](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nova recomendação de uso de NSGs para proteger máquinas virtuais que não são voltadas para a Internet

O controle de segurança "Implementar melhores práticas de segurança" agora inclui a seguinte nova recomendação:

- **Máquinas virtuais não voltadas para a Internet devem ser protegidas com grupos de segurança de rede**

Uma recomendação existente, **As máquinas virtuais voltadas para a Internet devem ser protegidas com grupos de segurança de rede**, não distingue entre as VMs voltadas para a Internet e aquelas não voltadas para a Internet. Para ambas, uma recomendação de alta severidade foi gerada se uma VM não foi atribuída a um grupo de segurança de rede. Essa nova recomendação separa os computadores não voltados para a Internet para reduzir os falsos positivos e evitar alertas de alta severidade desnecessários.

Saiba mais na tabela [Recomendações de rede](recommendations-reference.md#recs-network).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Novas políticas para habilitar a proteção contra ameaças e a Segurança de Dados Avançada

As novas políticas abaixo foram adicionadas à iniciativa Padrão ASC e foram projetadas para ajudar a habilitar a proteção contra ameaças ou a Segurança de Dados Avançada para os tipos de recursos relevantes.

As políticas podem ser encontradas na política do Azure:


| Política                                                                                                                                                                                                                                                                | ID da Política                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A Segurança de Dados Avançada deve ser habilitada nos servidores do Banco de Dados SQL do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A Segurança de Dados Avançada deve ser habilitada nos servidores SQL em computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [A Proteção Avançada contra Ameaças deve ser habilitada em contas de Armazenamento do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos cofres do Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos planos do Serviço de Aplicativo do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos registros do Registro de Contêiner do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos clusters do Serviço de Kubernetes do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [A proteção avançada contra ameaças deve ser habilitada nas Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Saiba mais sobre a [Proteção contra ameaças na Central de Segurança do Azure](azure-defender.md).
