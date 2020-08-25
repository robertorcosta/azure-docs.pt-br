---
title: Controles da amostra de blueprint de HIPAA/HITRUST
description: Mapeamento do controle de amostras de blueprint de HIPAA/HITRUST. Cada controle é mapeado para uma ou mais Políticas do Azure que auxiliam na avaliação.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209424"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapeamento do controle de amostra de blueprint de HIPAA/HITRUST

O artigo a seguir fornece detalhes de como a amostra de blueprint de HIPAA/HITRUST do Azure Blueprints é mapeada para os controles do HIPAA/HITRUST. Para obter mais informações sobre os controles, confira [HIPAA/HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Os mapeamentos a seguir são para os controles de **HIPAA/HITRUST**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: inciativa de política interna Auditoria de controles do HIPAA/HITRUST**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência 1:1 ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controle contra código mal-intencionado

Este blueprint ajuda você a gerenciar a proteção de ponto de extremidade, incluindo a proteção contra código mal-intencionado, por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente em máquinas virtuais na Central de Segurança do Azure e impõem a solução Microsoft Antimalware em máquinas virtuais do Windows.

- O Microsoft Antimalware para o Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção
- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais


## <a name="management-of-removable-media"></a>Gerenciamento de mídia removível

A organização, com base no nível de classificação de dados, registra mídias (incluindo laptops) antes do uso, coloca restrições cabíveis na maneira como essas mídias devem ser usadas e fornece um nível apropriado de proteção física e lógica (incluindo criptografia) para mídia que contenha informações sigilosas até que seja corretamente destruída ou limpa.

- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- A criptografia de disco deve ser aplicada em máquinas virtuais
- Os discos desconectados devem ser criptografados
- Exigir a criptografia em contas do Data Lake Storage
- O protetor TDE do SQL Server deve ser criptografado com sua própria chave
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave

## <a name="control-of-operational-software"></a>Controle de software operacional 

A organização identifica software não autorizado no sistema de informações, incluindo servidores, estações de trabalho e laptops, emprega uma política de permitir tudo, negar por exceção para proibir a execução de software não autorizado conhecido no sistema de informações e revisa e atualiza a lista de softwares não autorizados com periodicidade não menor que anualmente.

- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="change-control-procedures"></a>Procedimentos de controle de alterações

A integridade de todas as imagens de máquina virtual é garantida o tempo todo pelo registro em log e geração de um alerta para todas as alterações feitas em imagens de máquina virtual, disponibilizando para os proprietários de negócios e/ou clientes, por meio de métodos eletrônicos (por exemplo, portais ou alertas), os resultados de uma alteração ou movimentação e a validação subsequente da integridade da imagem.

- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Políticas de Auditoria do Sistema – Acompanhamento Detalhado"
- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em “Políticas de Auditoria do Sistema – Acompanhamento Detalhado”

## <a name="control-of-technical-vulnerabilities"></a>Controle de vulnerabilidades técnicas 

Existe um padrão de configuração reforçado para todos os componentes de sistema e de rede.

- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- \[Versão prévia\] A Avaliação de Vulnerabilidades deve ser habilitada nas Máquinas Virtuais
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- \[Versão Prévia\]: as políticas de segurança de pods devem ser definidas nos Serviços de Kubernetes

## <a name="segregation-in-networks"></a>Diferenciação em redes

Os gateways de segurança da organização (por exemplo, firewalls) impõem políticas de segurança e são configurados para filtrar o tráfego entre domínios, bloquear acesso não autorizado e são usados para manter a diferenciação entre segmentos de rede internos com fio, internos sem fio e externos (por exemplo, a Internet), incluindo DMZs e imposição de políticas de controle de acesso para cada um dos domínios.

- As sub-redes devem ser associadas a um Grupo de Segurança de Rede
- As máquinas virtuais devem estar conectadas a uma rede virtual aprovada
- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede
- O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual
- O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual
- O SQL Server deve usar um ponto de extremidade de serviço de rede virtual
- O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual
- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB
- O Key Vault deve usar um ponto de extremidade de serviço de rede virtual
- As sub-redes de gateway não devem ser configuradas com um grupo de segurança de rede
- As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual
- \[Versão Prévia\]: O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual
- As recomendações de Proteção de Rede Adaptável devem ser aplicadas em máquinas virtuais internas

## <a name="network-connection-control"></a>Controle de conexão de rede

O tráfego de rede é controlado de acordo com a política de controle de acesso da organização por meio do firewall e de outras restrições relacionadas à rede para cada ponto de acesso à rede ou interface gerenciada do serviço de telecomunicação externo.

- A transferência segura para contas de armazenamento deve ser habilitada
- A última versão do TLS deve ser usada no aplicativo de API
- A última versão do TLS deve ser usada no aplicativo Web
- A última versão do TLS deve ser usada no aplicativo de funções
- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- As sub-redes devem ser associadas a um Grupo de Segurança de Rede
- As regras NSGs para aplicativos Web em IaaS devem ser protegidas
- As regras do grupo de segurança de rede para máquinas virtuais para a Internet devem ser protegidas
- As máquinas virtuais devem estar conectadas a uma rede virtual aprovada
- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede

## <a name="network-controls"></a>Controles de rede

A organização usa canais de comunicação seguros e criptografados ao migrar servidores físicos, aplicativos ou dados para servidores virtualizados.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais
- As recomendações de Proteção de Rede Adaptável devem ser aplicadas em máquinas virtuais internas
- O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual
- O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual
- O SQL Server deve usar um ponto de extremidade de serviço de rede virtual
- O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual
- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB
- O Key Vault deve usar um ponto de extremidade de serviço de rede virtual
- Auditar o acesso irrestrito à rede para contas de armazenamento
- As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual
- \[Versão Prévia\]: O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual

## <a name="security-of-network-services"></a>Segurança de Serviços de Rede

Os serviços acordados fornecidos por um provedor/gerente de serviços de rede são formalmente gerenciados e monitorados para garantir que sejam fornecidos com segurança.

- \[Versão Prévia\]: o agente de coleta dos dados de tráfego de rede deve ser instalado nas máquinas virtuais do Windows
- \[Versão Prévia\]: o agente de coleta de dados de tráfego de rede deve ser instalado nas máquinas virtuais do Linux. O Observador de Rede deve ser habilitado

## <a name="information-exchange-policies-and-procedures"></a>Procedimentos e políticas de troca de informações

A organização limita o uso de mídias de armazenamento portátil controlados por indivíduos autorizados em sistemas de informações externas.

- Garantir que o aplicativo Web tenha a opção 'Certificados de Cliente (Certificados de cliente de entrada)' definida como 'Ativado'
- O CORS não deve permitir que todos os recursos acessem seus aplicativos Web
- O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções
- O CORS não deve permitir que todos os recursos tenham acesso ao seu Aplicativo de API
- A depuração remota deve ser desativada para aplicativos Web
- A depuração remota deve ser desativada para o aplicativos de funções
- A depuração remota deve ser desligada para aplicativos de API

## <a name="on-line-transactions"></a>Transações online

A organização requer de cada uma das partes envolvidas na transação o uso de criptografia e o uso de assinaturas eletrônicas. A organização garante que o armazenamento dos detalhes da transação esteja localizado fora de qualquer ambiente acessível publicamente (por exemplo, em uma plataforma de armazenamento existente na intranet da organização) e não mantido e exposto em um meio de armazenamento diretamente acessível pela Internet, em que uma autoridade confiável é usada (por exemplo, para a emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e inserida em todo o processo de gerenciamento de certificados/assinaturas de ponta a ponta.

- A transferência segura para contas de armazenamento deve ser habilitada
- A última versão do TLS deve ser usada no aplicativo de API
- A última versão do TLS deve ser usada no aplicativo Web
- A última versão do TLS deve ser usada no aplicativo de funções
- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- \[Versão Prévia\]: Implantar pré-requisitos para auditar as VMs do Windows que não contêm os certificados especificados na Raiz Confiável
- \[Versão Prévia\]: Mostrar os resultados da auditoria das VMs do Windows que não contêm os certificados especificados na Raiz Confiável

## <a name="user-password-management"></a>Gerenciamento de senha do usuário

As senhas são criptografadas durante a transmissão e o armazenamento em todos os componentes do sistema.

- \[Versão Prévia\] Auditar VMs com configurações de segurança de senha não seguras

## <a name="user-authentication-for-external-connections"></a>Autenticação de usuário para conexões externas

Métodos de autenticação fortes, como multifator, Radius ou Kerberos (para acesso privilegiado) e CHAP (para criptografia de credenciais para métodos de conexão discada) são implementados para todas as conexões externas com a rede da organização.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de gravação em sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="user-identification-and-authentication"></a>Identificação e autenticação do usuário

Os usuários que executam funções privilegiadas (por exemplo, administração do sistema) usam contas separadas ao executar essas funções privilegiadas. Os métodos de autenticação multifator são usados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de gravação em sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Mostrar os resultados da auditoria das VMs do Windows em que o grupo Administradores não contém todos os membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém somente os membros especificados
- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores não contém somente os membros especificados

## <a name="privilege-management"></a>Gerenciamento de privilégios

O acesso a funções de gerenciamento ou consoles administrativos para sistemas que hospedam sistemas virtualizados é restrito ao pessoal com base no princípio de privilégios mínimos e com suporte por meio de controles técnicos.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais
- Portas de gerenciamento devem ser fechadas nas máquinas virtuais
- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- Auditar o uso de regras personalizadas do RBAC
- O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes

## <a name="review-of-user-access-rights"></a>Revisão dos direitos de acesso do usuário

A organização mantém uma lista documentada de usuários autorizados a ativos de informações.

- Auditar o uso de regras personalizadas do RBAC

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Diagnóstico remoto e proteção da porta de configuração

Portas, serviços e aplicativos semelhantes instalados em um computador ou em sistemas de rede e que não são especificamente necessários para a funcionalidade comercial, são desabilitados ou removidos.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais
- Portas de gerenciamento devem ser fechadas nas máquinas virtuais
- A depuração remota deve ser desativada para aplicativos Web
- A depuração remota deve ser desativada para o aplicativos de funções
- A depuração remota deve ser desligada para aplicativos de API
- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="audit-logging"></a>Log de auditoria

Os logs de mensagens enviadas e recebidas são mantidos, incluindo a data, a hora, a origem e o destino da mensagem, mas não seu conteúdo. A auditoria está sempre disponível enquanto o sistema está ativo e rastreia eventos-chave, acesso a dados com êxito/falha, alterações de configuração de segurança do sistema, uso privilegiado ou de utilitário, alarmes gerados, ativação e desativação de sistemas de proteção (por exemplo, A/V e IDS), ativação e desativação de mecanismos de identificação e autenticação e criação e exclusão de objetos de nível de sistema.

- Os logs de diagnóstico no Azure Data Lake Storage devem ser habilitados
- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados 
- Os logs de diagnóstico no Hub IoT devem ser habilitados 
- Os logs de diagnóstico em contas do Lote devem ser habilitados 
- Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados 
- Os logs de diagnóstico no Hub de eventos devem ser habilitados 
- Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados 
- Os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados 
- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados 
- Os logs de diagnóstico no Key Vault deve estar habilitados 
- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados
- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados
- A auditoria no SQL Server deve ser habilitada
- Configuração de diagnóstico de auditoria
- O Azure Monitor deve coletar os logs de atividades de todas as regiões

## <a name="monitoring-system-use"></a>Uso do sistema de monitoramento

Os sistemas automatizados implantados em todo o ambiente da organização são usados para monitorar eventos importantes e atividades anormais, além de analisar logs do sistema, cujos resultados são examinados regularmente. O monitoramento inclui operações privilegiadas, acesso autorizado ou tentativas de acesso não autorizadas, incluindo tentativas de acessar contas desativadas e alertas ou falhas do sistema.

- O Azure Monitor deve coletar os logs de atividades de todas as regiões
- O agente do Log Analytics deve ser instalado nas máquinas virtuais
- O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais
- \[Versão Prévia\]: Implantar pré-requisitos para auditar as VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado
- \[Versão Prévia\]: Mostrar os resultados da auditoria das VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado
- O perfil de log do Azure Monitor deve coletar logs para as categorias 'gravar', 'excluir' e 'ação'
- O provisionamento automático do agente de monitoramento do Log Analytics deve estar habilitado na assinatura

## <a name="segregation-of-duties"></a>Diferenciação de tarefas

A separação de tarefas é usada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas. Nenhuma pessoa pode acessar, modificar ou usar sistemas de informações sem autorização ou detecção. O acesso para indivíduos responsáveis por administrar e acessar controles é limitado ao mínimo necessário, com base na função e nas responsabilidades de cada usuário e esses indivíduos não podem acessar funções de auditoria relacionadas a esses controles.

- O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes
- Auditar o uso de regras personalizadas do RBAC
- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Atribuição de Direitos do Usuário'
- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em “Atribuição de Direitos do Usuário”
- \[Versão Prévia\]: Implantar pré-requisitos para auditar configurações de VMs do Windows em “Opções de Segurança – Controle de Conta de Usuário”
- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em “Opções de Segurança – Controle de Conta de Usuário”
- Não devem existir funções personalizadas de proprietário de assinatura

## <a name="administrator-and-operator-logs"></a>Logs de administrador e operador

A organização garante que o registro em log apropriado esteja habilitado para auditar as atividades do administrador e revisa os logs do operador e do administrador do sistema regularmente.

- Um alerta do log de atividades deve existir para Operações administrativas específicas

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação de riscos relacionados a partes externas

As conexões de acesso remoto entre a organização e as partes externas são criptografadas

- A transferência segura para contas de armazenamento deve ser habilitada
- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL
- Apenas conexões seguras com o Cache Redis devem ser habilitadas

## <a name="business-continuity-and-risk-assessment"></a>Continuidade dos negócios e avaliação de riscos

A organização identifica os processos críticos do negócio e integra os requisitos de gerenciamento de segurança de informações da continuidade de negócios com outros requisitos de continuidade relacionados a esses aspectos como operações, pessoal, materiais, transporte e instalações.

- Auditar máquinas virtuais sem a recuperação de desastre configurada
- Os objetos do Key Vault devem ser recuperáveis
- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em “Opções de Segurança – Console de Recuperação”
- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Opções de Segurança – Console de recuperação"

## <a name="back-up"></a>Fazer backup

Esse blueprint atribui definições do Azure Policy que auditam as informações de backup do sistema para o site de armazenamento alternativo eletronicamente. Para a remessa física de metadados de armazenamento, considere o uso do Azure Data Box.

- O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB
- O Backup do Azure deve ser habilitado para máquinas virtuais

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais. 

## <a name="next-steps"></a>Próximas etapas

Você examinou o mapeamento de controles da amostra de blueprint de HIPAA/HITRUST. A seguir, acesse os seguintes artigos para saber mais sobre a visão geral e como implantar essa amostra:

> [!div class="next step action"]
> [Blueprint de HIPAA/HITRUST – Visão geral](./control-mapping.md)
> [Blueprint de HIPAA/HITRUST – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
