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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 69f439e102edc53207e44d63cb29396f64f59e0e
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272494"
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

* Detecção de ataque de não- **arquivo** <a name="windows-fileless"></a> -Ataques de arquivo insuficiente injetam cargas mal-intencionadas na memória para evitar a detecção por técnicas de verificação baseadas em disco. A carga do invasor persiste dentro da memória de processos comprometidos e executa uma ampla variedade de atividades mal-intencionadas.

    Com a detecção de ataques sem arquivos, as técnicas forenses de memória automatizada identificam os toolkits, as técnicas e os comportamentos de ataques sem arquivos. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória dos processos. Informações específicas para Linux incluem a identificação de: 

    - Kits de programas conhecidos e software de mineração de criptografia 
    - Shellcode, que é uma pequena parte do código normalmente usada como a carga na exploração de uma vulnerabilidade de software.
    - Executável mal-intencionado injetado na memória do processo

    A detecção de ataque sem arquivo gera alertas de segurança detalhados contendo as descrições com metadados de processo adicionais, como a atividade de rede. Isso acelera a triagem de alerta, correlação e tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos e fornece maior cobertura de detecção.

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





## <a name="threat-protection-for-containers"></a>Proteção contra ameaças para contêineres <a name="azure-containers"></a>

### <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Disponível|
|Refere|Camada padrão|
|Funções e permissões necessárias:|O **administrador de segurança** pode ignorar alertas.<br>**O leitor de segurança** pode exibir as conclusões.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Proteção contra ameaças para o Banco de Dados SQL e o SQL Data Warehouse <a name="data-sql"></a>

A Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Você receberá alertas quando ocorrerem atividades suspeitas no banco de dados possíveis vulnerabilidades ou ataques de injeção de SQL, além de padrões anômalos de consultas e acesso a banco de dados.

A proteção avançada contra ameaças para o banco de dados SQL do Azure e o SQL fazem parte do pacote unificado do [ADS (segurança de dados avançado)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para recursos avançados de segurança do SQL, cobrindo o banco de dados SQL do Azure, instâncias gerenciadas do Azure SQL, bancos de dados do Azure SQL data warehouse e SQL Servers em máquinas virtuais do

Para obter mais informações, consulte:

* [Como habilitar a Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como habilitar a Proteção Avançada contra Ameaças para servidores SQL em Máquinas Virtuais do Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção contra ameaças para o Banco de Dados SQL e o SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Proteção contra Ameaças do Armazenamento do Azure <a name="azure-storage"></a>

### <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|[Armazenamento de BLOBs](https://azure.microsoft.com/services/storage/blobs/) (disponibilidade geral)<br>[Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (versão prévia)<br>[Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (versão prévia)|
|Refere|Camada padrão|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) China gov, outros gov|
|||


### <a name="whats-protected"></a>O que é protegido?

A proteção contra ameaças para o armazenamento do Azure detecta atividade potencialmente prejudicial em suas contas de armazenamento do Azure. Seus dados podem ser protegidos independentemente de serem armazenados como contêineres de BLOB, compartilhamentos de arquivos ou data lagos.

Essa camada de proteção permite que você resolva as ameaças *sem* exigir que você seja um especialista em segurança e ajuda você a gerenciar seus sistemas de monitoramento de segurança.

Suas contas de armazenamento estão protegidas 

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Que tipo de alertas a proteção contra ameaças para o armazenamento do Azure fornece?

Os alertas de segurança são disparados quando há:

- **Atividade suspeita** -por exemplo, a conta de armazenamento foi acessada com êxito de um endereço IP que é conhecido como nó de saída ativo de Tor
- **Comportamento anormal** – por exemplo, alterações no padrão de acesso para uma conta de armazenamento
- **Possível malware carregado** -a análise de reputação de hash indica que um arquivo carregado contém malware

Os alertas incluem detalhes do incidente que os disparou, bem como recomendações sobre como investigar e corrigir ameaças.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>O que é a análise de reputação de hash para malware?

Para determinar se um arquivo carregado é suspeito, a proteção contra ameaças para o armazenamento do Azure usa a análise de reputação de hash com suporte da [inteligência de ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). As ferramentas de proteção contra ameaças não verificam os arquivos carregados, em vez disso examinam os logs de armazenamento e comparam os hashes de arquivos carregados recentemente com aqueles de vírus conhecidos, cavalos de Tróia, spyware e ransomware. 

Quando é suspeito que um arquivo contenha malware, a central de segurança exibe um alerta e pode, opcionalmente, enviar por email o proprietário do armazenamento para aprovação para excluir o arquivo suspeito. Para configurar essa remoção automática de arquivos que a análise de reputação de hash indica conter malware, implante uma [automação de fluxo de trabalho para disparar alertas que contenham "possíveis malwares carregados em uma conta de armazenamento"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>Próximas etapas 

Para obter detalhes sobre preços, incluindo uma avaliação gratuita de 30 dias, confira a [página de preços da Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações, consulte:

* [Como habilitar a Proteção Avançada contra Ameaças para o Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o Armazenamento do Azure](alerts-reference.md#alerts-azurestorage)
* [Recursos de inteligência contra ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> Você pode simular alertas de armazenamento seguindo as instruções nesta [postagem no blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).







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




## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Proteção contra ameaças para a camada de gerenciamento do Azure (Azure Resource Manager, versão prévia)<a name ="management-layer"></a>

A camada de proteção da Central de Segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A Central de Segurança oferece uma camada adicional de proteção usando eventos do Azure Resource Manager, que é considerado o plano de controle do Azure. Ao analisar os registros de Azure Resource Manager, a Central de Segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

Para obter uma lista dos alertas do Azure Resource Manager (versão prévia), confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas pelo Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença do Cloud App Security. Se você tiver uma licença do Cloud App Security, esses alertas serão habilitados por padrão. Para desabilitar os alertas:
>
> 1. No menu da central de segurança, selecione **preços & configurações**.
> 1. Selecione a assinatura que deseja alterar.
> 1. Selecione **Detecção de ameaças**.
> 1. Desmarque **permitir que o Microsoft Cloud app Security acesse meus dados**e selecione **salvar**.

>[!NOTE]
>A Central de Segurança armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a Central de Segurança na área geográfica do recurso, ela armazenará os dados no Estados Unidos. Quando o Cloud App Security está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica do Cloud App Security. Para saber mais, confira [Armazenamento de dados para serviços não regionais](https://azuredatacentermap.azurewebsites.net/).

1. Defina o espaço de trabalho no qual você está instalando o agente. Verifique se que o workspace está na mesma assinatura usada na Central de Segurança e se você tem permissões de leitura/gravação no workspace.

1. Defina o tipo de preço padrão e selecione **salvar**.






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