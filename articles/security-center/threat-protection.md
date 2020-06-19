---
title: Proteção contra ameaças na Central de Segurança do Azure
description: Este tópico descreve os recursos protegidos pelos recursos de proteção contra ameaças da Central de Segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 850b06153a25020f36a4c7df1863e5a576495f3b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744171"
---
# <a name="threat-protection-in-azure-security-center"></a>Proteção contra ameaças na Central de Segurança do Azure

Quando a Central de Segurança detecta uma ameaça em qualquer área do ambiente, ela gera um alerta. Esses alertas descrevem os detalhes dos recursos afetados, as etapas de correção sugeridas e, em alguns casos, uma opção para disparar um aplicativo lógico em resposta.

A proteção contra ameaças da Central de Segurança do Azure fornece defesas abrangentes para seu ambiente:

* **Proteção contra ameaças para recursos de computação do Azure**: Computadores Windows, computadores Linux, Serviço de Aplicativo do Azure e contêineres do Azure

* **Proteção contra ameaças para recursos de dados do Azure**: Banco de Dados SQL e SQL Data Warehouse, Armazenamento do Microsoft Azure e Azure Cosmos DB

* **Proteção contra ameaças para camadas de serviço do Azure**: Camada de rede do Azure, camada de gerenciamento do Azure (Azure Resource Manager - versão prévia) e Azure Key Vault (versão prévia)

Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md). 

> [!NOTE]
> Alertas de fontes diferentes podem levar diferentes quantidades de tempo para serem exibidos. Por exemplo, alertas que exigem análise de tráfego de rede podem levar mais tempo para aparecer do que alertas relacionados a processos suspeitos em execução em máquinas virtuais.

> [!TIP]
> Para habilitar os recursos de proteção contra ameaças da Central de Segurança, você deve aplicar o tipo de preço padrão à assinatura que contém as cargas de trabalho aplicáveis.
>
> Você pode habilitar a proteção contra ameaças para **contas de Armazenamento do Azure** no nível da assinatura ou no nível do recurso.
> Você pode habilitar a proteção contra ameaças para **servidores SQL do Banco de Dados SQL do Azure** no nível da assinatura ou no nível do recurso.
> Você pode habilitar a proteção contra ameaças para o **Banco de Dados do Azure para MariaDB/MySQL/PostgreSQL** apenas no nível do recurso.



## <a name="threat-protection-for-windows-machines"></a>Proteção contra ameaças para computadores Windows <a name="windows-machines"></a>

A Central de Segurança do Azure integra-se aos serviços do Azure para monitorar e proteger seus computadores Windows. A Central de Segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

* **Proteção Avançada contra Ameaças (ATP) do Microsoft Defender** <a name="windows-atp"></a> - a Central de Segurança estende suas plataformas de proteção de carga de trabalho de nuvem integrando-se à Proteção Avançada contra Ameaças (ATP) do Microsoft Defender. Juntas, elas fornecem recursos abrangentes de Detecção e resposta de ponto de extremidade (EDR).

    > [!IMPORTANT]
    > O sensor da ATP do Microsoft Defender é habilitado automaticamente em servidores Windows que usam a Central de Segurança.

    Quando a ATP do Microsoft Defender detecta uma ameaça, ela dispara um alerta. O alerta é mostrado no painel da Central de Segurança. No painel, você pode dinamizar para o console da ATP do Microsoft Defender e realizar uma investigação detalhada para descobrir o escopo do ataque. Para saber mais sobre a ATP do Microsoft Defender, confira [Integrar servidores ao serviço da ATP do Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Detecção de ataques sem arquivo** <a name="windows-fileless"></a> - ataques sem arquivos direcionados aos seus pontos de extremidade são comuns. Para evitar a detecção, os ataques sem arquivos injetam cargas mal-intencionadas na memória. Os payloads do invasor persistem na memória de processos comprometidos e executam uma ampla gama de atividades mal-intencionadas.

    Com a detecção de ataques sem arquivos, as técnicas forenses de memória automatizada identificam os toolkits, as técnicas e os comportamentos de ataques sem arquivos. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória de processos críticos de segurança.

    Ele encontra evidências de exploração, injeção de código e execução de payloads mal-intencionados. A detecção de ataques sem arquivos gera alertas de segurança detalhados para acelerar a triagem de alerta, a correlação e o tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos, proporcionando uma maior cobertura de detecção.

    Para obter detalhes sobre os alertas de detecção de ataques sem arquivos, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-windows).

> [!TIP]
> Você pode simular os alertas do Windows baixando o [Guia estratégico da Central de Segurança do Azure: Alertas de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Proteção contra ameaças para computadores Linux <a name="linux-machines"></a>

A Central de Segurança coleta registros de auditoria de computadores Linux usando **auditd**, uma das estruturas de auditoria mais comuns do Linux. auditd reside no kernel principal. 

* **Alertas de auditd do Linux e integração do agente do Log Analytics** <a name="linux-auditd"></a> - o sistema auditd consiste em um subsistema de nível de kernel, que é responsável por monitorar chamadas do sistema. Ele as filtra de acordo com um conjunto de regras especificado e grava mensagens para eles em um soquete. A Central de Segurança integra as funcionalidades do pacote auditd no agente do Log Analytics. Essa integração habilita a coleta de eventos auditd em todas as distribuições Linux com suporte, sem nenhum pré-requisito.

    Os registros auditd são coletados, aprimorados e agregados em eventos usando o agente do Log Analytics para o agente do Linux. A Central de Segurança adiciona continuamente novos recursos de análise que usam sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, esses recursos de análise abrangem processos suspeitos, tentativas de entrada duvidosa, carregamento de módulos kernel e outras atividades. Essas atividades podem indicar que um computador está sob ataque ou foi violado.  

    Para obter uma lista dos alertas do Linux, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-linux).

> [!TIP]
> Você pode simular os alertas do Linux baixando o [Guia estratégico da Central de Segurança do Azure: Detecções do Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Proteção contra ameaças para o Serviço de Aplicativo do Azure <a name="app-services"></a>

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões de nuvem soberana e governamental do Azure.

A Central de Segurança usa a escala da nuvem para identificar os ataques direcionados a aplicativos em execução no Serviço de Aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de serem roteadas para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionadas e registradas. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.

Usando a visibilidade que o Azure tem como um provedor de nuvem, a Central de Segurança analisa os logs internos do Serviço de Aplicativo para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui verificação generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e mostra padrões de rastreamento para pontos de extremidade semelhantes em vários hosts. Os ataques estão procurando uma página ou um plug-in vulnerável e não podem ser identificados no ponto de vista de um único host.

Se você estiver executando um plano do Serviço de Aplicativo baseado no Windows, a Central de Segurança também terá acesso às áreas restritas e VMs subjacentes. Juntamente com os dados de log mencionados acima, a infraestrutura pode fornecer as informações, desde um novo ataque que está ocorrendo no mundo até comprometimentos nas máquinas dos clientes. Portanto, mesmo que a Central de Segurança seja implantada depois que um aplicativo Web for explorado, ela poderá detectar ataques contínuos.

Para obter uma lista dos alertas do Serviço de Aplicativo do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).

Para saber mais sobre os planos do Serviço de Aplicativo, confira [Planos do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Proteção contra ameaças para contêineres do Azure <a name="azure-containers"></a>

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões de nuvem soberana e governamental do Azure.

A Central de Segurança fornece proteção contra ameaças em tempo real para seus ambientes em contêineres e gera alertas sobre atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

A Central de Segurança fornece proteção contra ameaças em diferentes níveis: 

* **Nível do host** - o agente da Central de Segurança (disponível na camada Standard, confira [Preços](security-center-pricing.md) para obter detalhes) monitora atividades suspeitas no Linux. O agente dispara alertas sobre atividades suspeitas provenientes do nó ou de um contêiner em execução nele. Exemplos dessas atividades incluem a detecção e a conexão de Shell da Web com endereços IP suspeitos conhecidos.

    Para obter uma visão mais profunda da segurança do seu ambiente em contêiner, o agente monitora a análise específica do contêiner. Ele disparará alertas para eventos como a criação de contêineres privilegiados, acessos suspeitos a servidores de API e servidores Secure Shell (SSH) em execução dentro de um contêiner do Docker.

    >[!IMPORTANT]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.

    Para obter uma lista dos alertas no nível do host, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-containerhost).


* No **nível do cluster AKS**, a proteção contra ameaças é baseada na análise de logs de auditoria do Kubernetes. Para habilitar esse monitoramento **sem agente**, adicione a opção Kubernetes à sua assinatura na página **Preços e configurações** (confira [Preços](security-center-pricing.md)). Para gerar alertas nesse nível, a Central de Segurança monitora seus serviços gerenciados pelo AKS usando os logs recuperados pelo AKS. Exemplos de eventos nesse nível incluem painéis expostos do Kubernetes, a criação de funções com altos privilégios e a criação de montagens confidenciais.

    >[!NOTE]
    > A Central de Segurança gera alertas de segurança para ações e implantações do Serviço de Kubernetes do Azure que ocorrem após a opção Kubernetes ser habilitada nas configurações de assinatura. 

    Para obter uma lista dos alertas no nível do cluster AKS, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-akscluster).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Proteção contra ameaças para o Banco de Dados SQL e o SQL Data Warehouse <a name="data-sql"></a>

A Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Você receberá alertas quando ocorrerem atividades suspeitas no banco de dados possíveis vulnerabilidades ou ataques de injeção de SQL, além de padrões anômalos de consultas e acesso a banco de dados.

A Proteção Avançada contra Ameaças para Banco de Dados SQL do Azure e SQL faz parte do pacote unificado de [Segurança de Dados Avançada (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para recursos avançados de segurança do SQL, que abrangem Bancos de Dados SQL do Azure, instâncias gerenciadas do Banco de Dados SQL do Azure, bancos de dados do Azure SQL Data Warehouse e servidores SQL em máquinas virtuais.

Para obter mais informações, consulte:

* [Como habilitar a Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como habilitar a Proteção Avançada contra Ameaças para servidores SQL em Máquinas Virtuais do Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção contra ameaças para o Banco de Dados SQL e o SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Proteção contra Ameaças do Armazenamento do Azure <a name="azure-storage"></a>

A Proteção Avançada contra Ameaças do Armazenamento detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem exigir que você seja um especialista em segurança e ajuda você a gerenciar sistemas de monitoramento de segurança.

A Proteção Avançada contra Ameaças para o Armazenamento do Azure está disponível, no momento, apenas para o [Armazenamento de Blob](https://azure.microsoft.com/services/storage/blobs/). 

Esse serviço está disponível em todas as nuvens públicas e nuvens do governo dos EUA, mas sem outras regiões de nuvem soberanas ou governamentais do Azure.

Para obter detalhes sobre preços, incluindo uma avaliação gratuita de 30 dias, confira a [página de preços da Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações, consulte:

* [Como habilitar a Proteção Avançada contra Ameaças para o Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o Armazenamento do Azure](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Você pode simular alertas do Armazenamento do Azure seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Proteção contra ameaças para o Azure Cosmos DB <a name="cosmos-db"></a>

Os alertas do Azure Cosmos DB são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos DB.

Para obter mais informações, consulte:

* [Proteção Avançada contra Ameaças do Azure Cosmos DB (versão prévia)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para o Azure Cosmos DB (versão prévia)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para a camada de rede do Azure <a name="network-layer"></a>

Os recursos de análise da camada de rede da Central de Segurança são baseados em [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) de amostra, que são os cabeçalhos de pacote coletados pelos roteadores principais do Azure. Com base nesse feed de dados, a Central de Segurança usa modelos de aprendizado de máquina para identificar e sinalizar atividades de tráfego mal-intencionadas. A Central de Segurança também usa o banco de dados de inteligência contra ameaças da Microsoft para aprimorar os endereços IP.

Algumas configurações de rede podem impedir que a Central de Segurança gere alertas sobre atividades de rede suspeitas. Para que a Central de Segurança gere alertas de rede, verifique se:

- Sua máquina virtual tem um endereço IP público (ou está em um balanceador de carga com um endereço IP público).

- O tráfego de saída da rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

- Sua máquina virtual recebeu o mesmo endereço IP para a hora inteira durante a qual a comunicação suspeita ocorreu. Isso também se aplica a VMs criadas como parte de um serviço gerenciado (por exemplo, AKS, Databricks).

Para obter uma lista dos alertas da camada de rede do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azurenetlayer).

Para obter detalhes sobre como a Central de Segurança pode usar sinais relacionados à rede para aplicar a proteção contra ameaças, confira [Detecções heurísticas de DNS na Central de Segurança](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Proteção contra ameaças para a camada de gerenciamento do Azure (Azure Resource Manager, versão prévia)<a name ="management-layer"></a>

A camada de proteção da Central de Segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A Central de Segurança oferece uma camada adicional de proteção usando eventos do Azure Resource Manager, que é considerado o plano de controle do Azure. Ao analisar os registros de Azure Resource Manager, a Central de Segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

Para obter uma lista dos alertas do Azure Resource Manager (versão prévia), confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas pelo Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença do Cloud App Security. Se você tiver uma licença do Cloud App Security, esses alertas serão habilitados por padrão. Para desabilitar os alertas:
>
> 1. Na tela **Central de Segurança**, selecione **Política de segurança**. Para a assinatura que você deseja alterar, selecione **Editar configurações**.
> 2. Selecione **Detecção de ameaças**.
> 3. Em **Habilitar integrações**, desmarque a opção **Permitir que o Microsoft Cloud App Security acesse meus dados** e selecione **Salvar**.

>[!NOTE]
>A Central de Segurança armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a Central de Segurança na área geográfica do recurso, ela armazenará os dados no Estados Unidos. Quando o Cloud App Security está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica do Cloud App Security. Para saber mais, confira [Armazenamento de dados para serviços não regionais](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças do Azure Key Vault (versão prévia)<a name="azure-keyvault"></a>

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões de nuvem soberana e governamental do Azure.

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

A Central de Segurança do Azure inclui proteção de ameaças avançada e nativa do Azure para o Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. A Central de Segurança detecta tentativas incomuns e possivelmente prejudiciais de acessar ou explorar as contas do Key Vault. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança e sem precisar gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando ocorrem atividades anômalas, a Central de Segurança mostra alertas e, opcionalmente, os envia por email aos administradores da assinatura. Esses alertas incluem detalhes das atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. 

Para obter uma lista dos alertas do Azure Key Vault, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra Ameaças para o Azure WAF <a name="azure-waf"></a>

O Gateway de Aplicativo do Azure oferece um WAF (firewall do aplicativo Web) que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O WAF do Gateway de Aplicativo é baseado Conjunto de Regras Principais 3.0 ou 2.2.9 do Open Web Application Security Project. O WAF é atualizado automaticamente para proteger você contra novas vulnerabilidades. 

Se você tiver uma licença para o Azure WAF, seus alertas do WAF serão transmitidos para a Central de Segurança sem precisar de configurações adicionais. Para saber mais sobre alertas gerados pelo WAF, confira [Regras e grupos de regras CRS do firewall do aplicativo Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra Ameaças para a Proteção contra DDoS <a name="azure-ddos"></a>

Ataques distribuídos de negação de serviço (DDoS) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você estiver movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Os ataques de DDoS podem ter como alvo qualquer ponto de extremidade que possa ser acessado pela Internet.

Para se defender contra ataques de DDoS, compre uma licença para a Proteção contra DDoS do Azure e certifique-se de seguir as práticas recomendadas de design de aplicativo. A Proteção contra DDoS fornece diferentes camadas de serviço. Para saber mais, confira [Visão geral da Proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Para obter uma lista dos alertas da Proteção contra DDoS do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os alertas de segurança desses recursos de proteção contra ameaças, confira os seguintes artigos:

* [Tabela de referência para todos os alertas da Central de Segurança do Azure](alerts-reference.md)
* [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Exportar alertas e recomendações de segurança (versão prévia)](continuous-export.md)