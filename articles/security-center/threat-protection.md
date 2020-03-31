---
title: Proteção contra ameaças no Centro de Segurança Do Azure
description: Este tópico descreve os recursos protegidos pelos recursos de proteção contra ameaças do Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e348180eb94c1703ceecf2f2b00ab942ba5ff0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536319"
---
# <a name="threat-protection-in-azure-security-center"></a>Proteção contra ameaças no Centro de Segurança Do Azure

Quando o Security Center detecta uma ameaça em qualquer área do seu ambiente, ele gera um alerta. Esses alertas descrevem detalhes dos recursos afetados, sugerem etapas de remediação e, em alguns casos, uma opção para acionar um aplicativo lógico em resposta.

A proteção contra ameaças do Azure Security Center oferece defesas abrangentes para o seu ambiente:

* **Proteção contra ameaças para recursos de computação do Azure**: máquinas Windows, máquinas Linux, Azure App Service e contêineres Azure

* **Proteção contra ameaças para recursos de dados do Azure**: Banco de dados SQL e SQL Data Warehouse, Armazenamento Azure e Azure Cosmos DB

* **Proteção contra ameaças para camadas de serviço do Azure**: camada de rede Do Azure, camada de gerenciamento do Azure (Azure Resource Manager) (Preview) e Azure Key Vault (Preview)

Se um alerta é gerado pelo Security Center ou recebido pelo Security Center de um produto de segurança diferente, você pode exportá-lo. Para exportar seus alertas para o Azure Sentinel (ou um SIEM de terceiros) ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md). 




## <a name="threat-protection-for-windows-machines"></a>Proteção contra ameaças para máquinas Windows<a name="windows-machines"></a>

O Azure Security Center integra-se aos serviços do Azure para monitorar e proteger suas máquinas baseadas no Windows. O Security Center apresenta os alertas e sugestões de correção de todos esses serviços em um formato fácil de usar.

* **O Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center amplia suas plataformas de proteção contra carga de trabalho na nuvem, integrando-se ao Microsoft Defender Advanced Threat Protection (ATP). Juntos, eles fornecem recursos abrangentes de detecção e resposta de ponto final (EDR).

    > [!IMPORTANT]
    > O sensor ATP do Microsoft Defender é ativado automaticamente em servidores Windows que usam o Security Center.

    Quando o Microsoft Defender ATP detecta uma ameaça, ele dispara um alerta. O alerta é mostrado no painel do Security Center. A partir do painel, você pode girar para o console ATP do Microsoft Defender e realizar uma investigação detalhada para descobrir o escopo do ataque. Para obter mais informações sobre o Microsoft Defender ATP, consulte [servidores onboard para o serviço Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Análise de falha** <a name="windows-dump"></a> - Quando o software falha, um dump captura uma parte da memória no momento do acidente.

    Um acidente pode ter sido causado por malware ou conter malware. Para evitar ser detectado por produtos de segurança, várias formas de malware usam um ataque sem arquivo, que evita escrever em disco ou criptografar componentes de software gravados em disco. Esse tipo de ataque é difícil de detectar usando abordagens tradicionais baseadas em disco.

    No entanto, usando a análise de memória, você pode detectar esse tipo de ataque. Analisando a memória no dump, o Security Center pode detectar as técnicas que o ataque está usando. Por exemplo, o ataque pode estar tentando explorar vulnerabilidades no software, acessar dados confidenciais e persistir sub-repticiamente dentro de uma máquina comprometida. O Security Center faz esse trabalho com o mínimo de impacto de desempenho para os hosts.

    Para obter detalhes sobre os alertas de análise de despejo de acidentes, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-windows).

* **Detecção de ataque sem arquivo** <a name="windows-fileless"></a> - Ataques sem arquivo direcionados aos seus pontos finais são comuns. Para evitar a detecção, ataques sem arquivo injetam cargas maliciosas na memória. As cargas de invasores persistem na memória de processos comprometidos e realizam uma ampla gama de atividades maliciosas.

    Com a detecção de ataques sem arquivos, técnicas forenses automatizadas de memória identificam kits de ferramentas, técnicas e comportamentos de ataque sem arquivo. Esta solução verifica periodicamente sua máquina em tempo de execução e extrai insights diretamente da memória de processos críticos de segurança.

    Encontra evidências de exploração, injeção de código e execução de cargas maliciosas. A detecção de ataques sem arquivos gera alertas de segurança detalhados para acelerar a triagem de alertas, correlação e tempo de resposta a jusante. Essa abordagem complementa as soluções EDR baseadas em eventos, proporcionando maior cobertura de detecção.

    Para obter detalhes dos alertas de detecção de ataque sem arquivo, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-windows).

> [!TIP]
> Você pode simular alertas do Windows baixando [o Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Proteção contra ameaças para máquinas Linux<a name="linux-machines"></a>

O Security Center coleta registros de auditoria de máquinas Linux usando **auditados**, um dos frameworks de auditoria Linux mais comuns. vidas auditadas no kernel mainline. 

* **Alertas auditados** <a name="linux-auditd"></a> linux e integração do Microsoft Monitoring Agent (MMA) - O sistema auditado consiste em um subsistema de nível de kernel, responsável pelo monitoramento das chamadas do sistema. Ele os filtra por um conjunto de regras especificado e grava mensagens para eles em um soquete. O Security Center integra funcionalidades do pacote auditado dentro do Microsoft Monitoring Agent (MMA). Essa integração permite a coleta de eventos auditados em todas as distribuições Linux suportadas, sem qualquer pré-requisito.  

    os registros auditados são coletados, enriquecidos e agregados em eventos usando o agente Linux MMA. O Security Center adiciona continuamente novas análises que usam sinais Linux para detectar comportamentos maliciosos em máquinas Linux em nuvem e no local. Semelhante aos recursos do Windows, essas análises abrangem processos suspeitos, sinais duvidosos em tentativas, carregamento de módulos de kernel e outras atividades. Essas atividades podem indicar que uma máquina está ataque ou foi violada.  

    Para obter uma lista dos alertas Linux, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-linux).

> [!TIP]
> Você pode simular alertas linux baixando [o Azure Security Center Playbook: Linux Detections](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Proteção contra ameaças para o serviço de aplicativos do Azure<a name="app-services"></a>

> [!NOTE]
> Este serviço não está disponível atualmente no governo azure e regiões de nuvem soberana.

O Security Center usa a escala da nuvem para identificar ataques direcionados a aplicativos que executam o App Service. Os atacantes testam aplicativos web para encontrar e explorar fraquezas. Antes de serem encaminhados para ambientes específicos, as solicitações de aplicativos em execução no Azure passam por vários gateways, onde são inspecionados e registrados. Esses dados são então usados para identificar explorações e atacantes e aprender novos padrões que serão usados mais tarde.

Usando a visibilidade que o Azure tem como provedor de nuvem, o Security Center analisa os registros internos do App Service para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui varredura generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs, e mostra padrões de rastreamento para pontos finais semelhantes em vários hosts. Os ataques estão procurando por uma página ou plugin vulnerável, e não podem ser identificados do ponto de vista de um único host.

Se você estiver executando um plano de serviço de aplicativo baseado no Windows, o Security Center também tem acesso às caixas de areia e VMs subjacentes. Juntamente com os dados de registro mencionados acima, a infra-estrutura pode contar a história, desde um novo ataque circulando na natureza até compromissos em máquinas de clientes. Portanto, mesmo que o Security Center seja implantado após a exploração de um aplicativo web, ele pode ser capaz de detectar ataques contínuos.

Para obter uma lista dos alertas do Azure App Service, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureappserv).

Para obter mais informações sobre os planos do App Service, consulte [os planos do App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Proteção contra ameaças para contêineres Azure<a name="azure-containers"></a>

> [!NOTE]
> Este serviço não está disponível atualmente no governo azure e regiões de nuvem soberana.

O Security Center oferece proteção contra ameaças em tempo real para seus ambientes contêineres e gera alertas para atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

O Security Center oferece proteção contra ameaças em diferentes níveis: 

* **Nível de host** - O agente do Security Center (disponível no nível Standard, veja [preços](security-center-pricing.md) para detalhes) monitora o Linux para atividades suspeitas. O agente dispara alertas para atividades suspeitas originárias do nó ou de um contêiner que está sendo executado nele. Exemplos dessas atividades incluem detecção de web shell e conexão com endereços IP suspeitos conhecidos.

    Para obter uma visão mais profunda da segurança do seu ambiente contêiner, o agente monitora análises específicas de contêineres. Ele acionará alertas para eventos como criação de contêineres privilegiados, acesso suspeito a servidores API e servidores Secure Shell (SSH) executados dentro de um contêiner Docker.

    >[!IMPORTANT]
    > Se você optar por não instalar os agentes em seus hosts, você receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Você ainda receberá alertas relacionados à análise de rede e comunicações com servidores maliciosos.

    Para obter uma lista dos alertas de nível de host, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-containerhost).


* No **nível de cluster AKS,** a proteção contra ameaças é baseada na análise dos registros de auditoria do Kubernetes. Para habilitar esse monitoramento **sem agente,** adicione a opção Kubernetes à sua assinatura na página **de configurações de &** de preços (veja [preços).](security-center-pricing.md) Para gerar alertas neste nível, o Security Center monitora seus serviços gerenciados pela AKS usando os logs recuperados pela AKS. Exemplos de eventos neste nível incluem painéis Kubernetes expostos, criação de funções privilegiadas e criação de montagens sensíveis.

    >[!NOTE]
    > O Security Center gera alertas de segurança para as ações e implantações do Azure Kubernetes Service que ocorrem após a opção Kubernetes ser ativada nas configurações de assinatura. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-akscluster).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o cenário de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [deste post no blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Proteção contra ameaças para banco de dados SQL e data warehouse SQL<a name="data-sql"></a>

O Banco de Dados Avançado de Proteção contra Ameaças para O Azure SQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Você verá alertas quando houver atividades suspeitas de banco de dados, vulnerabilidades potenciais ou ataques de injeção sql e padrões anômalos de acesso e consulta de banco de dados.

O Advanced Threat Protection for Azure SQL Database and SQL faz parte do pacote unificado [DE Segurança de Dados Avançados (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para recursos avançados de segurança SQL, abrangendo bancos de dados SQL do Azure, instâncias gerenciadas pelo Banco de Dados Azure SQL, bancos de dados Azure SQL Data Warehouse e servidores SQL em Máquinas Virtuais Azure.

Para obter mais informações, consulte:

* [Como ativar a proteção avançada contra ameaças para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como ativar proteção avançada contra ameaças para servidores SQL em máquinas virtuais do Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção contra ameaças para Banco de Dados SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Proteção contra ameaças para armazenamento Azure<a name="azure-storage"></a>

> [!NOTE]
> Este serviço está disponível nas nuvens do governo dos EUA, mas nenhuma outra região de nuvens do governo soberano ou azure.

O Advanced Threat Protection for Storage (atualmente disponível apenas para armazenamento Blob) detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você aborde ameaças sem exigir que você seja um especialista em segurança e ajuda você a gerenciar seus sistemas de monitoramento de segurança.

Para obter mais informações, consulte:

* [Como ativar a proteção avançada contra ameaças para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o Armazenamento Azure](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Você pode simular alertas de armazenamento do Azure seguindo as instruções [neste post do blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Proteção contra ameaças para Azure Cosmos DB<a name="cosmos-db"></a>

Os alertas do Azure Cosmos DB são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos DB.

Para obter mais informações, consulte:

* [Proteção avançada contra ameaças para Azure Cosmos DB (Preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para OZure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para camada de rede Azure<a name="network-layer"></a>

As análises da camada de rede do Security Center são baseadas em [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)de exemplo, que são cabeçalhos de pacote coletados pelos roteadores principais do Azure. Com base neste feed de dados, o Security Center usa modelos de aprendizado de máquina para identificar e sinalizar atividades de tráfego maliciosas. O Security Center também usa o banco de dados microsoft Threat Intelligence para enriquecer endereços IP.

Algumas configurações de rede podem restringir o Security Center de gerar alertas sobre atividades suspeitas de rede. Para que o Security Center gere alertas de rede, certifique-se de que:

- Sua máquina virtual tem um endereço IP público (ou está em um balanceador de carga com um endereço IP público).

- O tráfego de saída de rede da sua máquina virtual não está bloqueado por uma solução IDS externa.

- Sua máquina virtual foi atribuída ao mesmo endereço IP durante toda a hora durante a qual ocorreu a comunicação suspeita. Isso também se aplica a VMs criados como parte de um serviço gerenciado (por exemplo, AKS, Databricks).

Para obter uma lista dos alertas de camada de rede Do Azure, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azurenetlayer).

Para obter detalhes sobre como o Security Center pode usar sinais relacionados à rede para aplicar a proteção contra ameaças, consulte [detecções heurísticas de DNS no Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Proteção contra ameaças para a camada de gerenciamento do Azure (Azure Resource Manager) (Visualização)<a name ="management-layer"></a>

A camada de proteção do Security Center baseada no Azure Resource Manager está atualmente em pré-visualização.

O Security Center oferece uma camada adicional de proteção usando eventos do Azure Resource Manager, que é considerado o plano de controle do Azure. Ao analisar os registros do Azure Resource Manager, o Security Center detecta operações incomuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

Para obter uma lista dos alertas do Azure Resource Manager (Preview), consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas pelo Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença de Segurança de Aplicativos em Nuvem. Se você tiver uma licença de Segurança de Aplicativos em Nuvem, esses alertas serão ativados por padrão. Para desativar os alertas:
>
> 1. Na lâmina do **Centro de Segurança,** selecione **a política de segurança**. Para a assinatura que você deseja alterar, selecione **Editar configurações**.
> 2. Selecione **detecção de ameaças**.
> 3. Em **Habilitar integrações,** **deixe claro permitir que a Microsoft Cloud App Security acesse meus dados**e selecione **Salvar**.

>[!NOTE]
>O Security Center armazena dados de clientes relacionados à segurança no mesmo geo que seu recurso. Se a Microsoft ainda não implantou o Security Center no geo do recurso, então ele armazena os dados nos Estados Unidos. Quando a Cloud App Security é ativada, essas informações são armazenadas de acordo com as regras de geo localização da Cloud App Security. Para obter mais informações, consulte [Armazenamento de dados para serviços não-regionais](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para o Azure Key Vault (Visualização)<a name="azure-keyvault"></a>

> [!NOTE]
> Este serviço não está disponível atualmente no governo azure e regiões de nuvem soberana.

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

O Azure Security Center inclui a proteção avançada de ameaças do Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. O Security Center detecta tentativas incomuns e potencialmente prejudiciais para acessar ou explorar contas do Key Vault. Essa camada de proteção permite que você aborde ameaças sem ser um especialista em segurança e sem a necessidade de gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando atividades anômalas ocorrem, o Security Center mostra alertas e, opcionalmente, os envia por e-mail para administradores de assinatura. Esses alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e remediar ameaças. 

Para obter uma lista dos alertas do Azure Key Vault, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra ameaças para o Azure WAF<a name="azure-waf"></a>

O Gateway de Aplicativo do Azure oferece um WAF (firewall do aplicativo Web) que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O GATEWAY DE Aplicativo WAF é baseado no Conjunto de Regras principais 3.0 ou 2.2.9 do Projeto de Segurança de Aplicativo Web Aberto. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades. 

Se você tiver uma licença para o Azure WAF, seus alertas WAF serão transmitidos para o Security Center sem necessidade de configuração adicional. Para obter mais informações sobre os alertas gerados pelo WAF, consulte [grupos e regras de regras do CRS de firewall do aplicativo Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra ameaças para proteção DDoS do Azure<a name="azure-ddos"></a>

Os ataques de negação distribuída de serviço (DDoS) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você está movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Os ataques DDoS podem atingir qualquer ponto final que possa ser alcançado através da internet.

Para se defender contra ataques DDoS, compre uma licença para o Azure DDoS Protection e certifique-se de que você está seguindo as práticas recomendadas de design de aplicativos. O DDoS Protection fornece diferentes níveis de serviço. Para obter mais informações, consulte [a visão geral do Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Para obter uma lista dos alertas de proteção Azure DDoS, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os alertas de segurança desses recursos de proteção contra ameaças, consulte os seguintes artigos:

* [Tabela de referência para todos os alertas do Azure Security Center](alerts-reference.md)
* [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
* [Gerencie e responda a alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Alertas e recomendações de segurança de exportação (Preview)](continuous-export.md)