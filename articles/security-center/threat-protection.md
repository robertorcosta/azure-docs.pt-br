---
title: Proteção contra ameaças na Central de Segurança do Azure
description: Este tópico descreve os recursos protegidos pelos recursos de proteção contra ameaças da central de segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 79638f584f1c65b33f23a68f01dbe82878460cc2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234070"
---
# <a name="threat-protection-in-azure-security-center"></a>Proteção contra ameaças na Central de Segurança do Azure

Quando a central de segurança detecta uma ameaça em qualquer área do ambiente, ela gera um alerta. Esses alertas descrevem os detalhes dos recursos afetados, as etapas de correção sugeridas e, em alguns casos, uma opção para disparar um aplicativo lógico em resposta.

A proteção contra ameaças da central de segurança do Azure fornece defesas abrangentes para seu ambiente:

* **Proteção contra ameaças para recursos de computação do Azure**: computadores Windows, computadores Linux, serviço Azure app e contêineres do Azure

* **Proteção contra ameaças para recursos de dados do Azure**: banco de dados SQL e SQL data warehouse, armazenamento do azure e Azure Cosmos DB

* **Proteção contra ameaças para camadas de serviço do Azure**: camada de rede do Azure, camada de gerenciamento do azure (Azure Resource Manager) (versão prévia) e Azure Key Vault (visualização)

Se um alerta for gerado pela central de segurança ou recebido pela central de segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel (ou um SIEM de terceiros) ou qualquer outra ferramenta externa, siga as instruções em [exportando alertas para um Siem](continuous-export.md). 

> [!TIP]
> Para habilitar os recursos de proteção contra ameaças da central de segurança, você deve aplicar o tipo de preço padrão à assinatura que contém as cargas de trabalho aplicáveis.
>
> A proteção contra ameaças da central de segurança para contas de armazenamento e servidores SQL/MySQL/PG de PaaS atualmente são as únicas cargas de trabalho que podem ser habilitadas no nível de recurso para proteger cargas de trabalho de armazenamento/SQL individuais.



## <a name="threat-protection-for-windows-machines"></a>Proteção contra ameaças para computadores Windows<a name="windows-machines"></a>

A central de segurança do Azure integra-se aos serviços do Azure para monitorar e proteger suas máquinas baseadas no Windows. A central de segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

* **O Microsoft defender ATP** <a name="windows-atp"></a> -central de segurança estende suas plataformas de proteção de carga de trabalho de nuvem integrando-se com o Microsoft defender ATP (proteção avançada contra ameaças). Juntos, eles fornecem recursos abrangentes de EDR (detecção de ponto de extremidade e resposta).

    > [!IMPORTANT]
    > O sensor do Microsoft defender ATP é habilitado automaticamente em servidores Windows que usam a central de segurança.

    Quando o Microsoft defender ATP detecta uma ameaça, ele dispara um alerta. O alerta é mostrado no painel da central de segurança. No painel, você pode dinamizar para o console do Microsoft defender ATP e executar uma investigação detalhada para descobrir o escopo do ataque. Para obter mais informações sobre o Microsoft defender ATP, consulte [servidores integrados ao serviço do Microsoft defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Análise** <a name="windows-dump"></a> de despejo de memória – quando o software falha, um despejo de falha captura uma parte da mémoria no momento da falha.

    Uma falha pode ter sido causada por malware ou conter malware. Para evitar ser detectado por produtos de segurança, várias formas de malware usam um ataque de arquivo, o que evita a gravação em disco ou a criptografia de componentes de software gravados no disco. Esse tipo de ataque é difícil de detectar usando abordagens tradicionais baseadas em disco.

    No entanto, usando a análise de memória, você pode detectar esse tipo de ataque. Analisando a memória no despejo de falha, a central de segurança pode detectar as técnicas que o ataque está usando. Por exemplo, o ataque pode estar tentando explorar vulnerabilidades no software, acessar dados confidenciais e maneira furtiva persistir em um computador comprometido. A central de segurança faz isso funcionar com impacto mínimo sobre o desempenho dos hosts.

    Para obter detalhes dos alertas de análise de despejo de memória, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-windows).

* **Detecção** <a name="windows-fileless"></a> de ataque de não-arquivo-ataques de não-arquivo que visam seus pontos de extremidade são comuns. Para evitar a detecção, os ataques de arquivo insuficiente injetam cargas mal-intencionadas na memória. As cargas de invasor persistem na memória de processos comprometidos e executam uma ampla gama de atividades mal-intencionadas.

    Com a detecção de ataque sem arquivo, as técnicas forenses de memória automatizada identificam os kits de informática, técnicas e comportamentos de ataque sem arquivo. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória de processos críticos de segurança.

    Ele encontra evidências de exploração, injeção de código e execução de cargas mal-intencionadas. A detecção de ataques de não-arquivo gera alertas de segurança detalhados para acelerar a triagem de alerta, correlação e tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos, fornecendo maior cobertura de detecção.

    Para obter detalhes dos alertas de detecção de ataque sem arquivo, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-windows).

> [!TIP]
> Você pode simular alertas do Windows baixando o [guia estratégico da central de segurança do Azure: alertas de segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Proteção contra ameaças para computadores Linux<a name="linux-machines"></a>

A central de segurança coleta registros de auditoria de computadores Linux usando **auditoria**, uma das estruturas de auditoria do Linux mais comuns. a auditoria reside no kernel principal. 

* **Alertas auditados do Linux e integração** <a name="linux-auditd"></a> do agente de log Analytics-o sistema auditado consiste em um subsistema de nível de kernel, que é responsável por monitorar chamadas do sistema. Ele os filtra por um conjunto de regras especificado e grava mensagens para eles em um soquete. A central de segurança integra as funcionalidades do pacote auditado no agente de Log Analytics. Essa integração habilita a coleta de eventos auditados em todas as distribuições Linux com suporte, sem nenhum pré-requisito.

    os registros auditados são coletados, aprimorados e agregados em eventos usando o agente do Log Analytics para o agente do Linux. A central de segurança adiciona continuamente uma nova análise que usa sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, essas análises se estendem por processos suspeitos, tentativas de entrada duvidosa, carregamento de módulo kernel e outras atividades. Essas atividades podem indicar que um computador está sob ataque ou foi violado.  

    Para obter uma lista dos alertas do Linux, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-linux).

> [!TIP]
> Você pode simular os alertas do Linux baixando o [guia estratégico da central de segurança do Azure: detecções do Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Proteção contra ameaças para o serviço Azure App<a name="app-services"></a>

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

A central de segurança usa a escala da nuvem para identificar os ataques que visam os aplicativos em execução no serviço de aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de ser roteado para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionados e registrados. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.

Usando a visibilidade que o Azure tem como um provedor de nuvem, a central de segurança analisa os logs internos do serviço de aplicativo para identificar a metodologia de ataque em vários destinos. Por exemplo, a metodologia inclui verificação generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e mostra padrões de rastreamento para pontos de extremidade semelhantes em vários hosts. Os ataques estão procurando uma página ou um plug-in vulnerável e não podem ser identificados no ponto de vista de um único host.

Se você estiver executando um plano do serviço de aplicativo baseado no Windows, a central de segurança também terá acesso às áreas restritas e VMs subjacentes. Junto com os dados de log mencionados acima, a infra-estrutura pode contar a história, desde um novo ataque que está circulando de forma a comprometimentos nas máquinas dos clientes. Portanto, mesmo que a central de segurança seja implantada depois que um aplicativo Web for explorado, ele poderá detectar ataques contínuos.

Para obter uma lista dos alertas de serviço Azure App, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).

Para obter mais informações sobre os planos do serviço de aplicativo, consulte [planos do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Proteção contra ameaças para contêineres do Azure<a name="azure-containers"></a>

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

A central de segurança fornece proteção contra ameaças em tempo real para seus ambientes em contêineres e gera alertas para atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

A central de segurança fornece proteção contra ameaças em diferentes níveis: 

* **Nível de host** – agente da central de segurança (disponível na camada Standard, consulte [preços](security-center-pricing.md) para obter detalhes) monitora as atividades suspeitas no Linux. O agente dispara alertas para atividades suspeitas provenientes do nó ou de um contêiner em execução nele. Exemplos dessas atividades incluem a detecção e a conexão de Shell da Web com endereços IP suspeitos conhecidos.

    Para obter uma visão mais profunda da segurança do seu ambiente em contêiner, o agente monitora a análise específica do contêiner. Ele disparará alertas para eventos como criação de contêiner privilegiado, acesso suspeito a servidores de API e servidores Secure Shell (SSH) em execução dentro de um contêiner do Docker.

    >[!IMPORTANT]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados à análise de rede e comunicações com servidores mal-intencionados.

    Para obter uma lista dos alertas de nível de host, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-containerhost).


* No **nível do cluster AKs**, a proteção contra ameaças é baseada na análise de logs de auditoria do kubernetes. Para habilitar esse monitoramento **sem agente** , adicione a opção kubernetes à sua assinatura na página de **configurações de & de preços** (consulte [preços](security-center-pricing.md)). Para gerar alertas nesse nível, a central de segurança monitora seus serviços gerenciados AKS usando os logs recuperados pelo AKS. Exemplos de eventos neste nível incluem painéis kubernetes expostos, criação de funções com altos privilégios e a criação de montagens confidenciais.

    >[!NOTE]
    > A central de segurança gera alertas de segurança para as ações e implantações do serviço kubernetes do Azure que ocorrem após a opção kubernetes ser habilitada nas configurações de assinatura. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-akscluster).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções nesta [postagem no blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Proteção contra ameaças para banco de dados SQL e SQL Data Warehouse<a name="data-sql"></a>

A proteção avançada contra ameaças para o banco de dados SQL do Azure detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Você verá alertas quando houver atividades suspeitas de banco de dados, vulnerabilidades potenciais ou ataques de injeção de SQL e padrões de consulta e acesso de banco de dados anormais.

A proteção avançada contra ameaças para o banco de dados SQL do Azure e o SQL fazem parte do pacote unificado do [ADS (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para recursos avançados de segurança do SQL, que abrangem os bancos de dados SQL do Azure, instâncias gerenciadas do Azure SQL Database, bancos de dados do Azure SQL data warehouse e SQL Servers em máquinas virtuais do Azure.

Para obter mais informações, consulte:

* [Como habilitar a proteção avançada contra ameaças para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como habilitar a proteção avançada contra ameaças para servidores SQL em máquinas virtuais do Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção contra ameaças para o banco de dados SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Proteção contra ameaças para o armazenamento do Azure<a name="azure-storage"></a>

A proteção avançada contra ameaças para armazenamento detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem exigir que você seja um especialista em segurança e ajuda você a gerenciar seus sistemas de monitoramento de segurança.

A proteção avançada contra ameaças para o armazenamento do Azure está disponível no momento apenas para o [armazenamento de BLOBs](https://azure.microsoft.com/services/storage/blobs/). 

Esse serviço está disponível em todas as nuvens públicas e nuvens do governo dos EUA, mas sem outras regiões de nuvem do soberanas ou do Azure governamental.

Para obter detalhes de preços, incluindo uma avaliação gratuita de 30 dias, consulte a [página de preços da central de segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações, consulte:

* [Como habilitar a proteção avançada contra ameaças para o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o armazenamento do Azure](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Você pode simular alertas de armazenamento do Azure seguindo as instruções nesta [postagem no blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Proteção contra ameaças para Azure Cosmos DB<a name="cosmos-db"></a>

Os alertas de Azure Cosmos DB são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Azure Cosmos DB.

Para obter mais informações, consulte:

* [Proteção avançada contra ameaças para Azure Cosmos DB (versão prévia)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para Azure Cosmos DB (versão prévia)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para a camada de rede do Azure<a name="network-layer"></a>

A central de segurança rede – a análise de camada baseia-se nos [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)de exemplo, que são os cabeçalhos de pacote coletados pelos roteadores do Azure Core. Com base nesse feed de dados, a central de segurança usa modelos de aprendizado de máquina para identificar e sinalizar atividades de tráfego mal-intencionado. A central de segurança também usa o banco de dados de inteligência contra ameaças da Microsoft para enriquecer os endereços IP.

Algumas configurações de rede podem impedir que a central de segurança Gere alertas sobre a atividade de rede suspeita. Para a central de segurança gerar alertas de rede, verifique se:

- Sua máquina virtual tem um endereço IP público (ou está em um balanceador de carga com um endereço IP público).

- O tráfego de saída da rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

- Sua máquina virtual recebeu o mesmo endereço IP para a hora inteira durante a qual a comunicação suspeita ocorreu. Isso também se aplica a VMs criadas como parte de um serviço gerenciado (por exemplo, AKS, databricks).

Para obter uma lista dos alertas da camada de rede do Azure, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azurenetlayer).

Para obter detalhes de como a central de segurança pode usar sinais relacionados à rede para aplicar a proteção contra ameaças, consulte [detecção heurística de DNS na central de segurança](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Proteção contra ameaças para a camada de gerenciamento do Azure (Azure Resource Manager) (visualização)<a name ="management-layer"></a>

A camada de proteção da central de segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A central de segurança oferece uma camada adicional de proteção usando eventos Azure Resource Manager, que é considerada o plano de controle do Azure. Analisando os registros de Azure Resource Manager, a central de segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

Para obter uma lista dos alertas de Azure Resource Manager (versão prévia), consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas por Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença de Cloud App Security. Se você tiver uma licença Cloud App Security, esses alertas serão habilitados por padrão. Para desabilitar os alertas:
>
> 1. Na folha **central de segurança** , selecione **política de segurança**. Para a assinatura que você deseja alterar, selecione **Editar configurações**.
> 2. Selecione **detecção de ameaças**.
> 3. Em **habilitar integrações**, desmarque **permitir que Microsoft Cloud app Security acesse meus dados**e selecione **salvar**.

>[!NOTE]
>A central de segurança armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a central de segurança na área geográfica do recurso, ele armazenará os dados no Estados Unidos. Quando Cloud App Security está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica do Cloud App Security. Para obter mais informações, consulte [armazenamento de dados para serviços não regionais](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para Azure Key Vault (versão prévia)<a name="azure-keyvault"></a>

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

A central de segurança do Azure inclui proteção de ameaças avançada e nativa do Azure para Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. A central de segurança detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Key Vault. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança e sem a necessidade de gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando ocorrem atividades anômalas, a central de segurança mostra alertas e, opcionalmente, os envia por email aos administradores de assinatura. Esses alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e corrigir ameaças. 

Para obter uma lista dos alertas de Azure Key Vault, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra ameaças para o Azure WAF<a name="azure-waf"></a>

O Gateway de Aplicativo do Azure oferece um WAF (firewall do aplicativo Web) que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O WAF do gateway de aplicativo baseia-se no conjunto de regras principais 3,0 ou 2.2.9 do projeto de segurança de aplicativo Web aberto. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades. 

Se você tiver uma licença para o Azure WAF, seus alertas do WAF serão transmitidos para a central de segurança sem a necessidade de configuração adicional. Para obter mais informações sobre os alertas gerados pelo WAF, consulte [regras e grupos de regras CRS do firewall do aplicativo Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra ameaças para a proteção contra DDoS do Azure<a name="azure-ddos"></a>

Ataques de DDoS (negação de serviço distribuído) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você estiver movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Os ataques de DDoS podem ter como destino qualquer ponto de extremidade que possa ser acessado pela Internet.

Para se defender contra ataques de DDoS, compre uma licença para a proteção contra DDoS do Azure e verifique se você está seguindo as práticas recomendadas de design de aplicativo. A proteção contra DDoS fornece diferentes camadas de serviço. Para obter mais informações, consulte [visão geral da proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Para obter uma lista dos alertas de proteção contra DDoS do Azure, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os alertas de segurança desses recursos de proteção contra ameaças, consulte os seguintes artigos:

* [Tabela de referência para todos os alertas da central de segurança do Azure](alerts-reference.md)
* [Alertas de segurança na Central de Segurança do Azure](security-center-alerts-overview.md)
* [Gerenciar e responder a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Exportar alertas de segurança e recomendações (versão prévia)](continuous-export.md)