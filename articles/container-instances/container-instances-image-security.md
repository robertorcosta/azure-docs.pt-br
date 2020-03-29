---
title: Segurança para instâncias de contêineres
description: Recomendações para proteger imagens e segredos para instâncias de contêineres do Azure e considerações gerais de segurança para qualquer plataforma de contêineres
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: b5f2c4d9ca80318574e288110fd4ce7f490af00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76260490"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerações de segurança para instâncias de contêineres do Azure

Este artigo introduz considerações de segurança para usar o Azure Container Instances para executar aplicativos de contêineres. Os tópicos incluem:

> [!div class="checklist"]
> * **Recomendações de segurança** para gerenciar imagens e segredos para instâncias de contêineres do Azure
> * **Considerações para o ecossistema de contêineres** durante todo o ciclo de vida do contêiner, para qualquer plataforma de contêineres

## <a name="security-recommendations-for-azure-container-instances"></a>Recomendações de segurança para instâncias de contêineres do Azure

### <a name="use-a-private-registry"></a>Use um registro privado

Os contêineres são criados a partir de imagens que são armazenadas em um ou mais repositórios. Esses repositórios podem pertencer a um registro público, como [o Docker Hub,](https://hub.docker.com)ou a um registro privado. Um exemplo de um registro privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), que pode ser instalado localmente ou em uma nuvem privada virtual. Você também pode usar serviços de registro de contêineres privados baseados em nuvem, incluindo [o Registro de Contêineres do Azure](../container-registry/container-registry-intro.md). 

Uma imagem de contêiner disponível publicamente não garante a segurança. As imagens de contêiner consistem em várias camadas de software, e cada camada de software pode ter vulnerabilidades. Para ajudar a reduzir a ameaça de ataques, você deve armazenar e recuperar imagens de um registro privado, como o Registro de Contêineres do Azure ou o Registro Confiável do Docker. Além de fornecer um registro privado gerenciado, o Azure Container Registry suporta [autenticação baseada no principal serviço](../container-registry/container-registry-authentication.md) através do Azure Active Directory para fluxos básicos de autenticação. Essa autenticação inclui acesso baseado em função para leitura somente (puxar), gravação (push) e outras permissões.

### <a name="monitor-and-scan-container-images"></a>Monitorar e digitalização de imagens de contêineres

Aproveite as soluções para digitalização de imagens de contêineres em um registro privado e identifique possíveis vulnerabilidades. É importante entender a profundidade da detecção de ameaças que as diferentes soluções fornecem.

Por exemplo, o Registro de Contêineres do Azure integra-se opcionalmente [ao Azure Security Center](../security-center/azure-container-registry-integration.md) para escanear automaticamente todas as imagens Linux empurradas para um registro. O scanner qualys integrado do Azure Security Center detecta vulnerabilidades de imagem, as classifica e fornece orientação de remediação.

Soluções de monitoramento de segurança e digitalização de imagens, como [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) também estão disponíveis no Azure Marketplace.  

### <a name="protect-credentials"></a>Proteger credenciais

Os contêineres podem se espalhar por vários aglomerados e regiões do Azure. Assim, você deve proteger as credenciais necessárias para logins ou acesso a API, como senhas ou tokens. Certifique-se de que apenas usuários privilegiados podem acessar esses contêineres em trânsito e em repouso. Inventário de todos os segredos de credencial e, em seguida, exige que os desenvolvedores usem ferramentas emergentes de gerenciamento de segredos que são projetadas para plataformas de contêineres.  Certifique-se de que sua solução inclua bancos de dados criptografados, criptografia TLS para dados de segredos em trânsito e [controle de acesso baseado em função](../role-based-access-control/overview.md)de menor privilégio . [O Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) é um serviço em nuvem que protege chaves e segredos de criptografia (como certificados, strings de conexão e senhas) para aplicativos contêiner. Como esses dados são sensíveis e essenciais para os negócios, o acesso seguro aos seus cofres principais para que apenas aplicativos autorizados e usuários possam acessá-los.

## <a name="considerations-for-the-container-ecosystem"></a>Considerações para o ecossistema de contêineres

As seguintes medidas de segurança, bem implementadas e gerenciadas de forma eficaz, podem ajudá-lo a proteger e proteger seu ecossistema de contêineres. Essas medidas se aplicam ao longo do ciclo de vida do contêiner, desde o desenvolvimento até a implantação da produção, até uma gama de orquestradores de contêineres, hosts e plataformas. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Use o gerenciamento de vulnerabilidades como parte do ciclo de vida do desenvolvimento de contêineres 

Ao usar o gerenciamento eficaz de vulnerabilidades durante todo o ciclo de vida do desenvolvimento de contêineres, você melhora as chances de identificar e resolver preocupações de segurança antes que elas se tornem um problema mais sério. 

### <a name="scan-for-vulnerabilities"></a>Procurar por vulnerabilidades 

Novas vulnerabilidades são descobertas o tempo todo, então procurar e identificar vulnerabilidades é um processo contínuo. Incorporar varredura de vulnerabilidades durante todo o ciclo de vida do contêiner:

* Como uma verificação final em seu pipeline de desenvolvimento, você deve realizar uma varredura de vulnerabilidade em contêineres antes de empurrar as imagens para um registro público ou privado. 
* Continue a escanear imagens de contêineres no registro tanto para identificar quaisquer falhas que foram de alguma forma perdidas durante o desenvolvimento quanto para abordar quaisquer vulnerabilidades recém-descobertas que possam existir no código usado nas imagens do contêiner.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapear vulnerabilidades de imagem para contêineres em execução 

Você precisa ter um meio de mapear vulnerabilidades identificadas em imagens de contêineres para contêineres em execução, para que os problemas de segurança possam ser mitigados ou resolvidos.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Certifique-se de que apenas imagens aprovadas sejam usadas em seu ambiente 

Há mudanças e volatilidade suficientes em um ecossistema de contêineres sem permitir contêineres desconhecidos também. Permitir apenas imagens de contêiner aprovadas. Tenha ferramentas e processos em vigor para monitorar e impedir o uso de imagens de contêineres não aprovadas. 

Uma maneira eficaz de reduzir a superfície de ataque e impedir que os desenvolvedores cometam erros críticos de segurança é controlar o fluxo de imagens de contêineres em seu ambiente de desenvolvimento. Por exemplo, você pode sancionar uma única distribuição Linux como uma imagem base, de preferência uma que seja magra (Alpine ou CoreOS em vez de Ubuntu), para minimizar a superfície para potenciais ataques. 

A assinatura de imagem ou impressão digital pode fornecer uma cadeia de custódia que permite verificar a integridade dos contêineres. Por exemplo, o Azure Container Registry suporta o modelo de confiança de [conteúdo](https://docs.docker.com/engine/security/trust/content_trust) do Docker, que permite que os editores de imagens assinem imagens que são empurradas para um registro, e os consumidores de imagens retiram apenas imagens assinadas.

### <a name="permit-only-approved-registries"></a>Permitir apenas registros aprovados 

Uma extensão de garantir que seu ambiente use apenas imagens aprovadas é permitir apenas o uso de registros de contêineres aprovados. Exigir o uso de registros de contêineres aprovados reduz sua exposição ao risco limitando o potencial para a introdução de vulnerabilidades ou problemas de segurança desconhecidos. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir a integridade das imagens ao longo do ciclo de vida 

Parte do gerenciamento da segurança durante todo o ciclo de vida do contêiner é garantir a integridade das imagens do contêiner no registro e à medida que elas são alteradas ou implantadas na produção. 

* Imagens com vulnerabilidades, mesmo menores, não devem ser permitidas a serem executadas em um ambiente de produção. Idealmente, todas as imagens implantadas na produção devem ser salvas em um registro privado acessível a poucos selecionados. Mantenha o número de imagens de produção pequeno para garantir que elas possam ser gerenciadas de forma eficaz.

* Como é difícil identificar a origem do software a partir de uma imagem de contêiner disponível publicamente, construa imagens a partir da fonte para garantir o conhecimento da origem da camada. Quando surge uma vulnerabilidade em uma imagem de contêiner criada automaticamente, os clientes podem encontrar um caminho mais rápido para uma resolução. Com uma imagem pública, os clientes precisariam encontrar a raiz de uma imagem pública para corrigi-la ou obter outra imagem segura do editor. 

* Uma imagem completamente digitalizada implantada na produção não é garantida como atualizada durante a vida útil do aplicativo. As vulnerabilidades de segurança podem ser reportadas para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implantação em produção. 

  Audite periodicamente imagens implantadas na produção para identificar imagens desatualizadas ou que não foram atualizadas há algum tempo. Você pode usar metodologias de implantação verde-azul e mecanismos de atualização de rolamento para atualizar imagens de contêiner sem tempo de inatividade. Você pode digitalização de imagens usando ferramentas descritas na seção anterior. 

* Use um pipeline de integração contínua (CI) com varredura de segurança integrada para construir imagens seguras e empurrá-las para o seu registro privado. A verificação de vulnerabilidade incorporada à solução de CI garante que as imagens que passam em todos os testes sejam enviadas para o registro privado a partir do qual as cargas de trabalho de produção são implantadas. 

  Uma falha no pipeline ci garante que imagens vulneráveis não sejam empurradas para o registro privado usado para implantações de carga de trabalho de produção. Ele também automatiza a varredura de segurança de imagem se houver um número significativo de imagens. Caso contrário, a auditoria manual de imagens em busca de vulnerabilidades de segurança pode ser muito demorada e sujeita a erros. 

### <a name="enforce-least-privileges-in-runtime"></a>Impor menos privilégios em tempo de execução 

O conceito de menos privilégios é uma prática básica de segurança que também se aplica aos contêineres. Quando uma vulnerabilidade é explorada, geralmente dá ao invasor acesso e privilégios iguais aos do aplicativo ou processo comprometidos. Garantir que os contêineres operem com os menores privilégios e acesso necessários para fazer o trabalho reduz sua exposição ao risco. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Reduza a superfície de ataque do contêiner removendo privilégios desnecessários 

Você também pode minimizar a superfície de ataque potencial removendo quaisquer processos ou privilégios não utilizados ou desnecessários do tempo de execução do contêiner. Recipientes privilegiados funcionam como raiz. Se um usuário mal-intencionado ou a carga de trabalho escapar em um contêiner privilegiado, o contêiner será executado como raiz nesse sistema.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Pré-aprovar arquivos e executáveis que o contêiner pode acessar ou executar 

Reduzir o número de variáveis ou incógnitas ajuda a manter um ambiente estável e confiável. Limitar contêineres para que eles possam acessar ou executar apenas arquivos e executáveis pré-aprovados ou listados em segurança é um método comprovado de limitar a exposição ao risco.  

É muito mais fácil gerenciar uma lista de segurança quando ela é implementada desde o início. Uma lista de segurança fornece uma medida de controle e capacidade de gerenciamento à medida que você aprende quais arquivos e executáveis são necessários para que o aplicativo funcione corretamente. 

Uma lista de segurança não só reduz a superfície de ataque, mas também pode fornecer uma linha de base para anomalias e evitar os casos de uso dos cenários de "vizinho barulhento" e de fuga de contêineres. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Impor segmentação de rede em contêineres em execução  

Para ajudar a proteger os contêineres em uma sub-rede contra riscos de segurança em outra sub-rede, mantenha a segmentação da rede (ou nanosegmentação) ou a segregação entre contêineres em execução. A manutenção da segmentação da rede também pode ser necessária para o uso de contêineres em indústrias que são necessárias para cumprir os mandatos de conformidade.  

Por exemplo, a ferramenta parceira [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornece uma abordagem automatizada para nanosegmentação. O Aqua monitora as atividades da rede de contêineres em tempo de execução. Ele identifica todas as conexões de rede de entrada e saída para/de outros contêineres, serviços, endereços IP e internet pública. A nanosegmentação é criada automaticamente com base no tráfego monitorado. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorar a atividade do contêiner e o acesso do usuário 

Como em qualquer ambiente de TI, você deve monitorar consistentemente a atividade e o acesso do usuário ao seu ecossistema de contêineres para identificar rapidamente qualquer atividade suspeita ou maliciosa. O Azure fornece soluções de monitoramento de contêineres, incluindo:

* [O Azure Monitor para contêineres](../azure-monitor/insights/container-insights-overview.md) monitora o desempenho de suas cargas de trabalho implantadas nos ambientes Kubernetes hospedados no Azure Kubernetes Service (AKS). O Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. 

* A [solução Azure Container Monitoring](../azure-monitor/insights/containers.md) ajuda você a visualizar e gerenciar outros hosts de contêineres Docker e Windows em um único local. Por exemplo: 

  * Exibir informações detalhadas de auditoria que mostram os comandos usados com contêineres. 
  * Solucionar problemas de contêineres visualizando e pesquisando logs centralizados sem ter que visualizar remotamente hosts Docker ou Windows.  
  * Encontre recipientes que podem ser barulhentos e consumam recursos excessivos em um host.
  * Exibir informações centralizadas de uso e desempenho da CPU, memória, armazenamento e de rede para contêineres.  

  A solução suporta orquestradores de contêineres, incluindo Docker Swarm, DC/OS, Kubernetes não gerenciados, Fabric de serviço e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorar a atividade dos recursos dos contêineres 

Monitore sua atividade de recursos, como arquivos, rede e outros recursos que seus contêineres acessam. O monitoramento da atividade e do consumo de recursos é útil tanto para o monitoramento de desempenho quanto como medida de segurança. 

[O Azure Monitor](../azure-monitor/overview.md) permite o monitoramento central dos serviços do Azure, permitindo a coleta de métricas, registros de atividades e registros de diagnóstico. Por exemplo, o log de atividades informará quando novos recursos forem criados ou modificados. 

  Métricas disponíveis que fornecem estatísticas de desempenho para diferentes recursos e até mesmo o sistema operacional dentro de uma máquina virtual. É possível exibir esses dados com um dos gerenciadores no Portal do Azure e criar alertas com base nessas métricas. O Azure Monitor fornece o pipeline de métricas mais rápido possível (de 5 a 1 minuto), portanto, você deve usá-lo para notificações e alertas críticos em termos de tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registre todo o acesso administrativo do usuário de contêiner para auditoria 

Mantenha uma trilha de auditoria precisa de acesso administrativo ao seu ecossistema de contêineres, incluindo seu cluster Kubernetes, registro de contêineres e imagens de contêineres. Esses registros podem ser necessários para fins de auditoria e serão úteis como evidência forense após qualquer incidente de segurança. As soluções do Azure incluem:

* [Integração do Azure Kubernetes Service com o Azure Security Center](../security-center/azure-kubernetes-service-integration.md) para monitorar a configuração de segurança do ambiente de cluster e gerar recomendações de segurança
* [Solução de monitoramento de contêineres do Azure](../azure-monitor/insights/containers.md)
* Registros de recursos para [instâncias de contêineres do Azure](container-instances-log-analytics.md) e [registro de contêineres do Azure](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como usar [o Azure Security Center](../security-center/container-security.md) para detecção de ameaças em tempo real em seus ambientes contêineres.

* Saiba mais sobre o gerenciamento de vulnerabilidades de contêineres com soluções da [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [da Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).