---
title: Requisitos e modos de conectividade
description: Explica as opções de conectividade dos serviços de dados habilitados para o Azure ARC para do seu ambiente para o Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 4364ed916e2b2783ab09f9d61ae63197d001ad42
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273174"
---
# <a name="connectivity-modes-and-requirements"></a>Requisitos e modos de conectividade

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Modos de conectividade

Há várias opções para o grau de conectividade do seu ambiente de serviços de dados habilitados para o Azure ARC para o Azure. Conforme seus requisitos variam de acordo com a política de negócios, regulamentação governamental ou a disponibilidade de conectividade de rede para o Azure, você pode escolher entre os seguintes modos de conectividade.

Os serviços de dados habilitados para Arc do Azure fornecem a opção de se conectar ao Azure em dois "modos de conectividade" diferentes: conectado diretamente e indiretamente conectado.  Isso proporciona a você a flexibilidade de escolher a quantidade de dados enviada ao Azure e como os usuários interagem com o controlador de dados Arc. Dependendo do modo de conectividade escolhido, algumas funcionalidades dos serviços de dados habilitados para Arc do Azure podem ou não estar disponíveis.

Importante, se os serviços de dados habilitados para Arc do Azure estiverem diretamente conectados ao Azure, os usuários poderão usar [Azure Resource Manager APIs](/rest/api/resources/), o CLI do Azure e o portal do Azure para operar os serviços de dados de arco do Azure. A experiência no modo conectado diretamente é muito parecida com o uso de qualquer outro serviço do Azure com provisionamento/desprovisionamento, dimensionamento, configuração e assim por diante no portal do Azure.  Se os serviços de dados habilitados para Arc do Azure estiverem indiretamente conectados ao Azure, a portal do Azure será uma exibição somente leitura. Você pode ver o inventário das instâncias gerenciadas do SQL e as instâncias de hiperescala postgres que você implantou e os detalhes sobre elas, mas não pode tomar medidas nelas no portal do Azure.  No modo conectado indiretamente, todas as ações devem ser executadas localmente usando Azure Data Studio, a CLI de dados do Azure ou ferramentas nativas kubernetes como kubectl.

Além disso, Azure Active Directory e o controle de acesso baseado em função do Azure podem ser usados apenas no modo conectado diretamente, pois há uma dependência em uma conexão direta e contínua ao Azure para fornecer essa funcionalidade.

Por fim, alguns serviços anexados ao Azure só estão disponíveis quando podem ser acessados diretamente, como os serviços de segurança do Azure defender, insights de contêiner e ' armazenamento de blob/backup do Azure '.

Atualmente, na visualização, há suporte apenas para o modo indiretamente conectado.  O modo conectado diretamente está planejado para o futuro.  Há um modo nunca conectado, que está descrito abaixo, mas não tem suporte no momento.

||**Conectado indiretamente**|**Conectado diretamente**|**Nunca conectado**|
|---|---|---|---|
|**Descrição**|O modo conectado indiretamente oferece a maioria dos serviços de gerenciamento localmente em seu ambiente sem conexão direta com o Azure.  Uma quantidade mínima de dados deve ser enviada para o Azure _apenas_para fins de inventário e cobrança. Ele é exportado para um arquivo e carregado no Azure pelo menos uma vez por mês.  Nenhuma conexão direta ou contínua ao Azure é necessária.  Alguns recursos e serviços que exigem uma conexão com o Azure não estarão disponíveis.|O modo conectado diretamente oferece todos os serviços disponíveis quando uma conexão direta pode ser estabelecida com o Azure. As conexões são sempre iniciadas _do_ seu ambiente para o Azure e usam portas padrão e protocolos como https/443.|Nenhum dado pode ser enviado de ou para o Azure de qualquer forma.|
|**Disponibilidade atual**| Disponível em versão prévia.|Planejado para visualização no futuro.|Não há suporte no momento.|
|**Casos de uso típicos**|Data centers locais que não permitem a conectividade dentro ou fora da região de dados do data center devido a políticas de conformidade normativa ou de negócios ou sem preocupações sobre ataques externos ou vazamento de dados.  Exemplos típicos: instituições financeiras, assistência médica, governo. <br/><br/>Locais do site de borda em que o site de borda normalmente não tem conectividade com a Internet.  Exemplos típicos: aplicativos de campo de petróleo/gás ou militar.  <br/><br/>Locais de site de borda que têm conectividade intermitente com longos períodos de interrupções.  Exemplos típicos: estádios, cruzeiro é entregue. | Organizações que estão usando nuvens públicas.  Exemplos típicos: Azure, AWS ou Google Cloud.<br/><br/>Locais de site de borda onde a conectividade com a Internet normalmente está presente e é permitida.  Exemplos típicos: lojas de varejo, manufatura.<br/><br/>Data centers corporativos com políticas mais permissivas para a conectividade de/para a região de dados do datacenter para a Internet.  Exemplos típicos: empresas não regulamentadas, pequenas/médias empresas|Os ambientes verdadeiramente "ar-gapped" em que nenhum dado de nenhuma circunstância pode vir ou ir do ambiente de dados. Exemplos típicos: principais recursos do governo secretos.|
|**Como os dados são enviados para o Azure**|Há três opções de como os dados de cobrança e de inventário podem ser enviados para o Azure:<br><br> 1) os dados são exportados para fora da região de dados por um processo automatizado que tem conectividade com a região de dados segura e o Azure.<br><br>2) os dados são exportados para fora da região de dados por um processo automatizado na região de dados, automaticamente copiados para uma região menos segura e um processo automatizado na região menos segura carrega os dados no Azure.<br><br>3) os dados são exportados manualmente por um usuário na região segura, extraídos manualmente da região segura e carregados manualmente no Azure. <br><br>As duas primeiras opções são um processo contínuo automatizado que pode ser agendado para ser executado com frequência, portanto, há um atraso mínimo na transferência de dados para o Azure sujeito somente à conectividade disponível para o Azure.|Os dados são enviados de forma automática e contínua ao Azure.|Os dados nunca são enviados para o Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Disponibilidade de recursos por modo de conectividade

|**Recurso**|**Conectado indiretamente**|**Conectado diretamente**|
|---|---|---|
|**Alta disponibilidade automática**|Com suporte|Com suporte|
|**Provisionamento de autoatendimento**|Com suporte<br/>A criação pode ser feita por meio do Azure Data Studio, da CLI de dados do Azure ou das ferramentas nativas do kubernetes (Helm, kubectl, OC etc.) ou usando o Azure Arc habilitado kubernetes o provisionamento de GitOps.|Com suporte<br/>Além das opções de criação de modo conectado indiretamente, você também pode criar por meio do portal do Azure, APIs de Azure Resource Manager, os modelos de CLI do Azure ou ARM. **Disponibilidade pendente do modo conectado diretamente**
|**Escalabilidade elástica**|Com suporte|Com suporte<br/>**Disponibilidade pendente do modo conectado diretamente**|
|**Billing**|Com suporte<br/>Os dados de cobrança são exportados periodicamente e enviados para o Azure.|Com suporte<br/>Os dados de cobrança são enviados de forma automática e contínua ao Azure e refletidos quase em tempo real. **Disponibilidade pendente do modo conectado diretamente**|
|**Gerenciamento de estoque**|Com suporte<br/>Os dados de inventário são exportados periodicamente e enviados para o Azure.|Com suporte<br/>Os dados de inventário são enviados de forma automática e contínua ao Azure e refletidos quase em tempo real. **Disponibilidade pendente do modo conectado diretamente**|
|**Atualizações automáticas e aplicação de patch**|Com suporte<br/>O controlador de dados deve ter acesso direto ao MCR (registro de contêiner da Microsoft) ou as imagens de contêiner precisam ser extraídas de MCR e enviadas por push para um registro de contêiner privado e local ao qual o controlador de dados tem acesso.|Com suporte<br/>**Disponibilidade pendente do modo conectado diretamente**|
|**Backup e restauração automáticos**|Com suporte<br/>Backup e restauração locais automáticos.|Com suporte<br/>Além de backup e restauração locais automatizados, você pode, _opcionalmente_ , enviar backups para o backup do Azure para retenção de longo prazo fora do local. **Disponibilidade pendente do modo conectado diretamente**|
|**Monitoramento**|Com suporte<br/>Monitoramento local usando painéis Grafana e Kibana.|Com suporte<br/>Além dos painéis de monitoramento locais, _opcionalmente_ , você pode enviar dados e logs de monitoramento para Azure monitor para o monitoramento em escala de vários sites em um único local. **Disponibilidade pendente do modo conectado diretamente**|
|**Autenticação**|Use nome de usuário/senha local para o controlador de dados e a autenticação do painel. Use logons do SQL e do postgres ou Active Directory para conectividade com instâncias de banco de dados.  Use os provedores de autenticação do K8s para autenticação na API do kubernetes.|Além de ou em vez dos métodos de autenticação para o modo conectado indiretamente, você pode, _opcionalmente_ , usar Azure Active Directory. **Disponibilidade pendente do modo conectado diretamente**|
|**RBAC (controle de acesso baseado em função)**|Use o RBAC kubernetes na API do kubernetes. Use o RBAC do SQL e do postgres para instâncias de banco de dados.|Opcionalmente, você pode integrar com Azure Active Directory para RBAC. **Disponibilidade pendente do modo conectado diretamente**|
|**Azure defender**|Sem suporte|Planejado para o futuro|

## <a name="connectivity-requirements"></a>Requisitos de conectividade

**Algumas funcionalidades exigem uma conexão com o Azure.**

**Toda a comunicação com o Azure é sempre iniciada do seu ambiente.** Isso é verdadeiro mesmo para operações, que são iniciadas por um usuário na portal do Azure.  Nesse caso, há uma tarefa efetivamente, que é enfileirada no Azure.  Um agente em seu ambiente inicia a comunicação com o Azure para ver quais tarefas estão na fila, executa as tarefas e relata o status/conclusão/falha no Azure.

|**Tipo de dados**|**Direção**|**Obrigatório/opcional**|**Custos adicionais**|**Modo necessário**|**Observações**|
|---|---|---|---|---|---|
|**Imagens de contêiner**|Registro de contêiner da Microsoft-cliente de >|Obrigatório|Não|Indireto ou direto|As imagens de contêiner são o método para distribuir o software.  Em um ambiente que pode se conectar ao MCR (registro de contêiner da Microsoft) pela Internet, as imagens de contêiner podem ser extraídas diretamente do MCR.  Caso o ambiente de implantação não tenha conectividade direta, você pode extrair as imagens do MCR e enviá-las por push para um registro de contêiner privado no ambiente de implantação.  No momento da criação, você pode configurar o processo de criação para efetuar pull do registro de contêiner privado em vez de MCR. Isso também se aplica a atualizações automatizadas.|
|**Inventário de recursos**|Ambiente do cliente-> Azure|Obrigatório|Não|Indireto ou direto|Um inventário de controladores de dados, instâncias de banco de dados (PostgreSQL e SQL) é mantido no Azure para fins de cobrança e também para fins de criação de um inventário de todos os controladores de dados e instâncias de banco de dado em um só lugar, o que é especialmente útil se você tiver mais de um ambiente com os serviços de dados de arco do Azure.  À medida que as instâncias são provisionadas, desprovisionadas, dimensionadas/colocadas horizontalmente, escaladas para cima/para baixo, o inventário é atualizado no Azure.|
|**Dados de telemetria de cobrança**|Ambiente do cliente-> Azure|Obrigatório|Não|Indireto ou direto|A utilização de instâncias de banco de dados deve ser enviada ao Azure para fins de cobrança.  Não há nenhum custo para os serviços de dados habilitados para o Azure Arc durante o período de visualização.|
|**Dados e logs de monitoramento**|Ambiente do cliente-> Azure|Opcional|Talvez dependendo do volume de dados (consulte [Azure monitor preço](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Indireto ou direto|Talvez você queira enviar os logs e dados de monitoramento coletados localmente para Azure Monitor para agregar dados em vários ambientes em um único local e também para usar serviços Azure Monitor como alertas, usando os dados em Azure Machine Learning, etc.|
|**RBAC (controle de acesso baseado em função) do Azure**|Ambiente do cliente-> ambiente do cliente do Azure >|Opcional|Não|Somente direto|Se você quiser usar o Azure para RBAC, a conectividade deverá ser estabelecida com o Azure em todos os momentos.  Se você não quiser usar o Azure para RBAC, o RBAC kubernetes local poderá ser usado.  **Disponibilidade pendente do modo conectado diretamente**|
|**Azure AD (Active Directory)**|Ambiente do cliente-> ambiente do cliente do Azure >|Opcional|Talvez, mas talvez você já esteja pagando pelo Azure AD|Somente direto|Se você quiser usar o Azure AD para autenticação, a conectividade deverá ser estabelecida com o Azure em todos os momentos. Se você não quiser usar o Azure AD para autenticação, você pode Serviços de Federação do Active Directory (AD FS) (ADFS) em Active Directory. **Disponibilidade pendente do modo conectado diretamente**|
|**Backups/restauração**|Ambiente do cliente-> ambiente do cliente do Azure >|Opcional|Sim para custos de armazenamento|Somente direto|Talvez você queira enviar backups que são feitos localmente para o backup do Azure para retenção de longo prazo fora do local de backups e trazê-los de volta para o ambiente local para restauração. **Disponibilidade pendente do modo conectado diretamente**|
|**Serviços de segurança do Azure defender**|Ambiente do cliente-> ambiente do cliente do Azure >|Opcional|Sim|Somente direto|**Disponibilidade pendente do modo conectado diretamente**|
|**Provisionamento e alterações de configuração de portal do Azure**|Ambiente do cliente-> ambiente do cliente do Azure >|Opcional|Não|Somente direto|O provisionamento e as alterações de configuração podem ser feitas localmente usando Azure Data Studio ou a CLI do azdata.  No modo conectado diretamente, você também poderá provisionar e fazer alterações de configuração do portal do Azure. **Disponibilidade pendente do modo conectado diretamente**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Detalhes sobre endereços da Internet, portas, criptografia e suporte do servidor proxy

Atualmente, na fase de visualização, há suporte apenas para o modo indiretamente conectado.  Nesse modo, há apenas três conexões necessárias para os serviços disponíveis na Internet.  Todas as conexões HTTPS para o Azure e o registro de contêiner da Microsoft são criptografadas usando SSL/TLS usando certificados assinados oficialmente e verificáveis.

|**Nome**|**Fonte de conexão**|**Destino de conexão**|**Protocolo**|**Porta**|**Pode usar proxy**|**Autenticação**|**Observações**|
|---|---|---|---|---|---|---|---|
|**Registro de contêiner da Microsoft (MCR)**|O kubernetes kubelet em cada um dos nós kubernetes puxando as imagens de contêiner.|`mcr.microsoft.com`|HTTPS|443|Sim|Nenhum|O registro de contêiner da Microsoft hospeda as imagens de contêiner de serviços de dados habilitados para Arc do Azure.  Você pode extrair essas imagens do MCR e enviá-las a um registro de contêiner privado e configurar o processo de implantação do controlador de dados para efetuar pull das imagens de contêiner desse registro de contêiner privado.|
|**APIs de Azure Resource Manager**|Um computador executando Azure Data Studio, CLI de dados do Azure ou CLI do Azure que está se conectando ao Azure.|`login.microsoftonline.com`<br/>`management.azure.com`<br/>`san-af-eastus-prod.azurewebsites.net`<br/>`san-af-eastus2-prod.azurewebsites.net`<br/>`san-af-australiaeast-prod.azurewebsites.net`<br/>`san-af-centralus-prod.azurewebsites.net`<br/>`san-af-westus2-prod.azurewebsites.net`<br/>`san-af-westeurope-prod.azurewebsites.net`<br/>`san-af-southeastasia-prod.azurewebsites.net`<br/>`san-af-koreacentral-prod.azurewebsites.net`<br/>`san-af-northeurope-prod.azurewebsites.net`<br/>`san-af-westeurope-prod.azurewebsites.net`<br/>`san-af-uksouth-prod.azurewebsites.net`<br/>`san-af-francecentral-prod.azurewebsites.net`|HTTPS|443|Sim|Azure Active Directory|Azure Data Studio, a CLI de dados do Azure e CLI do Azure se conectar às APIs do Azure Resource Manager para enviar e recuperar dados de e para o Azure para alguns recursos.|
|**APIs de Azure Monitor**|Um computador que executa a CLI de dados do Azure ou CLI do Azure que está carregando métricas ou logs de monitoramento para Azure Monitor.|`login.microsoftonline.com`<br/>`management.azure.com`<br/>`*.ods.opinsights.azure.com`<br/>`*.oms.opinsights.azure.com`<br/>`*.monitoring.azure.com`|HTTPS|443|Sim|Azure Active Directory|Azure Data Studio, a CLI de dados do Azure e CLI do Azure se conectar às APIs do Azure Resource Manager para enviar e recuperar dados de e para o Azure para alguns recursos.|

> [!NOTE]
> Por enquanto, todas as conexões HTTPS/443 do navegador para os painéis Grafana e Kibana e da CLI de dados do Azure para a API do controlador de dados são criptografadas por SSL usando certificados autoassinados.  Um recurso estará disponível no futuro, que permitirá que você forneça seus próprios certificados para criptografia dessas conexões SSL.

A conectividade do Azure Data Studio e da CLI de dados do Azure para o servidor de API kubernetes usa a autenticação do kubernetes e a criptografia que você estabeleceu.  Cada usuário que está usando Azure Data Studio e a CLI de dados do Azure deve ter uma conexão autenticada com a API kubernetes para executar muitas das ações relacionadas aos serviços de dados habilitados para Arc do Azure.
