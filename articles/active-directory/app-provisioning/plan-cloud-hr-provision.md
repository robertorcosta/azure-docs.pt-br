---
title: Planeje o aplicativo de RH em nuvem para o provisionamento do usuário do Azure Active Directory
description: Este artigo descreve o processo de implantação da integração de sistemas de RH em nuvem, como Workday e SuccessFactors, com o Azure Active Directory. A integração do Azure AD com o sistema de RH na nuvem resulta em um sistema completo de gerenciamento do ciclo de vida da identidade.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522425"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planeje o aplicativo de RH em nuvem para o provisionamento do usuário do Azure Active Directory

Historicamente, a equipe de TI tem confiado em métodos manuais para criar, atualizar e excluir funcionários. Eles usaram métodos como o upload de arquivos CSV ou scripts personalizados para sincronizar dados de funcionários. Esses processos de provisionamento são propensos a erros, inseguros e difíceis de gerenciar.

Para gerenciar os ciclos de vida de identidade de funcionários, fornecedores ou funcionários contingentes, o serviço de [provisionamento de usuários Do Azure Active Directory (Azure AD)](../app-provisioning/user-provisioning.md) oferece integração com aplicativos de recursos humanos (RH) baseados em nuvem. Exemplos de aplicativos incluem Workday ou SuccessFactors.

O Azure AD usa essa integração para habilitar os seguintes fluxos de trabalho do aplicativo de RH (app) em nuvem:

- **Provisão os usuários para o Active Directory:** Provisionar conjuntos selecionados de usuários de um aplicativo de RH na nuvem em um ou mais domínios do Active Directory.
- **Provisão de usuários somente na nuvem para o Azure AD:** Em cenários em que o Active Directory não é usado, provisão os usuários diretamente do aplicativo de RH na nuvem para o Azure AD.
- **Escreva de volta para o aplicativo de RH em nuvem:** Escreva os endereços de e-mail e os atributos do Azure AD de volta ao aplicativo de RH na nuvem.

> [!NOTE]
> Este plano de implantação mostra como implantar seus fluxos de trabalho de aplicativos de RH na nuvem com o provisionamento de usuários Do Azure AD. Para obter informações sobre como implantar o provisionamento automático do usuário em aplicativos de software como serviço (SaaS), consulte [Planejar uma implantação automática de provisionamento de usuários](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de provisionamento de usuários Azure AD permite a automação dos seguintes cenários de gerenciamento do ciclo de vida da identidade baseada em RH:

- **Contratação de novos funcionários:** Quando um novo funcionário é adicionado ao aplicativo de RH na nuvem, uma conta de usuário é criada automaticamente no Active Directory e no Azure AD com a opção de escrever de volta o endereço de e-mail e os atributos de nome de usuário para o aplicativo de RH na nuvem.
- **Atualizações de atributos e perfis de funcionários:** Quando um registro de funcionário, como nome, título ou gerente, é atualizado no aplicativo de RH na nuvem, sua conta de usuário é atualizada automaticamente no Active Directory e no Azure AD.
- **Demissões de funcionários:** Quando um funcionário é demitido no aplicativo de RH na nuvem, sua conta de usuário é automaticamente desativada no Active Directory e no Azure AD.
- **Recontratafuncionários:** Quando um funcionário é recontratado no aplicativo de RH na nuvem, sua conta antiga pode ser reativada ou reativada automaticamente para o Active Directory e o Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Para quem essa integração é mais adequada?

A integração do aplicativo de RH na nuvem com o provisionamento de usuários Azure AD é ideal para organizações que:

- Quer uma solução pré-construída baseada em nuvem para provisionamento de usuários de RH em nuvem.
- Exija o provisionamento direto do usuário do aplicativo de RH na nuvem para o Active Directory ou o Azure AD.
- Exija que os usuários sejam provisionados usando dados obtidos do aplicativo de RH em nuvem.
- Exija a adesão, a movimentação e a falta de sincronização dos usuários com uma ou mais florestas, domínios e OUs ativos com base apenas nas informações de alteração detectadas no aplicativo de RH na nuvem.
- Use o Office 365 para e-mail.

## <a name="learn"></a>Saiba mais

O provisionamento de usuários cria uma base para a governança de identidade contínua. Ele melhora a qualidade dos processos de negócios que dependem de dados de identidade autoritários.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

- **Sistema de origem**: O repositório de usuários que o Azure AD prosconta. Um exemplo é um aplicativo de RH em nuvem, como Workday ou SuccessFactors.
- **Sistema de destino**: O repositório de usuários para o que o Azure AD prossegue. Exemplos são Active Directory, Azure AD, Office 365 ou outros aplicativos SaaS.
- **Processo Joiners-Movers-Leavers**: Um termo usado para novas contratações, transferências e rescisão usando um aplicativo de RH em nuvem como um sistema de registros. O processo é concluído quando o serviço prossegue com sucesso os atributos necessários ao sistema de destino.

### <a name="key-benefits"></a>Principais benefícios

Esse recurso de provisionamento de TI orientado por RH oferece os seguintes benefícios significativos para os negócios:

- **Aumentar a produtividade:** Agora você pode automatizar a atribuição de contas de usuário e licenças do Office 365 e fornecer acesso a grupos-chave. A automatização de atribuições dá aos novos contratados acesso imediato às suas ferramentas de trabalho e aumenta a produtividade.
- **Gerenciar riscos:** Você pode aumentar a segurança automatizando alterações com base no status dos funcionários ou membros de grupo com dados fluindo a partir do aplicativo de RH na nuvem. A automatização das alterações garante que as identidades dos usuários e o acesso aos principais aplicativos se atualizem automaticamente quando os usuários fazem a transição ou deixam a organização.
- **Endereçar conformidade e governança:** O Azure AD suporta registros de auditoria nativos para solicitações de provisionamento de usuários realizadas por aplicativos de sistemas de origem e destino. Com a auditoria, você pode rastrear quem tem acesso aos aplicativos a partir de uma única tela.
- **Gerenciar o custo:** O provisionamento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Reduz a necessidade de soluções de provisionamento de usuários desenvolvidas medida construídas ao longo do tempo usando plataformas herdadas e desatualizadas.

### <a name="licensing"></a>Licenciamento

Para configurar o aplicativo de RH em nuvem para a integração de provisionamento de usuários do Azure AD, você precisa de uma [licença ad Premium](https://azure.microsoft.com/pricing/details/active-directory/) válida e uma licença para o aplicativo de RH na nuvem, como Workday ou SuccessFactors.

Você também precisa de uma licença de assinatura AD Premium AD válida ou uma licença de assinatura mais alta para cada usuário que será originado do aplicativo de RH na nuvem e provisionado para o Active Directory ou o Azure AD. Qualquer número inadequado de licenças de propriedade do aplicativo de RH na nuvem pode levar a erros durante o provisionamento do usuário.

### <a name="prerequisites"></a>Pré-requisitos

- Acesso ao administrador global do Azure AD para configurar o agente de provisionamento Azure AD Connect.
- Uma instância de teste e produção do aplicativo de RH em nuvem.
- Permissões de administrador no aplicativo de RH na nuvem para criar um usuário de integração do sistema e fazer alterações para testar os dados dos funcionários para fins de teste.
- Para o provisionamento do usuário no Active Directory, é necessário um servidor executando o Windows Server 2012 ou superior com o tempo de execução .NET 4.7.1+ para hospedar o [agente de provisionamento Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) para sincronizar usuários entre Active Directory e Azure AD.

### <a name="training-resources"></a>Recursos de treinamento

| **Recursos** | **Link e descrição** |
|:-|:-|
| vídeos | [O que é provisionamento de usuários no Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Como implantar o provisionamento do usuário no Active Azure Directory](https://youtu.be/pKzyts6kfrw) |
| Tutoriais | [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure AD](../saas-apps/tutorial-list.md) |
| | [Tutorial: Configurar o Workday para provisionamento automático de usuário](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Perguntas frequentes | [Provisionamento automatizado de usuários](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Provisionamento do Workday para a Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Arquitetura da solução

O exemplo a seguir descreve a arquitetura de solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns e inclui:

- **Fluxo de dados de RH autoritário do aplicativo de RH em nuvem para o Active Directory.** Nesse fluxo, o evento de RH (processo Joiners-Movers-Leavers) é iniciado no inquilino do aplicativo de RH na nuvem. O serviço de provisionamento Azure AD e o agente de provisionamento Azure AD Connect disponibilizam os dados do usuário do inquilino do aplicativo de RH na nuvem para o Active Directory. Dependendo do evento, ele pode levar a criar, atualizar, ativar e desativar operações no Active Directory.
- **Sincronize com o Azure AD e escreva de volta e-mail e nome de usuário do Active Directory no local para o aplicativo de RH em nuvem.** Depois que as contas são atualizadas no Active Directory, ela é sincronizada com o Azure AD através do Azure AD Connect. Os endereços de e-mail e os atributos de nome de usuário podem ser gravados de volta ao inquilino do aplicativo de RH na nuvem.

![Diagrama de fluxo de trabalho](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrição do fluxo de trabalho

As seguintes etapas-chave estão indicadas no diagrama:  

1. **A equipe de RH** realiza as transações no inquilino do aplicativo de RH na nuvem.
2. **O serviço de provisionamento AD do Azure** executa os ciclos programados do inquilino do aplicativo de RH na nuvem e identifica alterações que precisam ser processadas para sincronização com o Active Directory.
3. **O serviço de provisionamento Azure AD** invoca o agente de provisionamento Azure AD Connect com uma carga útil de solicitação que contém operações de criação, atualização, ativação e desativação do Active Directory.
4. **O agente de provisionamento Azure AD Connect** usa uma conta de serviço para gerenciar dados da conta do Active Directory.
5. **O Azure AD Connect** executa o delta [sync](../hybrid/how-to-connect-sync-whatis.md) para puxar atualizações no Active Directory.
6. As atualizações **do Active Directory** são sincronizadas com o Azure AD.
7. **O serviço de provisionamento Azure AD** reescreve o atributo de e-mail e o nome de usuário do Azure AD para o inquilino do aplicativo de RH na nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva as partes interessadas certas

Quando os projetos de tecnologia falham, eles normalmente o fazem devido a expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando as partes interessadas certas.](https://aka.ms/deploymentplans) Certifique-se também de que os papéis das partes interessadas no projeto sejam bem compreendidos. Documente os stakeholders e suas contribuições e responsabilidades do projeto.

Inclua um representante da organização de RH que pode fornecer informações sobre os processos de negócios de RH existentes e a identidade do trabalhador, além dos requisitos de processamento de dados de trabalho.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente com seus usuários sobre quando e como sua experiência mudará. Deixe-os saber como ganhar apoio se eles tiverem problemas.

### <a name="plan-a-pilot"></a>Planeje um piloto

Integrar processos de negócios de RH e fluxos de trabalho de identidade do aplicativo de RH em nuvem aos sistemas-alvo requer uma quantidade considerável de validação de dados, transformação de dados, limpeza de dados e testes de ponta a ponta antes de implantar a solução na produção.

Execute a configuração inicial em um [ambiente piloto](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) antes de dimensioná-la para todos os usuários em produção.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecione aplicativos de conector de provisionamento de RH em nuvem

Para facilitar os fluxos de trabalho de provisionamento do Azure AD entre o aplicativo de RH na nuvem e o Active Directory, você pode adicionar vários aplicativos de conector de provisionamento da galeria de aplicativos Azure AD:

- **Aplicativo de RH em nuvem para provisionamento de usuários do Active Directory**: Este aplicativo de conector de provisionamento facilita o provisionamento da conta do usuário do aplicativo de RH na nuvem para um único domínio do Active Directory. Se você tiver vários domínios, será possível adicionar uma instância desse aplicativo da galeria de aplicativos do Azure AD para cada domínio do Active Directory que precisar ser provisionado.
- **Aplicativo de RH em nuvem para provisionamento de usuários azure AD**: Enquanto o Azure AD Connect é a ferramenta que deve ser usada para sincronizar usuários do Active Directory com o Azure AD, este aplicativo de conector de provisionamento pode ser usado para facilitar o provisionamento de usuários somente na nuvem do aplicativo de RH na nuvem para um único inquilino do Azure AD.
- **Regravação do aplicativo cloud HR**: Este aplicativo de conector de provisionamento facilita a regravação dos endereços de e-mail do usuário do Azure AD para o aplicativo de RH na nuvem.

Por exemplo, a imagem a seguir lista os aplicativos de conector workday que estão disponíveis na galeria de aplicativos do Azure AD.

![Galeria de aplicativos do portal do Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Fluxograma de decisão

Use o fluxograma de decisão a seguir para identificar quais aplicativos de provisionamento de RH em nuvem são relevantes para o seu cenário.

![Fluxograma de decisão](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Projete a topologia de implantação do agente de provisionamento Azure AD Connect

A integração de provisionamento entre o aplicativo de RH em nuvem e o Active Directory requer quatro componentes:

- Inquilino do aplicativo cloud HR
- Aplicativo de conector de provisionamento
- Agente de provisionamento Azure AD Connect
- Domínio do Active Directory

A topologia de implantação do agente de provisionamento Azure AD Connect depende do número de inquilinos de aplicativos de RH na nuvem e domínios de crianças do Active Directory que você planeja integrar. Se você tiver vários domínios do Active Directory, depende se os domínios do Active Directory são contíguos ou [desarticulados](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

Com base na sua decisão, escolha um dos cenários de implantação:

- Um único inquilino de aplicativo de RH em nuvem -> direcionar domínios de criança sistrias de diretório ativo ou único em uma floresta confiável
- Um único inquilino de aplicativo de RH em nuvem -> direcionar vários domínios de crianças em uma floresta de do Active Directory

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Um único inquilino de aplicativo de RH em nuvem -> direcionar domínios de criança sistrias de diretório ativo ou único em uma floresta confiável

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento Azure AD Connect para implantar|Dois (para alta disponibilidade e failover)
|Número de aplicativos de conector de provisionamento para configurar|Um aplicativo por domínio infantil|
|Host do servidor para o agente de provisionamento Azure AD Connect|Windows 2012 R2+ com linha de visão para controladores de domínio do Active Directory geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Fluxo para agentes locais](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Um único inquilino de aplicativo de RH em nuvem -> direcionar vários domínios de crianças em uma floresta de do Active Directory

Esse cenário envolve o provisionamento de usuários do aplicativo de RH em nuvem para domínios em florestas desarticuladas do Active Directory.

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento Azure AD Connect para implantar no local|Dois por floresta de diretório ativo desarticulada|
|Número de aplicativos de conector de provisionamento para configurar|Um aplicativo por domínio infantil|
|Host do servidor para o agente de provisionamento Azure AD Connect|Windows 2012 R2+ com linha de visão para controladores de domínio do Active Directory geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Inquilino de aplicativo de RH em nuvem única desarticula floresta de Diretório Ativo](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento Azure AD Connect

O aplicativo de RH em nuvem para a solução de provisionamento de usuários do Active Directory requer que você implante um ou mais agentes de provisionamento Azure AD Connect em servidores que executam o Windows 2012 R2 ou superior. Os servidores devem ter um tempo mínimo de 4 GB de RAM e .NET 4.7.1+. Certifique-se de que o servidor host tenha acesso à rede ao domínio do Active Directory de destino.

Para preparar o ambiente local, o assistente de configuração do agente de provisionamento Azure AD Connect registra o agente com seu inquilino Azure AD, [abre portas,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [permite acesso a URLs](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e suporta [configuração de proxy HTTPS de saída.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

O agente de provisionamento usa uma conta de serviço para se comunicar com os domínios do Active Directory. Antes de instalar o agente, crie uma conta de serviço em Usuários e Computadores do Active Directory que atenda aos seguintes requisitos:

- Uma senha que não expira
- Permissões de controle delegadas para ler, criar, excluir e gerenciar contas de usuário

Você pode selecionar controladores de domínio que devem lidar com solicitações de provisionamento. Se você tiver vários controladores de domínio distribuídos geograficamente, instale o agente de provisionamento no mesmo local que seus controladores de domínio preferidos. Esse posicionamento melhora a confiabilidade e o desempenho da solução completa.

Para alta disponibilidade, você pode implantar mais de um agente de provisionamento Azure AD Connect. Registre o agente para lidar com o mesmo conjunto de domínios do Active Directory no local.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planeje filtros de escopo e mapeamento de atributos

Quando você habilita o provisionamento do aplicativo de RH na nuvem para o Active Directory ou o Azure AD, o portal Azure controla os valores de atributos através do mapeamento de atributos.

### <a name="define-scoping-filters"></a>Defina filtros de escopo

Use [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir as regras baseadas em atributos que determinam quais usuários devem ser provisionados do aplicativo de RH na nuvem para o Active Directory ou o Azure AD.

Ao iniciar o processo De Marceneiros, reúna os seguintes requisitos:

- O aplicativo de RH em nuvem é usado para trazer a bordo funcionários e trabalhadores contingentes?
- Você planeja usar o aplicativo de RH em nuvem para o provisionamento de usuários do Azure AD para gerenciar funcionários e trabalhadores contingentes?
- Você planeja lançar o aplicativo de RH em nuvem para o provisionamento de usuários do Azure AD apenas para um subconjunto dos usuários do aplicativo de RH na nuvem? Um exemplo pode ser apenas empregados.

Dependendo de seus requisitos, ao configurar mapeamentos de atributos, você pode definir o campo **'Escopo do objeto de origem'** para selecionar quais conjuntos de usuários no aplicativo de RH na nuvem devem estar no escopo de provisionamento para o Active Directory. Para obter mais informações, consulte o tutorial do aplicativo de RH em nuvem para filtros de escopo comumente usados.

### <a name="determine-matching-attributes"></a>Determine atributos correspondentes

Com o provisionamento, você tem a capacidade de combinar contas existentes entre a fonte e o sistema de destino. Quando você integra o aplicativo de RH na nuvem com o serviço de provisionamento Ad do Azure, você pode [configurar o mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) para determinar quais dados do usuário devem fluir do aplicativo de RH na nuvem para o Active Directory ou o Azure AD.

Ao iniciar o processo De Marceneiros, reúna os seguintes requisitos:

- Qual é o ID único neste aplicativo de RH em nuvem que é usado para identificar cada usuário?
- De uma perspectiva de ciclo de vida de identidade, como você lida com recontratações? Recontratações guardam suas antigas fotos de funcionários?
- Você processa contratações com data futura e cria contas de Diretório Ativo para elas com antecedência?
- Do ponto de vista do ciclo de vida da identidade, como você lida com a conversão de funcionários para funcionários contingentes, ou não?
- Os usuários convertidos mantêm suas contas ativas antigas ou recebem novas?

Dependendo de suas necessidades, o Azure AD oferece suporte direto de mapeamento atributo-atributo, fornecendo valores constantes ou [expressões de escrita para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md). Essa flexibilidade lhe dá o controle final do que está povoado no atributo do aplicativo direcionado. Você pode usar a [API](../app-provisioning/export-import-provisioning-configuration.md) do Microsoft Graph e o Graph Explorer para exportar mapeamentos e esquemas de provisionamento do usuário para um arquivo JSON e importá-lo de volta para o Azure AD.

Por padrão, o atributo no aplicativo de RH na nuvem que representa o ID exclusivo do funcionário é usado como o atributo correspondente *mapeado ao atributo único no Active Directory.* Por exemplo, no cenário do aplicativo Workday, o atributo **Workday** **WorkerID** é mapeado para o atributo Active Directory **employeeID.**

Você pode definir vários atributos correspondentes e atribuir precedência de correspondência. Eles são avaliados em precedência correspondente. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.

Você também pode [personalizar os mapeamentos de atributos padrão,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)como alterar ou excluir mapeamentos de atributos existentes. Você também pode criar novos mapeamentos de atributos de acordo com as necessidades do seu negócio. Para obter mais informações, consulte o tutorial do aplicativo de RH na nuvem (como [Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)para obter uma lista de atributos personalizados para mapear.

### <a name="determine-user-account-status"></a>Determine o status da conta de usuário

Por padrão, o aplicativo de conector de provisionamento mapeia o status do perfil do usuário do RH para o status da conta de usuário no Active Directory ou no Azure AD para determinar se habilita ou desativa a conta do usuário.

Quando você iniciar o processo Joiners-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marceneiros** | De uma perspectiva de ciclo de vida de identidade, como você lida com recontratações? Recontratações guardam suas antigas fotos de funcionários? |
| | Você processa contratações com data futura e cria contas de Diretório Ativo para elas com antecedência? Essas contas são criadas em um estado habilitado ou desativado? |
| | Do ponto de vista do ciclo de vida da identidade, como você lida com a conversão de funcionários para funcionários contingentes, ou não? |
| | Os usuários convertidos mantêm suas contas ativas antigas ou recebem novas? |
| **Abandono** | As demissões são tratadas de forma diferente para funcionários e trabalhadores contingentes no Diretório Ativo? |
| | Quais datas efetivas são consideradas para o processamento do término do usuário? |
| | Como as conversões de funcionários e trabalhadores contingentes afetam as contas ativas existentes? |
| | Como você processa a operação Rescind no Active Directory? As operações rescindidas precisam ser tratadas se futuras contratações datadas forem criadas no Active Directory como parte do processo de Joiner. |

Dependendo de suas necessidades, você pode personalizar a lógica de mapeamento usando [expressões Azure AD](../app-provisioning/functions-for-customizing-application-data.md) para que a conta do Active Directory seja ativada ou desativada com base em uma combinação de pontos de dados.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapeie o aplicativo de RH em nuvem para os atributos do usuário do Active Directory

Cada aplicativo de RH em nuvem é fornecido com o aplicativo padrão de RH em nuvem para mapeamentos do Active Directory.

Quando você iniciar o processo Joiners-Movers-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marceneiros** | O processo de criação de conta do Active Directory é manual, automatizado ou parcialmente automatizado? |
| | Você planeja propagar atributos personalizados do aplicativo de RH em nuvem para o Active Directory? |
| **Motores** | Quais atributos você gostaria de processar sempre que uma operação movers ocorre no aplicativo de RH em nuvem? |
| | Você executa alguma validação de atributo específica no momento das atualizações do usuário? Se sim, forneça detalhes. |
| **Abandono** | As demissões são tratadas de forma diferente para funcionários e trabalhadores contingentes no Diretório Ativo? |
| | Quais datas efetivas são consideradas para o processamento do término do usuário? |
| | Como as conversões de funcionários e trabalhadores contingentes afetam as contas ativas existentes do Active Directory? |

Dependendo de suas necessidades, você pode modificar os mapeamentos para atender às suas metas de integração. Para obter mais informações, consulte o tutorial específico do aplicativo de RH em nuvem (como [Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)para obter uma lista de atributos personalizados para mapear.

### <a name="generate-a-unique-attribute-value"></a>Gerar um valor de atributo único

Ao iniciar o processo Detonadores, talvez seja necessário gerar valores de atributos únicos ao definir atributos como CN, samAccountName e UPN, que tem restrições exclusivas.

A função Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) avalia cada regra e, em seguida, verifica o valor gerado para a exclusividade no sistema de destino. Por exemplo, consulte [Gerar valor único para o atributo userPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> No momento, essa função é suportada apenas para provisionamento do usuário workday to Active Directory. Ele não pode ser usado com outros aplicativos de provisionamento.

### <a name="configure-active-directory-ou-container-assignment"></a>Configurar a atribuição de contêiner OU do Diretório Ativo

É um requisito comum colocar contas de usuário do Active Directory em contêineres com base em unidades de negócios, locais e departamentos. Quando você inicia um processo movers e, se houver uma mudança de organização de supervisão, você pode precisar mover o usuário de um OU para outro no Active Directory.

Use a função [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) para configurar a lógica de negócios para a atribuição OU e mapeá-la para o atributo Active Directory **parentDistinguishedName**.

Por exemplo, se você quiser criar usuários em OU com base no atributo DE RH **Município,** você pode usar a seguinte expressão:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Com esta expressão, se o valor do Município for Dallas, Austin, Seattle ou Londres, a conta de usuário será criada no OU correspondente. Se não houver correspondência, então a conta será criada no OU padrão.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plano para entrega de senhas de novas contas de usuário

Quando você inicia o processo De Toques, você precisa definir e fornecer uma senha temporária de novas contas de usuário. Com o provisionamento de usuários do Azure AD em nuvem, você pode implementar o recurso [de redefinição de senha de autoatendimento](../authentication/quickstart-sspr.md) (SSPR) do Azure AD para o usuário no primeiro dia.

O SSPR é um meio simples para os administradores de TI permitirem que os usuários refinam suas senhas ou desbloqueiem suas contas. Você pode provisionar o atributo **Número móvel** do aplicativo de RH na nuvem para o Active Directory e sincronizá-lo com o Azure AD. Depois **que** o atributo Número de Celular estiver no Azure AD, você pode habilitar o SSPR para a conta do usuário. Em seguida, no primeiro dia, o novo usuário pode usar o número de celular registrado e verificado para autenticação.

## <a name="plan-for-initial-cycle"></a>Plano para o ciclo inicial

Quando o serviço de provisionamento Ad do Azure é executado pela primeira vez, ele executa um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle) contra o aplicativo de RH em nuvem para criar um instantâneo de todos os objetos do usuário no aplicativo de RH na nuvem. O tempo devida para os ciclos iniciais depende diretamente de quantos usuários estão presentes no sistema de origem. O ciclo inicial para alguns inquilinos de aplicativos de RH em nuvem com mais de 100.000 usuários pode levar muito tempo.

**Para grandes inquilinos de aplicativos de RH em nuvem (>30.000 usuários),** execute o ciclo inicial em estágios progressivos. Inicie as atualizações incrementais somente após a validação de que os atributos corretos estão definidos no Active Directory para diferentes cenários de provisionamento de usuários. Siga a ordem aqui.

1. Execute o ciclo inicial apenas para um conjunto limitado de usuários definindo o [filtro de escopo](#plan-scoping-filters-and-attribute-mapping).
2. Verifique o provisionamento da conta do Active Directory e os valores de atributo definidos para os usuários selecionados para a primeira execução. Se o resultado atender às suas expectativas, expanda o filtro de escopo para incluir progressivamente mais usuários e verifique os resultados para a segunda corrida.

Depois de satisfeito com os resultados do ciclo inicial para usuários de teste, inicie as [atualizações incrementais](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Teste de plano e segurança

Em cada etapa de sua implantação desde o piloto inicial até a habilitação do provisionamento do usuário, certifique-se de que você está testando que os resultados são como esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o aplicativo de RH na nuvem para o provisionamento do usuário Azure AD, execute casos de teste para verificar se essa solução atende aos requisitos da sua organização.

|Cenários|Resultados esperados|
|:-|:-|
|Novo funcionário é contratado no aplicativo de RH em nuvem.| - A conta de usuário é provisionada no Active Directory.</br>- O usuário pode fazer login em aplicativos de domínio do Active Directory e executar as ações desejadas.</br>- Se a sincronização do Azure AD Connect estiver configurada, a conta de usuário também será criada no Azure AD.
|O usuário é encerrado no aplicativo de RH em nuvem.|- A conta de usuário está desativada no Active Directory.</br>- O usuário não pode fazer login em nenhum aplicativo corporativo protegido pelo Active Directory.
|A organização de supervisão do usuário é atualizada no aplicativo de RH em nuvem.|Com base no mapeamento de atributos, a conta de usuário passa de um OU para outro no Active Directory.|
|O RH atualiza o gerente do usuário no aplicativo de RH em nuvem.|A área de gerente no Active Directory é atualizada para refletir o nome do novo gerente.|
|A HR recontrata um funcionário para uma nova função.|O comportamento depende de como o aplicativo de RH em nuvem é configurado para gerar IDs de funcionários:</br>- Se o ID do funcionário antigo for reutilizado para recontratar, o conector habilita ristindo a conta do Active Directory existente para o usuário.</br>- Se a recontratação receber um novo ID de funcionário, o conector criará uma nova conta do Active Directory para o usuário.|
|O RH converte o empregado em um trabalhador contratado ou vice-versa.|Uma nova conta do Active Directory é criada para a nova persona e a conta antiga fica desativada na data efetiva de conversão.|

Use os resultados anteriores para determinar como fazer a transição da implementação automática do provisionamento do usuário para a produção com base nos cronogramas estabelecidos.

> [!TIP]
> Use técnicas como redução de dados e limpeza de dados quando você atualizar o ambiente de teste com dados de produção para remover ou mascarar dados pessoais confidenciais para cumprir as normas de privacidade e segurança. 

### <a name="plan-security"></a>Planejar segurança

É comum que uma revisão de segurança seja necessária como parte da implantação de um novo serviço. Se uma revisão de segurança for necessária ou não for realizada, consulte os muitos [white papers](https://www.microsoft.com/download/details.aspx?id=36391) do Azure AD que fornecem uma visão geral da identidade como um serviço.

### <a name="plan-rollback"></a>Reversão do plano

A implementação de provisionamento de usuários de RH em nuvem pode não funcionar conforme desejado no ambiente de produção. Se assim for, as seguintes etapas de reversão podem ajudá-lo a reverter para um estado bom conhecido anteriormente.

1. Revise o [relatório de resumo de provisionamento](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) e os registros de provisionamento para determinar quais operações incorretas foram [realizadas](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) nos usuários ou grupos afetados. Para obter mais informações sobre o relatório de resumo de provisionamento e logs, consulte [Gerenciar provisionamento de aplicativos de RH em nuvem](#manage-your-configuration).
2. O último estado bom conhecido dos usuários ou grupos afetados pode ser determinado através dos registros de auditoria de provisionamento ou pela revisão dos sistemas de destino (Azure AD ou Active Directory).
3. Trabalhe com o proprietário do aplicativo para atualizar os usuários ou grupos afetados diretamente no aplicativo usando os últimos valores de estado de bom estado conhecidos.

## <a name="deploy-the-cloud-hr-app"></a>Implantar o aplicativo de RH em nuvem

Escolha o aplicativo de RH em nuvem que esteja alinhado aos requisitos da sua solução.

**Jornada de trabalho**: Para importar perfis de trabalhadores do Workday para o Active Directory e o Azure AD, consulte [Tutorial: Configure workday para provisionamento automático do usuário](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcionalmente, você pode escrever de volta o endereço de e-mail e o nome de usuário para Workday.

## <a name="manage-your-configuration"></a>Gerencie sua configuração

O Azure AD pode fornecer insights adicionais sobre o uso do provisionamento de usuários e a saúde operacional da sua organização através de registros de auditoria e relatórios.

### <a name="gain-insights-from-reports-and-logs"></a>Obtenha insights de relatórios e logs

Após um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle)bem-sucedido, o serviço de provisionamento Ad do Azure continua a executar atualizações incrementais indefinidamente, em intervalos definidos nos tutoriais específicos de cada aplicativo, até que ocorra um dos seguintes eventos:

- O serviço é parado manualmente. Um novo ciclo inicial é acionado usando o [portal Azure](https://portal.azure.com/) ou o comando [API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) do Microsoft Graph apropriado.
- Um novo ciclo inicial é acionado devido a uma alteração nos mapeamentos de atributos ou filtros de escopo.
- O processo de provisionamento entra em quarentena devido a uma alta taxa de erro. Ele fica em quarentena por mais de quatro semanas, quando é automaticamente desativado.

Para revisar esses eventos e todas as outras atividades realizadas pelo serviço de provisionamento, [saiba como revisar registros e obter relatórios sobre atividades de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

Todas as atividades realizadas pelo serviço de provisionamento são registradas nos registros de auditoria do Azure AD. Você pode encaminhar os registros de auditoria do Azure AD para os registros do Azure Monitor para análise suplementar. Com os logs do Azure Monitor (também conhecido como espaço de trabalho do Log Analytics), você pode consultar dados para encontrar eventos, analisar tendências e executar correlação entre várias fontes de dados. Assista a este [vídeo](https://youtu.be/MP5IaCTwkQg) para saber os benefícios de usar os logs do Azure Monitor para logs Azure AD em cenários práticos do usuário.

Instale as [visualizações de análise de log para logs de atividades do Azure AD](../reports-monitoring/howto-install-use-log-analytics-views.md) para ter acesso a [relatórios pré-construídos](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) em torno de eventos de provisionamento em seu ambiente.

Para obter mais informações, consulte como [analisar os registros de atividades do Azure AD com seus registros do Monitor do Azure](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Gerenciar dados pessoais

O agente de provisionamento Azure AD Connect instalado no servidor Windows cria logs no registro de eventos do Windows que podem conter dados pessoais dependendo do seu aplicativo de RH na nuvem para mapeamentos de atributos do Active Directory. Para cumprir com as obrigações de privacidade do usuário, configure uma tarefa agendada pelo Windows para limpar o registro do evento e garantir que nenhum dado seja mantido além de 48 horas.

O serviço de provisionamento Ad do Azure não gera relatórios, realiza análises ou fornece insights para além de 30 dias porque o serviço não armazena, processa ou mantém quaisquer dados além de 30 dias.

### <a name="troubleshoot"></a>Solução de problemas

Para solucionar problemas que possam aparecer durante o provisionamento, consulte os seguintes artigos:

- [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](application-provisioning-config-problem.md)
- [Sincronize um atributo do seu Active Directory no local para o Azure AD para provisionamento a um aplicativo](user-provisioning-sync-attributes-for-mapping.md)
- [O provisionamento do usuário para um aplicativo da Galeria do Azure AD está levando horas ou mais](application-provisioning-when-will-provisioning-finish.md)
- [Problema para salvar as credenciais de administrador ao configurar o provisionamento do usuário para um aplicativo de galeria do Azure Active Directory](application-provisioning-config-problem-storage-limit.md)
- [Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD](application-provisioning-config-problem-no-users-provisioned.md)
- [O conjunto errado de usuários está sendo provisionado para um aplicativo da Galeria do Azure AD](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Configurar o Visualizador de Eventos do Windows para solucionar problemas do agente](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configurar os logs de auditoria do portal do Azure para solucionar problemas de serviço](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Entender logs para operações de criação da conta de usuário do AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Entendendo os logs para operações de atualização do Manager](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Resolver erros comumente encontrados](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Próximas etapas

- [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
- [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Pular a exclusão de contas de usuário que saem do escopo](skip-out-of-scope-deletions.md)
- [Azure AD Connect Provisioning Agent: Histórico de lançamento de versão](provisioning-agent-release-version-history.md)
