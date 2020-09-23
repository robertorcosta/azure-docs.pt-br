---
title: Azure defender para servidores-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para servidores.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 29f15aa7d1cbcd260ce44f6083138681ceb9fd79
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933719"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Introdução ao Azure defender para servidores

O Azure defender for Servers adiciona detecção de ameaças e defesas avançadas para seus computadores Windows e Linux.

Para o Windows, o Azure defender se integra aos serviços do Azure para monitorar e proteger suas máquinas baseadas no Windows. A Central de Segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

Para o Linux, o Azure defender coleta registros de auditoria de computadores Linux usando **auditoria**, uma das estruturas de auditoria do Linux mais comuns. auditd reside no kernel principal. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Quais são os benefícios do Azure defender para servidores?

Os recursos de detecção e proteção de ameaças fornecidos com o Azure defender para servidores incluem:

- **Verificação de avaliação de vulnerabilidade para VMs** -o verificador de vulnerabilidade incluído na central de segurança do Azure é fornecido pela Qualys. 

    O verificador de Qualys é uma das principais ferramentas para a identificação em tempo real de vulnerabilidades em suas máquinas virtuais do Azure. Você não precisa de uma licença do Qualys nem de uma conta do Qualys: tudo é tratado diretamente na Central de Segurança. [Saiba mais](deploy-vulnerability-assessment-vm.md).

- **Acesso à VM just-in-time (JIT)**  – atores de ameaça que buscam computadores acessíveis ativamente com portas de gerenciamento abertas, como RDP ou SSH. Todas as suas máquinas virtuais são alvos potenciais de um ataque. Quando uma VM é comprometida com êxito, ela é usada como o ponto de entrada para atacar outros recursos em seu ambiente.

    Ao habilitar o Azure defender para servidores, você pode usar o acesso à VM just-in-time para bloquear o tráfego de entrada para suas VMs, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. [Saiba mais](just-in-time-explained.md).

- O fim ( **monitoramento** de integridade de arquivo) – o fim (monitoramento de integridade de arquivo), também conhecido como monitoramento de alterações, examina arquivos e registros de sistema operacional, software de aplicativo e outros para alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do arquivo é diferente da última verificação do arquivo. Você pode aproveitar essa comparação para determinar se foram feitas modificações válidas ou suspeitas em seus arquivos.

    Ao habilitar o Azure defender para servidores, você pode usar o FIM para validar a integridade dos arquivos do Windows, seus registros do Windows e arquivos do Linux. [Saiba mais](security-center-file-integrity-monitoring.md).

- Os controles de aplicativo adaptáveis **(AAC)** são uma solução inteligente e automatizada para definir as listas de permissões de aplicativos conhecidos confiáveis para seus computadores.

    Quando você tiver habilitado e configurado controles de aplicativo adaptáveis, obterá alertas de segurança se qualquer aplicativo for executado além daqueles que você definiu como seguro. [Saiba mais](security-center-adaptive-network-hardening.md).

- **Anh (proteção de rede adaptável)** – aplicar NSG (grupos de segurança de rede) para filtrar o tráfego de e para recursos, melhora sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui pelo NSG é um subconjunto das regras de NSG definidas. Nesses casos, é possível melhorar ainda mais a postura de segurança ao proteger as regras de NSG, com base nos padrões reais de tráfego.

    A proteção de rede adaptável fornece recomendações para proteger ainda mais as regras de NSG. Ele usa um algoritmo de aprendizado de máquina que fatores em tráfego real, configuração confiável conhecida, inteligência contra ameaças e outros indicadores de comprometimento e, em seguida, fornece recomendações para permitir o tráfego somente de tuplas de IP/porta específicas. [Saiba mais](security-center-adaptive-network-hardening.md).

- **Integração com a ATP (proteção avançada contra ameaças) do Microsoft defender (somente Windows)** – o Azure defender integra-se com o Microsoft defender com a ATP (proteção avançada contra ameaças). Juntas, elas fornecem recursos abrangentes de Detecção e resposta de ponto de extremidade (EDR). [Saiba mais](security-center-wdatp.md).

    > [!IMPORTANT]
    > O sensor da ATP do Microsoft Defender é habilitado automaticamente em servidores Windows que usam a Central de Segurança.

    Quando a ATP do Microsoft Defender detecta uma ameaça, ela dispara um alerta. O alerta é mostrado na central de segurança. Na central de segurança, você também pode dinamizar para o console do Microsoft defender ATP e executar uma investigação detalhada para descobrir o escopo do ataque. Para saber mais sobre a ATP do Microsoft Defender, confira [Integrar servidores ao serviço da ATP do Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

- **Proteção de host do Docker** – a central de segurança do Azure identifica contêineres não gerenciados hospedados em VMs do Linux IaaS ou em outros computadores Linux que executam contêineres do Docker. A central de segurança avalia continuamente as configurações desses contêineres. Em seguida, ele os compara com o benchmark do Docker do CIS (Center for Internet Security). A central de segurança inclui o conjunto de regras inteiro do parâmetro de comparação do Docker do CIS e o alerta se os contêineres não atenderem a nenhum dos controles. [Saiba mais](harden-docker-hosts.md).

- Detecção de ataque de não- **arquivo (somente Windows)** -ataques de arquivo insuficiente injetam cargas mal-intencionadas na memória para evitar a detecção por técnicas de verificação baseadas em disco. A carga do invasor persiste dentro da memória de processos comprometidos e executa uma ampla variedade de atividades mal-intencionadas.

  Com a detecção de ataques sem arquivos, as técnicas forenses de memória automatizada identificam os toolkits, as técnicas e os comportamentos de ataques sem arquivos. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória dos processos. Informações específicas para Linux incluem a identificação de: 

  - Kits de programas conhecidos e software de mineração de criptografia 

  - Shellcode, que é uma pequena parte do código normalmente usada como a carga na exploração de uma vulnerabilidade de software.

  - Executável mal-intencionado injetado na memória do processo

  A detecção de ataque sem arquivo gera alertas de segurança detalhados contendo as descrições com metadados de processo adicionais, como a atividade de rede. Isso acelera a triagem de alerta, correlação e tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos e fornece maior cobertura de detecção.

  Para obter detalhes sobre os alertas de detecção de ataques sem arquivos, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-windows).

- **Alertas auditados do Linux e integração do agente de log Analytics (somente Linux)** – o sistema auditado consiste em um subsistema de nível de kernel, que é responsável por monitorar chamadas do sistema. Ele as filtra de acordo com um conjunto de regras especificado e grava mensagens para eles em um soquete. A Central de Segurança integra as funcionalidades do pacote auditd no agente do Log Analytics. Essa integração habilita a coleta de eventos auditd em todas as distribuições Linux com suporte, sem nenhum pré-requisito.

    Os registros auditd são coletados, aprimorados e agregados em eventos usando o agente do Log Analytics para o agente do Linux. A Central de Segurança adiciona continuamente novos recursos de análise que usam sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, esses recursos de análise abrangem processos suspeitos, tentativas de entrada duvidosa, carregamento de módulos kernel e outras atividades. Essas atividades podem indicar que um computador está sob ataque ou foi violado.  

    Para obter uma lista dos alertas do Linux, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulando alertas

Você pode simular alertas baixando um dos seguintes guias estratégicos:

- Para Windows: [guia estratégico da central de segurança do Azure: alertas de segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

- Para Linux: [guia estratégico da central de segurança do Azure: detecções do Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).




## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure defender para servidores. 

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Habilitar o Azure Defender](security-center-pricing.md)