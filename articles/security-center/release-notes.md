---
title: Notas de versão da central de segurança do Azure
description: Uma descrição do que há de novo e mudou na central de segurança do Azure.
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
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210890"
---
# <a name="whats-new-in-azure-security-center"></a>O que há de novo na central de segurança do Azure?

A segurança do Azure está em desenvolvimento ativo e recebe melhorias em uma base contínua. Para manter-se atualizado com os desenvolvimentos mais recentes, esta página fornece informações sobre:

- Novos recursos
- Correções de bug
- Funcionalidades preteridas

Esta página é atualizada regularmente, portanto, reveja-a com frequência. Se estiver procurando itens com mais de seis meses, você os encontrará no [arquivo morto para o que há de novo na central de segurança do Azure](release-notes-archive.md).


## <a name="may-2020"></a>Maio de 2020

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à VM (máquina virtual) just-in-time (JIT)

A central de segurança inclui um recurso opcional para proteger as portas de gerenciamento de suas VMs. Isso fornece uma defesa contra a forma mais comum de ataques de força bruta.

Essa atualização traz as seguintes alterações para esse recurso:

- A recomendação que aconselha você a habilitar o JIT em uma VM foi renomeada. Anteriormente, "o controle de acesso à rede just-in-time deve ser aplicado nas máquinas virtuais" agora: "as portas de gerenciamento das máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time".

- A recomendação foi definida para ser disparada somente se houver portas de gerenciamento abertas.

[Saiba mais sobre o recurso de acesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>As recomendações personalizadas foram movidas para um controle de segurança separado

Um dos controles de segurança introduzidos com a pontuação segura aprimorada foi "implementar práticas recomendadas de segurança". Todas as recomendações personalizadas criadas para suas assinaturas foram colocadas automaticamente nesse controle. 

Para facilitar a localização de suas recomendações personalizadas, nós as movemos para um controle de segurança dedicado, "recomendações personalizadas". Este controle não tem impacto sobre sua pontuação segura.

Saiba mais sobre os controles de segurança na [Pontuação segura aprimorada (visualização) na central de segurança do Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Alternar adicionado para exibir recomendações em controles ou como uma lista simples

Os controles de segurança são grupos lógicos de recomendações de segurança relacionadas. Eles refletem suas superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações.

Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Por padrão, suas recomendações são mostradas nos controles de segurança. A partir dessa atualização, você também pode exibi-los como uma lista. Para exibi-los como uma lista simples classificada pelo status de integridade dos recursos afetados, use a nova alternância ' Agrupar por controles '. A alternância está acima da lista no Portal.

Os controles de segurança-e essa alternância são parte da nova experiência de Pontuação segura. Lembre-se de enviar seus comentários de dentro do Portal.

Saiba mais sobre os controles de segurança na [Pontuação segura aprimorada (visualização) na central de segurança do Azure](secure-score-security-controls.md).


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>Recomendações de segurança de conta movidas para o controle de segurança "práticas recomendadas de segurança"

Um dos controles de segurança introduzidos com a pontuação segura aprimorada é "práticas recomendadas de segurança". Quando uma recomendação está nesse controle, ela não afeta a pontuação segura. 

Com essa atualização, três recomendações foram movidas dos controles nos quais foram originalmente colocadas e nesse controle de práticas recomendadas. Fizemos esta etapa porque determinamos que o risco dessas três recomendações é menor do que inicialmente imaginava.

As recomendações são:

- A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura (originalmente no controle "Habilitar MFA")
- Contas externas com permissões de leitura devem ser removidas da sua assinatura (originalmente no controle "gerenciar acesso e permissões")
- Um máximo de 3 proprietários deve ser designado para sua assinatura (originalmente no controle "gerenciar acesso e permissões")

Saiba mais sobre os controles de segurança na [Pontuação segura aprimorada (visualização) na central de segurança do Azure](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-generally-available"></a>Políticas personalizadas com metadados personalizados disponíveis para o público geral

As políticas personalizadas agora fazem parte da experiência de recomendações da central de segurança, da Pontuação segura e do painel de padrões de conformidade regulatória. Esse recurso agora está disponível para o público geral e permite que você estenda a cobertura de avaliação de segurança da sua organização na central de segurança. 

Crie uma iniciativa personalizada na política do Azure, adicione políticas a ela e integre-a à central de segurança do Azure e visualize-a como recomendações.

Agora também adicionamos a opção para editar os metadados de recomendação personalizados. As opções de metadados incluem gravidade, etapas de correção, informações sobre ameaças e muito mais.  

[Saiba mais sobre como aprimorar suas recomendações personalizadas com informações detalhadas](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


## <a name="april-2020"></a>Abril de 2020

### <a name="dynamic-compliance-packages-now-generally-available"></a>Pacotes de conformidade dinâmica agora disponíveis para o público geral

O painel de conformidade regulatória da central de segurança do Azure agora inclui **pacotes de conformidade dinâmica** (agora disponíveis para o público) para acompanhar padrões adicionais do setor e regulatórios.

Os pacotes de conformidade dinâmica podem ser adicionados à sua assinatura ou grupo de gerenciamento na página política de segurança da central de segurança. Quando você tiver integrado um Standard ou um benchmark, o padrão será exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Um relatório de resumo para qualquer um dos padrões que foram integrados estará disponível para download.

Agora, você pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **CSCF do CSP SWIFT-v2020**
- **NHS oficial do Reino Unido e Reino Unido**
- **PBMM Federal do Canadá**
- **Azure cis 1.1.0 (novo)** (que é uma representação mais completa do Azure cis 1.1.0)

Além disso, adicionamos recentemente o **benchmark de segurança do Azure**, as diretrizes específicas do Azure criadas pela Microsoft para práticas recomendadas de segurança e conformidade baseadas em estruturas de conformidade comuns. Padrões adicionais terão suporte no painel à medida que forem disponibilizados.  
 
[Saiba mais sobre como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendações de identidade agora incluídas na camada gratuita da central de segurança do Azure

As recomendações de segurança para identidade e acesso na camada gratuita da central de segurança do Azure agora estão disponíveis para o público geral. Isso faz parte do esforço para tornar os recursos do CSPM (gerenciamento de postura de segurança na nuvem) gratuitos. Até agora, essas recomendações só estavam disponíveis no tipo de preço Standard.

Exemplos de recomendações de identidade e acesso incluem:

- "A autenticação multifator deve ser habilitada em contas com permissões de proprietário em sua assinatura."
- "Um máximo de três proprietários deve ser designado para sua assinatura."
- "Contas preteridas devem ser removidas da sua assinatura."

Se você tiver assinaturas no tipo de preço gratuito, suas pontuações seguras serão afetadas por essa alteração, pois elas nunca foram avaliadas para sua identidade e segurança de acesso.

[Saiba mais sobre as recomendações de identidade e acesso](recommendations-reference.md#recs-identity).
[Saiba mais sobre monitoramento de identidade e acesso](security-center-identity-access.md).


## <a name="march-2020"></a>Março de 2020

### <a name="workflow-automation-is-now-generally-available"></a>A automação do fluxo de trabalho já está disponível ao público geral

O recurso de automação de fluxo de trabalho da central de segurança do Azure agora está disponível. Use-o para disparar automaticamente aplicativos lógicos em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que têm a opção correção rápida disponível.

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de participantes relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga e pode melhorar sua segurança, garantindo que as etapas do processo sejam feitas de forma rápida, consistente e de acordo com seus requisitos predefinidos.

Para obter mais informações sobre os recursos de central de segurança automática e manual para executar seus fluxos de trabalho, consulte [automação de fluxo de trabalho](workflow-automation.md).

Para saber mais sobre a criação de aplicativos lógicos, consulte [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integração da central de segurança do Azure com o centro de administração do Windows

Agora é possível mover seus servidores Windows locais do centro de administração do Windows diretamente para a central de segurança do Azure. A central de segurança se torna seu único painel de controle para exibir informações de segurança para todos os seus recursos do centro de administração do Windows, incluindo servidores locais, máquinas virtuais e cargas de trabalho de PaaS adicionais.

Depois de mover um servidor do centro de administração do Windows para a central de segurança do Azure, você poderá:

- Exiba alertas de segurança e recomendações na extensão da central de segurança do centro de administração do Windows.
- Exiba a postura de segurança e recupere informações detalhadas adicionais dos servidores gerenciados do centro de administração do Windows na central de segurança dentro do portal do Azure (ou por meio de uma API).

Saiba mais sobre como [integrar a central de segurança do Azure com o centro de administração do Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Proteção para o serviço kubernetes do Azure

A central de segurança do Azure está expandindo seus recursos de segurança de contêiner para proteger o AKS (serviço kubernetes do Azure).

A popular plataforma de software livre kubernetes foi adotada tão amplamente que agora é um padrão do setor para orquestração de contêineres. Apesar dessa implementação generalizada, ainda há uma falta de compreensão sobre como proteger um ambiente kubernetes. A defesa das superfícies de ataque de um aplicativo em contêineres exige experiência para garantir que a infraestrutura seja configurada de forma segura e constante para possíveis ameaças.

A defesa da central de segurança inclui:

- **Descoberta e visibilidade** – descoberta contínua de instâncias do AKS gerenciadas nas assinaturas registradas na central de segurança.
- **Recomendações de segurança** -recomendações acionáveis para ajudá-lo a cumprir as práticas recomendadas de segurança para o AKs. Essas recomendações estão incluídas na sua pontuação segura para garantir que elas sejam exibidas como parte da postura de segurança de sua organização. Um exemplo de uma recomendação relacionada ao AKS que você pode ver é "o controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço kubernetes".
- **Proteção contra ameaças** -por meio de análise contínua da implantação do AKs, a central de segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível do cluster do host e do AKS.

[Saiba mais sobre a integração dos serviços Kubernetess do Azure com a central de segurança](azure-kubernetes-service-integration.md).
[Saiba mais sobre os recursos de segurança do contêiner na central de segurança](container-security.md).


### <a name="improved-just-in-time-experience"></a>Experiência just-in-time aprimorada

Os recursos, a operação e a interface do usuário das ferramentas just-in-time da central de segurança do Azure que protegem suas portas de gerenciamento foram aprimoradas da seguinte maneira: 

- **Campo de justificativa** – ao solicitar acesso a uma máquina virtual (VM) por meio da página just-in-time do portal do Azure, um novo campo opcional estará disponível para inserir uma justificativa para a solicitação. As informações inseridas neste campo podem ser rastreadas no log de atividades. 
- **Limpeza automática de regras JIT (just-in-time) redundantes** – sempre que você atualiza uma política JIT, uma ferramenta de limpeza é executada automaticamente para verificar a validade de todo o conjunto de regras. A ferramenta procura incompatibilidades entre as regras em sua política e as regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, ela determinará a causa e, quando for seguro, removerá as regras internas que não são mais necessárias. O limpador nunca exclui as regras que você criou. 

[Saiba mais sobre o recurso de acesso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Duas recomendações de segurança para aplicativos Web preteridas

Duas recomendações de segurança relacionadas a aplicativos Web estão sendo preteridas: 

- As regras para aplicativos Web em IaaS NSGs devem ser protegidas.
    (Política relacionada: as regras de NSGs para aplicativos Web em IaaS devem ser protegidas)

- O acesso aos serviços de aplicativos deve ser restrito.
    (Política relacionada: o acesso aos serviços de aplicativos deve ser restrito [visualização])

Essas recomendações não serão mais exibidas na lista de recomendações da central de segurança. As políticas relacionadas não serão mais incluídas na iniciativa denominada "central de segurança padrão".

[Saiba mais sobre as recomendações de segurança](recommendations-reference.md).

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>A detecção de ataque de não-arquivo para Linux agora está em visualização

À medida que os invasores aumentam os métodos de stealthier de aplicação para evitar a detecção, a central de segurança do Azure está estendendo a detecção de ataque com o Linux, além do Windows Ataques de não-arquivo exploram vulnerabilidades de software, injetam cargas mal-intencionadas em processos de sistema benignos e ocultam na memória. Estas técnicas:

- minimizar ou eliminar rastreamentos de malware em disco
- reduzir significativamente as chances de detecção por soluções de verificação de malware baseadas em disco

Para combater essa ameaça, a central de segurança do Azure liberou a detecção de ataques de arquivo para Windows em outubro de 2018 e agora não ampliou a detecção de ataques de arquivo no Linux também. 


## <a name="january-2020"></a>Janeiro de 2020

### <a name="enhanced-secure-score"></a>Pontuação segura aprimorada

Uma versão aprimorada do recurso de Pontuação segura da central de segurança do Azure agora está disponível em versão prévia. Nesta versão, várias recomendações são agrupadas em controles de segurança que refletem melhor suas superfícies de ataque vulneráveis (por exemplo, restringir o acesso às portas de gerenciamento).

Familiarize-se com as alterações de Pontuação segura durante a fase de visualização e determine outras correções que o ajudarão a proteger ainda mais seu ambiente.

Saiba mais em [Pontuação segura avançada (versão prévia) na central de segurança do Azure](secure-score-security-controls.md).