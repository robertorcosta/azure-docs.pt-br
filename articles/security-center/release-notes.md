---
title: Notas sobre a versão da Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 6909bcbc67680f9205af8a79782907d4671d668b
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860708"
---
# <a name="whats-new-in-azure-security-center"></a>Novidades na Central de Segurança do Azure

A segurança do Azure está em desenvolvimento ativo e recebe aprimoramentos continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece a você informações sobre:

- Novos recursos
- Correções de bug
- Funcionalidades preteridas

Esta página é atualizada regularmente, então visite-a com frequência. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo na Central de Segurança do Azure](release-notes-archive.md).


## <a name="may-2020"></a>Maio de 2020


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

Saiba mais sobre [avaliação de vulnerabilidade integrada da Central de Segurança para máquinas virtuais](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)

A Central de Segurança inclui um recurso opcional para proteger as portas de gerenciamento de suas VMs. Isso fornece uma defesa contra a forma mais comum de ataques de força bruta.

Essa atualização traz as seguintes alterações para esse recurso:

- A recomendação que aconselha você a habilitar o JIT em uma VM foi renomeada. O que anteriormente era "O controle de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais" agora é: "As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time".

- A recomendação foi definida para ser disparada somente se houver portas de gerenciamento abertas.

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>As recomendações personalizadas foram movidas para um controle de segurança separado

Um dos controles de segurança introduzidos com a classificação de segurança aprimorada foi "Implementar melhores práticas de segurança". Todas as recomendações personalizadas criadas para suas assinaturas foram colocadas automaticamente nesse controle. 

Para facilitar a localização de suas recomendações personalizadas, nós as movemos para um controle de segurança dedicado, "Recomendações personalizadas". Este controle não tem impacto sobre sua classificação de segurança.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Adicionada alternância para exibir recomendações em controles ou como uma lista simples

Os controles de segurança são grupos lógicos de recomendações de segurança relacionadas. Eles refletem suas superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações.

Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Por padrão, suas recomendações são mostradas nos controles de segurança. Agora, graças à mudança introduzida nesta atualização, você também pode exibi-las como uma lista. Para vê-las como uma lista simples classificada pelo status de integridade dos recursos afetados, use a nova alternância 'Agrupar por controles'. A alternância está acima da lista no portal.

Os controles de segurança (e essa alternância) são parte da nova experiência de classificação de segurança. Lembre-se de enviar seus comentários de dentro do portal.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="expanded-security-control-implement-security-best-practices"></a>Controle de segurança expandido "Implementar melhores práticas de segurança" 

Um dos controles de segurança introduzidos com a classificação de segurança aprimorada foi "Implementar melhores práticas de segurança". Quando uma recomendação está nesse controle, ela não afeta a classificação de segurança. 

Com essa atualização, três recomendações foram movidas dos controles nos quais foram originalmente colocadas e transferidas para esse controle de práticas recomendadas. Realizamos esta etapa porque determinamos que o risco dessas três recomendações é menor do que se imaginava inicialmente.

Além disso, duas novas recomendações foram introduzidas e adicionadas a esse controle.

As três recomendações que foram movidas são:

- **A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura** (originalmente, no controle "Habilitar MFA")
- **Contas externas com permissões de leitura devem ser removidas de sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")
- **Um máximo de três proprietários deve ser designado para sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")

As duas novas recomendações adicionadas ao controle são:

- **[Versão prévia] O agente de configuração convidado deve ser instalado** – o uso da [Configuração de Convidado do Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) fornece visibilidade dentro de máquinas virtuais para configurações de servidor e de aplicativo (somente Windows).

- **[Versão prévia] O Windows Exploit Guard deve ser habilitado** – o Microsoft Defender Exploit Guard utiliza o agente de Configuração de Convidado do Azure Policy. O Exploit Guard tem quatro componentes que são projetados para bloquear dispositivos contra uma ampla variedade de vetores de ataque e comportamentos de bloqueio comumente usados em ataques de malware, permitindo que cada empresa encontre um equilíbrio entre os respectivos requisitos de produtividade e o risco de segurança que enfrenta (somente Windows).

Saiba mais sobre o Microsoft Defender Exploit Guard em [Criar e implantar uma política do Exploit Guard](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral

As políticas personalizadas agora fazem parte da experiência de recomendações da Central de Segurança, da classificação de segurança e do painel de padrões de conformidade regulatória. Esse recurso agora está em disponibilidade geral e permite que você estenda a cobertura de avaliação de segurança da sua organização na Central de Segurança. 

Crie uma iniciativa personalizada no Azure Policy, adicione políticas a ela, integre-a à Central de Segurança do Azure e visualize-a como recomendações.

Agora também adicionamos a opção para editar os metadados de recomendação personalizados. As opções de metadados incluem severidade, etapas de correção, informações sobre ameaças e muito mais.  

Saiba mais sobre [como aprimorar as recomendações personalizadas com informações detalhadas](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos 

Estamos integrando as funcionalidades de detecção do CDA (análise de despejo de memória) do Windows à [detecção de ataque sem arquivos](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). A análise de detecção de ataque sem arquivos acompanha versões aprimoradas dos seguintes alertas de segurança para computadores Windows: Injeção de código descoberta, Módulo do Windows simulado detectado, Shellcode descoberto e Segmento de código suspeito detectado.

Alguns dos benefícios dessa transição:

- **Detecção de malware proativa e oportuna** – a abordagem CDA envolvida ao aguardar a ocorrência de uma falha e, em seguida, executara análise para localizar o malware ativo. O uso da detecção de ataque sem arquivos introduz a identificação proativa de ameaças na memória enquanto elas estão em execução. 

- **Alertas aprimorados** – os alertas de segurança da detecção de ataque sem arquivos incluem aprimoramentos que não estão disponíveis no CDA, como as informações de conexões de rede ativas. 

- **Agregação de alerta** – quando o CDA detecta vários padrões de ataque em um despejo de memória, ele disparou vários alertas de segurança. A detecção de ataque sem arquivos combina todos os padrões de ataque identificados do mesmo processo em um alerta, eliminando a necessidade de correlacionar vários alertas.

- **Requisitos reduzidos em seu workspace do Log Analytics** – despejos de memória que contêm dados potencialmente confidenciais não serão mais carregados em seu workspace do Log Analytics.



## <a name="april-2020"></a>Abril de 2020

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Os pacotes de conformidade dinâmica agora estão em disponibilidade geral

O painel de conformidade regulatória da Central de Segurança do Azure agora inclui **pacotes de conformidade dinâmica** (agora em disponibilidade geral) para acompanhar padrões adicionais do setor e regulatórios.

Os pacotes de conformidade dinâmica podem ser adicionados à sua assinatura ou grupo de gerenciamento na página política de segurança da Central de Segurança. Quando você tiver integrado um padrão ou um parâmetro de comparação, o padrão será exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Um relatório de resumo para qualquer um dos padrões que foram integrados estará disponível para download.

Agora, você pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official e UK NHS**
- **PBMM Federal do Canadá**
- **Azure CIS 1.1.0 (novo)** (que é uma representação mais completa do Azure CIS 1.1.0)

Além disso, adicionamos recentemente o **Azure Security Benchmark**, as diretrizes específicas do Azure criadas pela Microsoft para melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Outros padrões se tornarão compatíveis com o painel à medida que forem disponibilizados.  
 
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

### <a name="workflow-automation-is-now-generally-available"></a>A automação do fluxo de trabalho já está em disponibilidade geral

O recurso de automação de fluxo de trabalho da Central de Segurança do Azure agora está em disponibilidade geral. Use-o para disparar automaticamente Aplicativos Lógicos em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que têm a opção de correção rápida disponível.

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de stakeholders relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga e pode aprimorar sua segurança, garantindo que as etapas do processo sejam feitas de maneira rápida, consistente e de acordo com seus requisitos predefinidos.

Para obter mais informações sobre as funcionalidades automáticas e manuais da Central de Segurança do Azure para executar seus fluxos de trabalho, confira [automação de fluxo de trabalho](workflow-automation.md).

Saiba mais sobre a [criação de Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


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

Saiba mais sobre a [Integração do Serviço de Kubernetes do Azure à Central de Segurança](azure-kubernetes-service-integration.md).

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

Saiba mais sobre a [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).