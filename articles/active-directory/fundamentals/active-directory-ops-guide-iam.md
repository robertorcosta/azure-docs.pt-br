---
title: Guia de referência de operações de gerenciamento de gerenciamento de acesso e identidade do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve tomar para proteger as operações de gerenciamento de identidade e acesso
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298607"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Guia de referência de operações de gerenciamento de gerenciamento de acesso e identidade do Azure Active Directory

Esta seção do guia de referência de [operações azure AD](active-directory-ops-guide-intro.md) descreve as verificações e ações que você deve considerar para proteger e gerenciar o ciclo de vida das identidades e suas atribuições.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar com o tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

O gerenciamento do Azure Active Directory requer a execução contínua das principais tarefas operacionais e processos que podem não fazer parte de um projeto de implantação. Ainda é importante que você configure essas tarefas para manter seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Defina o processo de como criar assinaturas do Azure | Varia de acordo com a organização |
| Decida quem recebe licenças de Mobilidade Corporativa + Segurança | Equipe de Operações IAM |
| Decida quem recebe licenças do Office 365 | Equipe de Produtividade |
| Decida quem recebe outras licenças, por exemplo, Dynamics, VSO | Proprietário do Aplicativo |
| Atribuir licenças | Equipe de Operações IAM |
| Solucionar problemas e corrigir erros de atribuição de licença | Equipe de Operações IAM |
| Provisão de identidades para aplicativos no Azure AD | Equipe de Operações IAM |

Ao revisar sua lista, você pode achar que precisa atribuir um proprietário para tarefas que estão faltando a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhados com as recomendações acima.

#### <a name="assigning-owners-recommended-reading"></a>Atribuindo aos proprietários a leitura recomendada

- [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governança no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Sincronização de identidade no local

### <a name="identify-and-resolve-synchronization-issues"></a>Identificar e resolver problemas de sincronização

A Microsoft recomenda que você tenha uma boa linha de base e compreensão dos problemas em seu ambiente local que podem resultar em problemas de sincronização para a nuvem. Uma vez que ferramentas automatizadas como [o IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) e [o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) podem gerar um alto volume de falsos positivos, recomendamos identificar erros de sincronização que foram deixados sem solução por mais de 100 dias limpando esses objetos por engano. Erros de sincronização não resolvidos a longo prazo podem gerar incidentes de suporte. [A solução de problemas durante](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) a sincronização fornece uma visão geral de diferentes tipos de erros de sincronização, alguns dos possíveis cenários que causam esses erros e formas potenciais de corrigir os erros.

### <a name="azure-ad-connect-sync-configuration"></a>Configuração do Azure AD Connect Sync

Para permitir todas as experiências híbridas, a postura de segurança baseada em dispositivos e a integração com o Azure AD, é necessário que você sincronize contas de usuário que seus funcionários usam para fazer login em seus desktops.

Se você não sincronizar o login dos usuários da floresta, então você deve alterar a sincronização para vir da floresta adequada.

#### <a name="synchronization-scope-and-object-filtering"></a>Escopo de sincronização e filtragem de objetos

A remoção de baldes conhecidos de objetos que não são necessários para serem sincronizados tem os seguintes benefícios operacionais:

- Menos fontes de erros de sincronização
- Ciclos de sincronização mais rápidos
- Menos "lixo" para levar adiante a partir de locais, por exemplo, a poluição da lista de endereços globais para contas de serviços no local que não são relevantes na nuvem

> [!NOTE]
> Se você descobrir que está importando muitos objetos que não estão sendo exportados para a nuvem, você deve filtrar por OU ou atributos específicos.

Exemplos de objetos a serem excluidos são:

- Contas de serviço que não são usadas para aplicativos em nuvem
- Grupos que não devem ser usados em cenários de nuvem, como os usados para conceder acesso a recursos
- Usuários ou contatos que são identidades externas que devem ser representados com a Colaboração Azure AD B2B
- Contas de computador onde os funcionários não são destinados a acessar aplicativos em nuvem de, por exemplo, servidores

> [!NOTE]
> Se uma única identidade humana tiver várias contas provisionadas de algo como migração de domínio legado, fusão ou aquisição, você só deve sincronizar a conta usada pelo usuário no dia-a-dia, por exemplo, o que eles usam para fazer login em seu computador .

Idealmente, você vai querer chegar a um equilíbrio entre reduzir o número de objetos para sincronizar e a complexidade nas regras. Geralmente, uma combinação entre [filtragem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) de OU/contêiner mais um mapeamento de atributo simples para o atributo cloudFiltered é uma combinação eficaz de filtragem.

> [!IMPORTANT]
> Se você usar filtragem de grupo na produção, você deve fazer a transição para outra abordagem de filtragem.

#### <a name="sync-failover-or-disaster-recovery"></a>Sincronizar failover ou recuperação de desastres

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o Sync Server ficar offline por qualquer motivo, as alterações no local não podem ser atualizadas na nuvem e podem resultar em problemas de acesso para os usuários. Portanto, é importante definir uma estratégia de failover que permita aos administradores retomar rapidamente a sincronização depois que o servidor de sincronização ficar off-line. Tais estratégias podem se enquadrar nas seguintes categorias:

- **Implantar o Azure AD Connect Server(s) no modo de preparação** - permite que um administrador "promova" o servidor de preparação para a produção por um simples switch de configuração.
- **Usar virtualização** - Se o ad(Azure AD connect for implantado em uma máquina virtual ( VM), os administradores podem aproveitar sua pilha de virtualização para migrar ao vivo ou reimplantar rapidamente a VM e, portanto, retomar a sincronização.

Se sua organização não estiver com uma estratégia de recuperação de desastres e failover para o Sync, você não hesitará em implantar o Azure AD Connect no Modo de Preparação. Da mesma forma, se houver uma incompatibilidade entre sua configuração de produção e de preparação, você deve refazer a linha de base do modo de preparação do Azure AD Connect para corresponder à configuração de produção, incluindo versões e configurações de software.

![Uma captura de tela da configuração do modo de preparação do Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Mantenha-se atualizado

A Microsoft atualiza o Azure AD Connect regularmente. Mantenha-se atualizado para aproveitar as melhorias de desempenho, correções de bugs e novos recursos que cada nova versão fornece.

Se a sua versão do Azure AD Connect estiver com mais de seis meses de atraso, você deve atualizar para a versão mais recente.

#### <a name="source-anchor"></a>Âncora de origem

O uso **de consistência ms-DS** como [âncora de origem](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) permite uma migração mais fácil de objetos entre florestas e domínios, o que é comum na consolidação/limpeza do domínio AD, fusões, aquisições e alienações.

Se você estiver usando o **ObjectGuid** como âncora de origem, recomendamos que você mude para usar **ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Regras personalizadas

As regras personalizadas do Azure AD Connect fornecem a capacidade de controlar o fluxo de atributos entre objetos no local e objetos de nuvem. No entanto, o uso excessivo ou o uso indevido de regras personalizadas podem introduzir os seguintes riscos:

- Complexidade de solução de problemas
- Degradação do desempenho ao realizar operações complexas entre objetos
- Maior probabilidade de divergência de configuração entre o servidor de produção e o servidor de estágio
- Sobrecarga adicional ao atualizar o Azure AD Connect se as regras personalizadas forem criadas dentro da precedência maior que 100 (usada por regras incorporadas)

Se você está usando regras excessivamente complexas, você deve investigar as razões da complexidade e encontrar oportunidades de simplificação. Da mesma forma, se você criou regras personalizadas com valor de precedência acima de 100, você deve corrigir as regras para que elas não estejam em risco ou em conflito com o conjunto padrão.

Exemplos de uso indevido de regras personalizadas incluem:

- **Compensar os dados sujos no diretório** - Neste caso, recomenda-se trabalhar com os proprietários da equipe de AD e limpar os dados no diretório como uma tarefa de remediação, e ajustar processos para evitar a reintrodução de dados ruins.
- **Remediação pontual de usuários individuais** - É comum encontrar regras que casos especiais outliers, geralmente por causa de um problema com um usuário específico.
- **"CloudFiltering" supercomplicado** - Embora reduzir o número de objetos seja uma boa prática, há o risco de criar e complicações descomplicadas o escopo de sincronização usando muitas regras de sincronização. Se houver uma lógica complexa para incluir/excluir objetos além da filtragem de OU, recomenda-se lidar com essa lógica fora da sincronização e rotular os objetos com um simples atributo "cloudFiltered" que pode fluir com uma regra de sincronização simples.

#### <a name="azure-ad-connect-configuration-documenter"></a>Documentador de configuração do Azure AD Connect

O [Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) é uma ferramenta que você pode usar para gerar documentação de uma instalação do Azure AD Connect para permitir uma melhor compreensão da configuração de sincronização, criar confiança em acertar as coisas e saber o que foi alterado quando você aplicou uma nova compilação ou configuração do Azure AD Connect ou adicionou ou atualizou as regras de sincronização personalizadas. Os recursos atuais da ferramenta incluem:

- Documentação da configuração completa da sincronização Azure AD Connect.
- Documentação de quaisquer alterações na configuração de dois servidores de sincronização Do Azure AD Connect ou alterações de uma determinada linha de base de configuração.
- Geração de um script de implantação do PowerShell para migrar as diferenças de regras de sincronização ou personalizações de um servidor para outro.

## <a name="assignment-to-apps-and-resources"></a>Atribuição a aplicativos e recursos

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenciamento em grupo para serviços de nuvem da Microsoft

O Azure Active Directory simplifica o gerenciamento de licenças por meio [de licenciamento em grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) para serviços de nuvem da Microsoft. Dessa forma, o IAM fornece a infra-estrutura do grupo e a gestão delegada desses grupos às equipes adequadas nas organizações. Existem várias maneiras de configurar a adesão de grupos no Azure AD, incluindo:

- **Sincronizados a partir de locais** - Os grupos podem vir de diretórios locais, o que pode ser um bom ajuste para organizações que estabeleceram processos de gerenciamento de grupos que podem ser estendidos para atribuir licenças no escritório 365.

- **Baseado em atributos / dinâmico** - Grupos podem ser criados na nuvem com base em uma expressão baseada em atributos do usuário, por exemplo, Departamento é igual a "vendas". O Azure AD mantém os membros do grupo, mantendo-o consistente com a expressão definida. O uso desse tipo de grupo para atribuição de licença permite uma atribuição de licença baseada em atributos, o que é um bom ajuste para organizações que têm alta qualidade de dados em seu diretório.

- **Propriedade delegada** - Os grupos podem ser criados na nuvem e podem ser designados proprietários. Dessa forma, você pode capacitar os proprietários de negócios, por exemplo, equipe de colaboração ou equipe de BI, para definir quem deve ter acesso.

Se você estiver usando um processo manual para atribuir licenças e componentes aos usuários, recomendamos que você implemente o licenciamento em grupo. Se o processo atual não monitorar erros de licenciamento ou o que é Atribuído versus Disponível, você deve definir melhorias no processo para resolver erros de licenciamento e monitorar a cessão de licenciamento.

Outro aspecto da gestão de licenças é a definição de planos de serviço (componentes da licença) que devem ser habilitados com base nas funções de trabalho na organização. A concessão de acesso a planos de serviço que não são necessários, pode fazer com que os usuários vejam ferramentas no portal do Office para as que não foram treinadas ou não devem estar usando. Ele pode gerar volume adicional de help desk, provisionamento desnecessário e colocar sua conformidade e governança em risco, por exemplo, ao provisionar o OneDrive for Business para indivíduos que podem não ser autorizados a compartilhar conteúdo.

Use as seguintes diretrizes para definir planos de serviço aos usuários:

- Os administradores devem definir "pacotes" de planos de serviço a serem oferecidos aos usuários com base em seu papel, por exemplo, trabalhador de colarinho branco versus trabalhador de piso.
- Crie grupos por cluster e atribua a licença com plano de serviço.
- Opcionalmente, um atributo pode ser definido para segurar os pacotes para os usuários.

> [!IMPORTANT]
> O licenciamento baseado em grupo no Azure AD introduz o conceito de usuários em um estado de erro de licenciamento. Se você notar quaisquer erros de licenciamento, então você deve [identificar e resolver](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) imediatamente quaisquer problemas de cessão de licença.

![Uma captura de tela de uma tela de computador Descrição gerada automaticamente](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Gerenciamento do ciclo de vida

Se você estiver usando atualmente uma ferramenta, como [o Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) ou o sistema de terceiros, que depende de uma infra-estrutura local, recomendamos que você descarregue a atribuição da ferramenta existente, implemente o licenciamento baseado em grupo e defina um gerenciamento do ciclo de vida do grupo com base em [grupos.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups) Da mesma forma, se o processo existente não contabilizar novos funcionários ou funcionários que deixam a organização, você deve implantar o licenciamento baseado em grupo com base em grupos dinâmicos e definir um ciclo de vida de membros de grupo. Finalmente, se o licenciamento baseado em grupo for implantado contra grupos locais que não possuem gerenciamento do ciclo de vida, considere usar grupos de nuvem para habilitar recursos como propriedade delegada ou associação dinâmica baseada em atributos.

### <a name="assignment-of-apps-with-all-users-group"></a>Atribuição de aplicativos com grupo "Todos os usuários"

Os proprietários de recursos podem acreditar que o grupo **All users** contém apenas **funcionários corporativos** quando eles podem realmente conter **funcionários corporativos** e **convidados**. Como resultado, você deve tomar cuidado especial ao usar o grupo **All users** para atribuição de aplicativos e conceder acesso a recursos como conteúdo do SharePoint ou aplicativos.

> [!IMPORTANT]
> Se o grupo **All users** for ativado e usado para políticas de acesso condicional, atribuição de aplicativos ou recursos, certifique-se de proteger [o grupo](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) se você não quiser que ele inclua usuários convidados. Além disso, você deve corrigir suas atribuições de licenciamento criando e atribuindo a grupos que contêm apenas **Funcionários corporativos.** Por outro lado, se você descobrir que o grupo **All users** está habilitado, mas não está sendo usado para conceder acesso aos recursos, certifique-se de que a orientação operacional da sua organização seja usar intencionalmente esse grupo (que inclui funcionários da empresa e **convidados).** **Guests**

### <a name="automated-user-provisioning-to-apps"></a>Provisionamento automatizado de usuários para aplicativos

[O provisionamento automatizado do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) para aplicativos é a melhor maneira de criar um provisionamento consistente, deprovisionamento e ciclo de vida de identidades em vários sistemas.

Se você está atualmente provisionando aplicativos de forma ad-hoc ou usando coisas como arquivos CSV, JIT ou uma solução local que não aborda o gerenciamento do ciclo de vida, recomendamos que você [implemente o provisionamento de aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) com o Azure AD para aplicativos suportados e defina um padrão consistente para aplicativos que ainda não são suportados pelo Azure AD.

![Serviço de provisionamento Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Linha de base do ciclo delta do Azure AD Connect

É importante entender o volume de mudanças em sua organização e ter certeza de que não está demorando muito para ter um tempo de sincronização previsível.

A freqüência [de sincronização delta padrão](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) é de 30 minutos. Se a sincronização delta estiver demorando mais de 30 minutos de forma consistente, ou houver discrepâncias significativas entre o desempenho de sincronização delta de encenação e produção, você deve investigar e rever os [fatores que influenciam o desempenho do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect solução de problemas recomenda leitura recomendada

- [Preparar atributos de diretório para sincronização com o Office 365 usando a ferramenta IdFix - Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Conexão Azure AD: erros de solução de problemas durante a sincronização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Resumo

Há cinco aspectos para uma infra-estrutura de identidade segura. Esta lista irá ajudá-lo a encontrar e tomar rapidamente as ações necessárias para proteger e gerenciar o ciclo de vida das identidades e seus direitos em sua organização.

- Designe proprietários para tarefas-chave.
- Encontre e resolva problemas de sincronização.
- Defina uma estratégia de failover para recuperação de desastres.
- Agilizar o gerenciamento de licenças e atribuição de aplicativos.
- Automatize o provisionamento do usuário para aplicativos.

## <a name="next-steps"></a>Próximas etapas

Comece com as [verificações e ações de gerenciamento de autenticação.](active-directory-ops-guide-auth.md)
