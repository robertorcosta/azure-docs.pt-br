---
title: Arquivos de novidades na Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure de seis meses atrás e antes.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: e802f798ade5e6bfe0b40b17bbf15df5387ef7c3
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357837"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Arquivos de novidades na Central de Segurança do Azure?

A página principal [Novidades na Central de Segurança do Azure?](release-notes.md) contém atualizações dos últimos seis meses, enquanto essa página contém itens mais antigos.

Esta página apresenta informações sobre:

- Novos recursos
- Correções de bug
- Funcionalidades preteridas




## <a name="may-2020"></a>Maio de 2020

As atualizações de maio incluem:
- [Regras de supressão de alertas (versão prévia)](#alert-suppression-rules-preview)
- [A avaliação de vulnerabilidades de máquinas virtuais já está em disponibilidade geral](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [As recomendações personalizadas foram movidas para um controle de segurança separado](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Adicionada alternância para exibir recomendações em controles ou como uma lista simples](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Controle de segurança expandido "Implementar melhores práticas de segurança"](#expanded-security-control-implement-security-best-practices)
- [Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regras de supressão de alertas (versão prévia)

Esse novo recurso (atualmente em versão prévia) ajuda a reduzir a fadiga causada por alertas. Use regras para ocultar automaticamente os alertas que são conhecidos como inócuos ou relacionados a atividades normais em sua organização. Isso permite que você se concentre nas ameaças mais relevantes. 

Os alertas que corresponderem às regras de supressão habilitadas ainda serão gerados, mas o estado deles será definido como ignorado. Você poderá ver o estado no portal do Azure ou de qualquer outra maneira que você acessar os alertas de segurança da Central de Segurança.

As regras de supressão definem os critérios para os quais os alertas devem ser automaticamente ignorados. Normalmente, você usaria uma regra de supressão para:

- suprimir alertas que você identificou como falsos positivos

- suprimir alertas que estão sendo disparados com frequência demais para serem úteis

Saiba mais sobre a [supressão de alertas da Proteção contra Ameaças da Central de Segurança do Azure](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A avaliação de vulnerabilidades de máquinas virtuais já está em disponibilidade geral

A camada Standard da Central de Segurança agora inclui uma avaliação de vulnerabilidade integrada para máquinas virtuais sem cobrança de nenhum valor adicional. Essa extensão é da plataforma Qualys, mas relata as descobertas dela diretamente para a Central de Segurança. Você não precisa de uma licença do Qualys nem de uma conta do Qualys: tudo é tratado diretamente na Central de Segurança.

A nova solução pode verificar continuamente suas máquinas virtuais para encontrar vulnerabilidades e apresentar as descobertas na Central de Segurança. 

Para implantar a solução, use a nova recomendação de segurança:

"Habilitar a solução de avaliação de vulnerabilidades interna nas máquinas virtuais (da plataforma Qualys)"

Saiba mais sobre [avaliação de vulnerabilidade integrada da Central de Segurança para máquinas virtuais](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)

A Central de Segurança inclui um recurso opcional para proteger as portas de gerenciamento de suas VMs. Isso fornece uma defesa contra a forma mais comum de ataques de força bruta.

Essa atualização traz as seguintes alterações para esse recurso:

- A recomendação que aconselha você a habilitar o JIT em uma VM foi renomeada. O que anteriormente era "O controle de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais" agora é: "As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time".

- A recomendação é disparada somente se há portas de gerenciamento abertas.

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>As recomendações personalizadas foram movidas para um controle de segurança separado

Um dos controles de segurança introduzidos com a classificação de segurança aprimorada foi "Implementar melhores práticas de segurança". Todas as recomendações personalizadas criadas para suas assinaturas foram colocadas automaticamente nesse controle. 

Para facilitar a localização de suas recomendações personalizadas, nós as movemos para um controle de segurança dedicado, "Recomendações personalizadas". Este controle não tem impacto sobre sua classificação de segurança.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Adicionada alternância para exibir recomendações em controles ou como uma lista simples

Os controles de segurança são grupos lógicos de recomendações de segurança relacionadas. Eles refletem suas superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações.

Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Por padrão, suas recomendações são mostradas nos controles de segurança. Nesta atualização em diante, você também pode vê-las como uma lista. Para vê-las como uma lista simples classificada pelo status de integridade dos recursos afetados, use a nova alternância 'Agrupar por controles'. A alternância está acima da lista no portal.

Os controles de segurança (e essa alternância) são parte da nova experiência de classificação de segurança. Lembre-se de enviar seus comentários de dentro do portal.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Alternância Agrupar por controles para recomendações":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Controle de segurança expandido "Implementar melhores práticas de segurança" 

Um dos controles de segurança introduzidos com a classificação de segurança aprimorada é "Implementar melhores práticas de segurança". Quando uma recomendação está nesse controle, ela não afeta a classificação de segurança. 

Com essa atualização, três recomendações foram movidas dos controles nos quais foram originalmente colocadas e transferidas para esse controle de práticas recomendadas. Realizamos esta etapa porque determinamos que o risco dessas três recomendações é menor do que se imaginava inicialmente.

Além disso, duas novas recomendações foram introduzidas e adicionadas a esse controle.

As três recomendações que foram movidas são:

- **A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura** (originalmente, no controle "Habilitar MFA")
- **Contas externas com permissões de leitura devem ser removidas de sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")
- **Um máximo de três proprietários deve ser designado para sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")

As duas novas recomendações adicionadas ao controle são:

- **O agente de configuração convidado deve ser instalado em máquinas virtuais do Windows (versão prévia)** : o uso da [Configuração de Convidado do Azure Policy](../governance/policy/concepts/guest-configuration.md) fornece visibilidade nas máquinas virtuais das configurações de servidor e de aplicativo (somente Windows).

- **O Microsoft Defender Exploit Guard deve ser habilitado nos computadores (versão prévia)** : o Microsoft Defender Exploit Guard utiliza o agente de Configuração de Convidado do Azure Policy. O Exploit Guard tem quatro componentes que são projetados para bloquear dispositivos contra uma ampla variedade de vetores de ataque e comportamentos de bloqueio comumente usados em ataques de malware, permitindo que cada empresa encontre um equilíbrio entre os respectivos requisitos de produtividade e o risco de segurança que enfrenta (somente Windows).

Saiba mais sobre o Microsoft Defender Exploit Guard em [Criar e implantar uma política do Exploit Guard](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral

As políticas personalizadas agora fazem parte da experiência de recomendações da Central de Segurança, da classificação de segurança e do painel de padrões de conformidade regulatória. Esse recurso agora está em disponibilidade geral e permite que você estenda a cobertura de avaliação de segurança da sua organização na Central de Segurança. 

Crie uma iniciativa personalizada no Azure Policy, adicione políticas a ela, integre-a à Central de Segurança do Azure e visualize-a como recomendações.

Agora também adicionamos a opção para editar os metadados de recomendação personalizados. As opções de metadados incluem severidade, etapas de correção, informações sobre ameaças e muito mais.  

Saiba mais sobre [como aprimorar as recomendações personalizadas com informações detalhadas](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos 

Estamos integrando as funcionalidades de detecção do CDA (análise de despejo de memória) do Windows à [detecção de ataque sem arquivos](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). A análise de detecção de ataque sem arquivos acompanha versões aprimoradas dos seguintes alertas de segurança para computadores Windows: Injeção de código descoberta, Módulo do Windows simulado detectado, Shellcode descoberto e Segmento de código suspeito detectado.

Alguns dos benefícios dessa transição:

- **Detecção de malware proativa e oportuna** : a abordagem da CDA envolvia a espera da ocorrência de uma falha e a posterior execução da análise para encontrar artefatos mal-intencionados. O uso da detecção de ataque sem arquivos introduz a identificação proativa de ameaças na memória enquanto elas estão em execução. 

- **Alertas aprimorados** – os alertas de segurança da detecção de ataque sem arquivos incluem aprimoramentos que não estão disponíveis no CDA, como as informações de conexões de rede ativas. 

- **Agregação de alerta** – quando o CDA detecta vários padrões de ataque em um despejo de memória, ele disparou vários alertas de segurança. A detecção de ataque sem arquivos combina todos os padrões de ataque identificados do mesmo processo em um alerta, eliminando a necessidade de correlacionar vários alertas.

- **Requisitos reduzidos em seu workspace do Log Analytics** – despejos de memória que contêm dados potencialmente confidenciais não serão mais carregados em seu workspace do Log Analytics.






## <a name="april-2020"></a>Abril de 2020

As atualizações de abril incluem:
- [Os pacotes de conformidade dinâmica agora estão em disponibilidade geral](#dynamic-compliance-packages-are-now-generally-available)
- [Recomendações de identidade agora incluídas na camada gratuita da Central de Segurança do Azure](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Os pacotes de conformidade dinâmica agora estão em disponibilidade geral

O painel de conformidade regulatória da Central de Segurança do Azure agora inclui **pacotes de conformidade dinâmica** (agora em disponibilidade geral) para acompanhar padrões adicionais do setor e regulatórios.

Os pacotes de conformidade dinâmica podem ser adicionados à sua assinatura ou grupo de gerenciamento na página política de segurança da Central de Segurança. Quando você tiver integrado um padrão ou um parâmetro de comparação, o padrão será exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Um relatório de resumo para qualquer um dos padrões que foram integrados estará disponível para download.

Agora, você pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official e UK NHS**
- **PBMM Federal do Canadá**
- **Azure CIS 1.1.0 (novo)** (que é uma representação mais completa do Azure CIS 1.1.0)

Além disso, adicionamos recentemente o **Azure Security Benchmark** , as diretrizes específicas do Azure criadas pela Microsoft para melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Outros padrões se tornarão compatíveis com o painel à medida que forem disponibilizados.  
 
Saiba mais sobre [como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendações de identidade agora incluídas na camada gratuita da Central de Segurança do Azure

As recomendações de segurança para identidade e acesso na camada gratuita da Central de Segurança do Azure agora estão em disponibilidade geral. Isso faz parte do esforço para tornar gratuitos os recursos do CSPM (Gerenciamento da situação de segurança na nuvem). Até agora, essas recomendações só estavam disponíveis no tipo de preço Standard.

Exemplos de recomendações de identidade e acesso incluem:

- "A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura."
- "Um máximo de três proprietários deve ser designado para sua assinatura."
- "As contas preteridas devem ser removidas de sua assinatura."

Se você tiver assinaturas no tipo de preço gratuito, suas classificações de segurança serão afetadas por essa alteração, pois essas assinaturas nunca foram avaliadas quanto à identidade e segurança de acesso.

Saiba mais sobre [recomendações de identidade e acesso](recommendations-reference.md#recs-identity).

Saiba mais sobre o [monitoramento de identidade e acesso](security-center-identity-access.md).



## <a name="march-2020"></a>Março de 2020

As atualizações em março incluem:

- [A automação do fluxo de trabalho já está em disponibilidade geral](#workflow-automation-is-now-generally-available)
- [Integração da Central de Segurança do Azure ao Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Proteção para o Serviço de Kubernetes do Azure](#protection-for-azure-kubernetes-service)
- [Experiência Just-In-Time aprimorada](#improved-just-in-time-experience)
- [Duas recomendações de segurança para aplicativos Web preteridas](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>A automação do fluxo de trabalho já está em disponibilidade geral

O recurso de automação de fluxo de trabalho da Central de Segurança do Azure agora está em disponibilidade geral. Use-o para disparar automaticamente Aplicativos Lógicos em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que têm a opção de correção rápida disponível.

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de stakeholders relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga e pode aprimorar sua segurança, garantindo que as etapas do processo sejam feitas de maneira rápida, consistente e de acordo com seus requisitos predefinidos.

Para obter mais informações sobre as funcionalidades automáticas e manuais da Central de Segurança do Azure para executar seus fluxos de trabalho, confira [automação de fluxo de trabalho](workflow-automation.md).

Saiba mais sobre a [criação de Aplicativos Lógicos](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integração da Central de Segurança do Azure ao Windows Admin Center

Agora é possível mover seus servidores Windows locais do Windows Admin Center diretamente para a Central de Segurança do Azure. A Central de Segurança se torna seu único painel de controle para ver informações de segurança para todos os seus recursos do Windows Admin Center, incluindo servidores locais, máquinas virtuais e cargas de trabalho de PaaS adicionais.

Depois de mover um servidor do Windows Admin Center para a Central de Segurança do Azure, você poderá:

- Exibir alertas de segurança e recomendações na extensão da Central de Segurança do Windows Admin Center.
- Exibir a postura de segurança e recuperar informações detalhadas adicionais dos servidores gerenciados do Windows Admin Center na Central de Segurança dentro do portal do Azure (ou por meio de uma API).

Saiba mais sobre [como integrar a Central de Segurança do Azure ao Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Proteção para o Serviço de Kubernetes do Azure

A Central de Segurança do Azure está expandindo os respectivos recursos de segurança de contêiner para proteger o AKS (Serviço de Kubernetes do Azure).

A popular plataforma de software livre do Kubernetes foi adotada tão amplamente que ela agora é um padrão do setor para orquestração de contêineres. Apesar dessa implementação generalizada, ainda há uma falta de compreensão sobre como proteger um ambiente do Kubernetes. A defesa das superfícies de ataque de um aplicativo em contêineres exige experiência para garantir que a infraestrutura seja configurada de modo seguro e constante para possíveis ameaças.

A defesa da Central de Segurança inclui:

- **Descoberta e visibilidade** – descoberta contínua de instâncias gerenciadas do AKS nas assinaturas registradas na Central de Segurança.
- **Recomendações de segurança** – recomendações acionáveis para ajudá-lo a manter a conformidade com as melhores práticas de segurança para o AKS. Essas recomendações estão incluídas na sua classificação de segurança para garantir que elas sejam exibidas como parte da postura de segurança de sua organização. Um exemplo de uma recomendação relacionada ao AKS que você pode ver é "O controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço de Kubernetes".
- **Proteção contra ameaças** – por meio da análise contínua de sua implantação do AKS, a Central de Segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível de cluster do host e do AKS.

Saiba mais sobre a [Integração do Serviço de Kubernetes do Azure à Central de Segurança](defender-for-kubernetes-introduction.md).

Saiba mais sobre os [recursos de segurança de contêiner na Central de Segurança](container-security.md).


### <a name="improved-just-in-time-experience"></a>Experiência Just-In-Time aprimorada

Os recursos, a operação e a interface do usuário das ferramentas Just-In-Time da Central de Segurança do Azure que protegem suas portas de gerenciamento foram aprimoradas da seguinte maneira: 

- **Campo de justificativa** – ao solicitar acesso a uma VM (máquina virtual) por meio da página Just-In-Time do portal do Azure, um novo campo opcional estará disponível para inserir uma justificativa para a solicitação. As informações inseridas nesse campo podem ser acompanhadas no log de atividades. 
- **Limpeza automática de regras JIT (Just-In-Time) redundantes** – sempre que você atualiza uma política JIT, uma ferramenta de limpeza é executada automaticamente para verificar a validade de todo o conjunto de regras. A ferramenta procura incompatibilidades entre as regras em sua política e as regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, ela determinará a causa e, quando for seguro, removerá as regras internas que não forem mais necessárias. O limpador nunca exclui regras que você criou. 

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Duas recomendações de segurança para aplicativos Web preteridas

Duas recomendações de segurança relacionadas a aplicativos Web estão sendo preteridas: 

- As regras para aplicativos Web nos NSGs da IaaS devem ser fortalecidas.
    (Política relacionada: as regras de NSGs para aplicativos Web em IaaS devem ser fortalecidas)

- O acesso aos Serviços de Aplicativos deve ser restrito.
    (Política relacionada: o acesso aos Serviços de Aplicativos deve ser restrito [Versão Prévia])

Essas recomendações não serão mais exibidas na lista de recomendações da Central de Segurança. As políticas relacionadas não serão mais incluídas na iniciativa denominada "Padrão da Central de Segurança".

Saiba mais sobre [recomendações de segurança](recommendations-reference.md).




## <a name="february-2020"></a>Fevereiro de 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Detecção de ataque sem arquivos para Linux (versão prévia)

Já que os invasores usam métodos cada vez mais difíceis de detectar, a Central de Segurança do Azure está estendendo a detecção de ataque sem arquivos para o Linux, além do Windows. Ataques sem arquivos exploram vulnerabilidades de software, injetam cargas mal-intencionadas em processos de sistema benignos e ficam ocultos na memória. Essas técnicas:

- minimizam ou eliminam sinais de malware no disco
- reduzem significativamente as chances de detecção por soluções de verificação de malware baseadas em disco

Para combater essa ameaça, a Central de Segurança do Azure liberou a detecção de ataque sem arquivos para Windows em outubro de 2018 e agora ampliou a detecção de ataque sem arquivos para o Linux também. 



## <a name="january-2020"></a>Janeiro de 2020

### <a name="enhanced-secure-score-preview"></a>Classificação de segurança aprimorada (versão prévia)

Uma versão aprimorada do recurso de classificação de segurança da Central de Segurança do Azure agora está disponível em versão prévia. Nesta versão, várias recomendações são agrupadas em Controles de Segurança que refletem melhor suas superfícies de ataque vulneráveis (por exemplo, restringir o acesso às portas de gerenciamento).

Familiarize-se com as alterações classificação de segurança durante a fase de versão prévia e determine outras correções que o ajudarão a proteger ainda mais seu ambiente.

Saiba mais sobre a [Pontuação segura aprimorada (visualização)](secure-score-security-controls.md).



## <a name="november-2019"></a>Novembro de 2019

As atualizações em novembro incluem:
 - [Proteção contra ameaças para Azure Key Vault em regiões América do Norte (visualização)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [A Proteção contra Ameaças para o Armazenamento do Microsoft Azure inclui Triagem de Reputação de Malware](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automação de fluxo de trabalho com Aplicativos Lógicos (versão preliminar)](#workflow-automation-with-logic-apps-preview)
 - [Correção rápida para recursos em massa disponíveis para o público geral](#quick-fix-for-bulk-resources-generally-available)
 - [Verificar se há vulnerabilidades em imagens de contêiner (versão preliminar)](#scan-container-images-for-vulnerabilities-preview)
 - [Padrões adicionais de conformidade regulatória (versão preliminar)](#additional-regulatory-compliance-standards-preview)
 - [Proteção contra ameaças para o Serviço de Kubernetes do Azure (versão preliminar)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Avaliação de vulnerabilidades de máquinas virtuais (versão preliminar)](#virtual-machine-vulnerability-assessment-preview)
 - [Segurança de Dados Avançada para o SQL Server em Máquinas Virtuais do Microsoft Azure (versão preliminar)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Compatibilidade com políticas personalizadas (versão preliminar)](#support-for-custom-policies-preview)
 - [Estender a cobertura da Central de Segurança do Azure com plataforma para comunidade e parceiros](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Integrações avançadas com a exportação de recomendações e alertas (versão preliminar)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Carregar servidores locais na Central de Segurança do Centro de Administração do Windows (versão preliminar)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Proteção contra ameaças para Azure Key Vault em regiões América do Norte (visualização)

O Azure Key Vault é um serviço essencial para proteger dados e melhorar o desempenho de aplicativos na nuvem, oferecendo a capacidade de gerenciar centralmente chaves, segredos, chaves de criptografia e políticas na nuvem. Como o Azure Key Vault armazena dados confidenciais e críticos para os negócios, ele exige segurança máxima para os cofres de chaves e os dados armazenados neles.

O suporte da Central de Segurança do Azure para proteção contra ameaças para Azure Key Vault oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar cofres de chaves. Essa nova camada de proteção permite que os clientes resolvam ameaças contra seus cofres de chaves sem serem especialistas em segurança ou gerenciar sistemas de monitoramento de segurança. O recurso está em versão preliminar pública em regiões da América do Norte.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>A Proteção contra Ameaças para o Armazenamento do Microsoft Azure inclui Triagem de Reputação de Malware

A proteção contra ameaças para o Armazenamento do Azure oferece novas detecções alimentadas pela Inteligência contra Ameaças da Microsoft para detectar carregamentos de malware no Armazenamento do Azure usando análise de reputação de hash e acesso suspeito de um nó de saída de Tor ativo (um proxy de anonimato). Agora você pode ver o malware detectado em contas de armazenamento usando a Central de Segurança do Azure.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automação de fluxo de trabalho com Aplicativos Lógicos (versão preliminar)

As organizações com segurança gerenciada centralmente e TI/operações implementam processos de fluxo de trabalho integrados para impulsionar a ação necessária na organização quando as discrepâncias são descobertas em seus ambientes. Em muitos casos, esses fluxos de trabalho são processos repetíveis, e a automação pode simplificar muito os processos na organização.

Hoje, estamos introduzindo um novo recurso na Central de Segurança que permite que os clientes criem configurações de automação aproveitando os Aplicativos Lógicos do Azure e criem políticas que vão dispará-los automaticamente com base em descobertas de ASC específicas, como Recomendações ou Alertas. O Aplicativo Lógico do Azure pode ser configurado para realizar qualquer ação personalizada com suporte pela vasta comunidade de conectores de Aplicativos Lógicos ou usar um dos modelos fornecidos pela Central de Segurança, como enviar um email ou abrir um tíquete de do ServiceNow™.

Para obter mais informações sobre as funcionalidades automáticas e manuais da Central de Segurança do Azure para executar seus fluxos de trabalho, confira [automação de fluxo de trabalho](workflow-automation.md).

Para saber mais sobre a criação de Aplicativos Lógicos, consulte [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Correção rápida para recursos em massa disponíveis para o público geral

Com as muitas tarefas que um usuário recebe como parte da Classificação de Segurança, a capacidade de corrigir efetivamente os problemas em um grande frota pode se tornar desafiador.

Para simplificar a correção de configurações incorretas de segurança e conseguir corrigir rapidamente as recomendações em uma grande quantidade de recursos e melhorar sua Classificação de Segurança, use a Correção Rápida.

Essa operação permitirá selecionar os recursos para os quais você deseja aplicar a correção e iniciar uma ação de correção que definirá a configuração em seu nome.

A Correção Rápida geralmente está disponível para clientes atuais como parte da página de recomendações da Central de Segurança.

Veja quais recomendações têm Correção Rápida habilitada no [guia de referência para recomendações de segurança](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Verificar se há vulnerabilidades em imagens de contêiner (versão preliminar)

A Central de Segurança do Azure agora pode verificar as imagens de contêiner no Registro de Contêiner do Azure em busca de vulnerabilidades.

A verificação de imagem funciona analisando o arquivo de imagem de contêiner e, em seguida, verificando se há alguma vulnerabilidade conhecida (da plataforma Qualys).

A varredura em si é disparada automaticamente ao enviar por push novas imagens de contêiner para o Registro de Contêiner do Azure. As vulnerabilidades encontradas surgirão como recomendações da Central de Segurança e incluídas na Classificação de Segurança do Azure, com informações sobre como corrigi-las para reduzir a superfície de ataque permitida.


### <a name="additional-regulatory-compliance-standards-preview"></a>Padrões adicionais de conformidade regulatória (versão preliminar)

O painel de Conformidade Regulatória mostra informações sobre sua postura de conformidade com base nas avaliações da Central de Segurança. O painel mostra como o seu ambiente está em conformidade com os controles e requisitos designados por padrões regulatórios específicos e benchmarks do setor e fornece recomendações prescritivas sobre como lidar com esses requisitos.

O painel de conformidade regulatória oferece, até o momento, suporte a quatro padrões integrados:  Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Agora estamos anunciando a versão preliminar de padrões adicionais compatíveis: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM e UK Official com UK NHS. Também estamos lançando uma versão atualizada do Azure CIS 1.1.0, abrangendo mais controles do padrão e aprimorando a extensibilidade.

Saiba mais sobre [como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Proteção contra ameaças para o Serviço de Kubernetes do Azure (versão preliminar)

O Kubernetes está se tornando rapidamente o novo padrão para implantar e gerenciar software na nuvem. Poucas pessoas têm ampla experiência com Kubernetes e muitos se concentram apenas em engenharia e administração geral e ignoram o aspecto de segurança. O ambiente Kubernetes precisa ser configurado com cuidado para ser seguro, garantindo que nenhuma das portas da superfície de ataque focadas no contêiner seja deixada aberta é exposta para invasores. A Central de Segurança está expandindo seu suporte no espaço do contêiner para um dos serviços de crescimento mais rápido no Azure: AKS (Serviço de Kubernetes do Azure).

Os novos recursos dessa versão preliminar pública incluem:

- **Descoberta e visibilidade** : descoberta contínua de instâncias do AKS gerenciadas nas assinaturas registradas da Central de Segurança.
- **Recomendações de Classificação de Segurança** : itens acionáveis para ajudar os clientes a cumprir as melhores práticas de segurança no AKS como parte da Classificação de Segurança do cliente, por exemplo, "o controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço do Kubernetes".
- **Detecção de ameaças** : análise baseada em cluster e host, como "um contêiner privilegiado detectado".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Avaliação de vulnerabilidades de máquinas virtuais (versão preliminar)

Os aplicativos instalados em máquinas virtuais geralmente podem ter vulnerabilidades que poderiam levar a uma violação da máquina virtual. Estamos anunciando que a camada Standard da central de segurança inclui a avaliação de vulnerabilidades interna para máquinas virtuais sem nenhuma taxa adicional. A avaliação de vulnerabilidade, fornecida pela Qualys na versão preliminar pública, permitirá que você examine continuamente todos os aplicativos instalados em uma máquina virtual para encontrar aplicativos vulneráveis e apresente as descobertas na experiência do portal da Central de Segurança. A Central de Segurança cuida de todas as operações de implantação para que não seja necessário nenhum trabalho extra do usuário. No futuro, estamos planejando fornecer opções de avaliação de vulnerabilidade para dar suporte às necessidades de negócios únicas de nossos clientes.

[Saiba mais sobre avaliações de vulnerabilidades para suas máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança de Dados Avançada para o SQL Server em Máquinas Virtuais do Microsoft Azure (versão preliminar)

O suporte da Central de Segurança do Azure para proteção contra ameaças e a avaliação de vulnerabilidade para bancos de dados SQL em execução em VMs de IaaS agora está em versão preliminar.

A [Avaliação de vulnerabilidade ](../azure-sql/database/sql-vulnerability-assessment.md) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. Ele fornece visibilidade da sua postura de segurança como parte da Classificação de Segurança do Azure e inclui as etapas para resolver problemas de segurança e aprimorar as fortificações do banco de dados.

A [Proteção Avançada contra Ameaças](../azure-sql/database/threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu SQL Server. Monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança orientados a ações em padrões anormais de acesso de banco de dados. Esses alertas mostram os detalhes da atividade suspeita e as ações recomendadas para investigar e atenuar a ameaça.


### <a name="support-for-custom-policies-preview"></a>Compatibilidade com políticas personalizadas (versão preliminar)

A Central de Segurança do Azure agora permite políticas personalizadas (em versão preliminar).

Nossos clientes querem estender sua cobertura atual de avaliações de segurança na Central de Segurança com suas próprias avaliações, com base nas políticas que elas criam no Azure Policy. Com o suporte a políticas personalizadas, isso agora é possível.

As políticas personalizadas agora serão parte da experiência de recomendações da Central de Segurança, da Classificação de Segurança e do painel de padrões de conformidade regulatória. Com o suporte a políticas personalizadas, agora você pode criar uma iniciativa personalizada no Azure Policy e, em seguida, adicioná-la como uma política na Central de Segurança e visualizá-la como uma recomendação.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Estender a cobertura da Central de Segurança do Azure com plataforma para comunidade e parceiros

Use a Central de Segurança para receber recomendações não apenas da Microsoft, mas também de soluções existentes de parceiros como Check Point, Tenable e CyberArk, com muito mais integrações chegando.  O fluxo de integração simples da Central de Segurança pode conectar suas soluções existentes à Central de Segurança, permitindo que você exiba suas recomendações de postura de segurança em um único lugar, execute relatórios unificados e aproveite todos os recursos da Central de Segurança em relação às recomendações integradas e de parceiros. Você também pode exportar recomendações da Central de Segurança para produtos de parceiros.

[Saiba mais sobre a associação de segurança inteligente da Microsoft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrações avançadas com a exportação de recomendações e alertas (versão preliminar)

Para habilitar cenários de nível empresarial na Central de Segurança, agora é possível consumir alertas e recomendações da Central de Segurança em locais adicionais, exceto o portal do Azure ou a API. Eles podem ser exportados diretamente para um hub de eventos e para workspaces do Log Analytics. Aqui estão alguns fluxos de trabalho que você pode criar com base nesses novos recursos:

- Com a exportação para workspaces do Log Analytics, você pode criar painéis personalizados com o Power BI.
- Com a exportação para o hub de eventos, você poderá exportar alertas e recomendações da Central de Segurança para seus SIEMs de terceiros, para uma solução de terceiros em tempo real ou o Data Explorer do Azure.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Carregar servidores locais na Central de Segurança do Centro de Administração do Windows (versão preliminar)

O Centro de Administração do Windows é um portal de gerenciamento para servidores Windows que não estão implantados no Azure, oferecendo vários recursos de gerenciamento do Azure, como atualizações de sistema e backup. Recentemente, adicionamos um recurso para carregar esses servidores não Azure para serem protegidos pelo Certificado do Serviço de Aplicativo diretamente do Centro de Administração do Windows.

Com essa nova experiência, os usuários poderão integrar um servidor WAC à Central de Segurança do Azure e habilitar a exibição de seus alertas de segurança e recomendações diretamente no Centro de Administração do Windows.


## <a name="september-2019"></a>Setembro de 2019

As atualizações de setembro incluem:

 - [Gerenciar regras com aprimoramentos de controles de aplicativos adaptáveis](#managing-rules-with-adaptive-application-controls-improvements)
 - [Controlar a recomendação de segurança do contêiner usando Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gerenciar regras com aprimoramentos de controles de aplicativos adaptáveis

A experiência de gerenciar regras para máquinas virtuais usando controles de aplicativos adaptáveis foi aprimorada. Os controles de aplicativo adaptáveis da Central de Segurança do Azure ajudam a controlar quais aplicativos podem ser executados em suas máquinas virtuais. Além de uma melhoria geral no gerenciamento de regras, um novo benefício permite que você controle quais tipos de arquivo serão protegidos quando você adicionar uma nova regra.

[Saiba mais sobre controles de aplicativo adaptáveis](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Controlar a recomendação de segurança do contêiner usando Azure Policy

A recomendação da Central de Segurança do Azure para corrigir vulnerabilidades em segurança de contêiner agora pode ser habilitada ou desabilitada pelo Azure Policy.

Para exibir as políticas de segurança habilitadas, na Central de Segurança, abra a página Política de Segurança.


## <a name="august-2019"></a>Agosto de 2019

As atualizações de agosto incluem:

 - [Acesso à VM just-in-time (JIT) para o Firewall do Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Correção com um só clique para impulsionar sua postura de segurança (versão preliminar)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Gerenciamento entre locatários](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Acesso à VM just-in-time (JIT) para o Firewall do Azure 

O acesso à VM just-in-time (JIT) para o Firewall do Azure já está disponível para o público geral. Use-o para proteger seus ambientes protegidos por Firewall do Azure, além de seus ambientes protegidos por grupo de segurança de rede.

O acesso à VM JIT reduz a exposição a ataques volumétricos de rede, fornecendo acesso controlado às VMs somente quando necessário, usando suas regras de grupo de segurança de rede e Firewall do Azure.

Quando você habilita o JIT para suas VMs, cria uma política que determina as portas a serem protegidas, por quanto tempo as portas devem permanecer abertas e os endereços IP aprovados de onde essas portas podem ser acessadas. Essa política ajuda você a manter o controle do que os usuários podem fazer quando solicitam acesso.

As solicitações são registradas no log de atividades do Azure, para que você possa monitorar e auditar facilmente o acesso. A página just-in-time também ajuda a identificar rapidamente as VMs existentes que têm o JIT habilitado e as VMs em que o JIT é recomendado.

[Saiba mais sobre o Firewall do Azure](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correção com um só clique para impulsionar sua postura de segurança (versão preliminar)

A pontuação segura é uma ferramenta que ajuda a avaliar sua postura de segurança de carga de trabalho. Ela analisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações devem ser executadas primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação.

Para simplificar a correção de configurações incorretas de segurança e ajudá-lo a melhorar rapidamente sua classificação de segurança, adicionamos um novo recurso que permite que você corrija uma recomendação em uma grande quantidade de recursos em um único clique.

Essa operação permitirá selecionar os recursos para os quais você deseja aplicar a correção e iniciar uma ação de correção que definirá a configuração em seu nome.

Veja quais recomendações têm Correção Rápida habilitada no [guia de referência para recomendações de segurança](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gerenciamento entre locatários

A Central de Segurança agora dá suporte a cenários de gerenciamento entre locatários como parte do Azure Lighthouse. Isso permite que você tenha visibilidade e gerencie a postura de segurança de vários locatários na Central de Segurança. 

[Saiba mais sobre as experiências de gerenciamento entre locatários](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Julho de 2019

### <a name="updates-to-network-recommendations"></a>Atualizações das recomendações de rede

A Central de Segurança do Azure (ASC) lançou novas recomendações de rede e melhorou algumas delas. Agora, o uso da Central de Segurança garante uma proteção de rede ainda maior para seus recursos. 

[Saiba mais sobre as recomendações de rede](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Junho de 2019

### <a name="adaptive-network-hardening---generally-available"></a>Proteção de rede adaptável: disponível para o público geral

Uma das maiores superfícies de ataque para cargas de trabalho executadas na nuvem pública são as conexões de e para a Internet pública. Nossos clientes acham difícil saber quais regras do Grupo de Segurança de Rede (NSG) devem estar em vigor para garantir que as cargas de trabalho do Azure estejam disponíveis apenas para os intervalos de origem necessários. Com esse recurso, a Central de Segurança aprende o tráfego de rede e os padrões de conectividade das cargas de trabalho do Azure e mostra as recomendações de regras do grupo de segurança de rede para máquinas virtuais voltadas para a Internet. Isso ajuda nossos clientes a configurar melhor suas políticas de acesso à rede e limitar sua exposição a ataques. 

[Saiba mais sobre a proteção de rede adaptável](security-center-adaptive-network-hardening.md).