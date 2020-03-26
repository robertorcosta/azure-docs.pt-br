---
title: Controles da amostra de blueprint de HIPAA/HITRUST
description: Mapeamento do controle de amostras de blueprint de HIPAA/HITRUST. Cada controle é mapeado para uma ou mais Políticas do Azure que auxiliam na avaliação.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75470741"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapeamento do controle de amostra de blueprint de HIPAA/HITRUST

O artigo a seguir fornece detalhes de como a amostra de blueprint de HIPAA/HITRUST do Azure Blueprints é mapeada para os controles do HIPAA/HITRUST. Para obter mais informações sobre os controles, confira [HIPAA/HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Os mapeamentos a seguir são para os controles de **HIPAA/HITRUST**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: inciativa de política interna Auditoria de controles do HIPAA/HITRUST**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência 1:1 ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controle contra código mal-intencionado

Este blueprint ajuda você a gerenciar a proteção de ponto de extremidade, incluindo a proteção contra código mal-intencionado, por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente em máquinas virtuais na Central de Segurança do Azure e impõem a solução Microsoft Antimalware em máquinas virtuais do Windows.

- Implantar a extensão padrão Antimalware de IaaS da Microsoft para Windows Server
- Os logs de diagnóstico em contas do Lote devem ser habilitados
- As atualizações do sistema devem ser instaladas em suas máquinas


## <a name="management-of-removable-media"></a>Gerenciamento de mídia removível

A organização, com base no nível de classificação de dados, registra mídias (incluindo laptops) antes do uso, coloca restrições cabíveis na maneira como essas mídias devem ser usadas e fornece um nível apropriado de proteção física e lógica (incluindo criptografia) para mídia que contenha informações sigilosas até que seja corretamente destruída ou limpa.

- Exigir a criptografia em contas do Data Lake Storage
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- A criptografia de disco deve ser aplicada em máquinas virtuais
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada


## <a name="information-exchange-policies-and-procedures"></a>Procedimentos e políticas de troca de informações

Os provedores de serviço de nuvem usam uma plataforma de virtualização reconhecida pela indústria e formatos de virtualização padrão (por exemplo, Open Virtualization Format, OVF) para ajudar a garantir a interoperabilidade e mantém documentadas as alterações personalizadas feitas em qualquer hipervisor em uso e todos os ganchos de virtualização específicos da solução disponíveis para revisão do cliente.

- Implantar pré-requisitos para auditar VMs do Windows que não têm os aplicativos especificados instalados

## <a name="control-of-operational-software"></a>Controle de software operacional 

A organização identifica software não autorizado no sistema de informações, incluindo servidores, estações de trabalho e laptops, emprega uma política de permitir tudo, negar por exceção para proibir a execução de software não autorizado conhecido no sistema de informações e revisa e atualiza a lista de softwares não autorizados com periodicidade não menor que anualmente.

- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Opções de Segurança – Auditoria"
- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Políticas de Auditoria do Sistema – Gerenciamento de Contas"

## <a name="change-control-procedures"></a>Procedimentos de controle de alterações

A integridade de todas as imagens de máquina virtual é garantida o tempo todo pelo registro em log e geração de um alerta para todas as alterações feitas em imagens de máquina virtual, disponibilizando para os proprietários de negócios e/ou clientes, por meio de métodos eletrônicos (por exemplo, portais ou alertas), os resultados de uma alteração ou movimentação e a validação subsequente da integridade da imagem.

- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Políticas de Auditoria do Sistema – Acompanhamento Detalhado"

## <a name="inventory-of-assets"></a>Inventário de ativos 

Um inventário de ativos e serviços é mantido

- Os logs de diagnóstico nos serviços de Pesquisa devem ser habilitados.
- \[Versão Prévia\] Implantar requisitos para auditar as configurações de VMs do Windows em "Opções de Segurança – Servidor de Rede da Microsoft"
- \[Versão Prévia\] Implantar requisitos para auditar as configurações de VMs do Windows em "Modelos Administrativos – Rede"

## <a name="control-of-technical-vulnerabilities"></a>Controle de vulnerabilidades técnicas 

Existe um padrão de configuração reforçado para todos os componentes de sistema e de rede.

- Auditar Máquinas Virtuais sem a recuperação de desastre configurada
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- A vulnerabilidade deve ser corrigida por uma solução de Avaliação de Vulnerabilidades

## <a name="segregation-in-networks"></a>Diferenciação em redes

Os gateways de segurança da organização (por exemplo, firewalls) impõem políticas de segurança e são configurados para filtrar o tráfego entre domínios, bloquear acesso não autorizado e são usados para manter a diferenciação entre segmentos de rede internos com fio, internos sem fio e externos (por exemplo, a Internet), incluindo DMZs e imposição de políticas de controle de acesso para cada um dos domínios.

- Provisionamento automático do agente de monitoramento de segurança
- Implantar o observador de rede quando redes virtuais são criadas

## <a name="input-data-validation"></a>Validação de dados de entrada

Para todos os aplicativos Web voltados para o público, os firewalls de nível de aplicativo são implementados para controlar tráfego. Para aplicativos voltados para o público que não são baseados na Web, a organização implementou um firewall baseado em rede específico ao tipo de aplicativo. Se o tráfego para o aplicativo voltado ao público for criptografado, o dispositivo ficará atrás da criptografia ou será capaz de descriptografar o tráfego antes da análise.

- \[Versão Prévia\] Mostrar resultados de auditoria da configuração de VMs do Windows em "Propriedades do Firewall do Windows"


## <a name="network-connection-control"></a>Controle de conexão de rede

O tráfego de rede é controlado de acordo com a política de controle de acesso da organização por meio do firewall e de outras restrições relacionadas à rede para cada ponto de acesso à rede ou interface gerenciada do serviço de telecomunicação externo.

- O acesso por pontos de extremidade voltados para a Internet deve ser restrito
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- A depuração remota deve ser desligada para aplicativos de API

## <a name="network-controls"></a>Controles de rede

A organização usa canais de comunicação seguros e criptografados ao migrar servidores físicos, aplicativos ou dados para servidores virtualizados.

- A criptografia de disco deve ser aplicada em VMs
- O protetor TDE do SQL Server deve ser criptografado com sua própria chave
- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Opções de Segurança – Acesso à Rede"
- Auditar o acesso irrestrito à rede para contas de armazenamento
- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Propriedades do Firewall do Windows"
- Implantar configurações de diagnóstico de Grupos de segurança de rede
- O acesso por pontos de extremidade voltados para a Internet deve ser restrito

## <a name="sensitive-system-isolation"></a>Isolamento de sistema confidencial

Recursos compartilhados do sistema (por exemplo, registros, memória principal, armazenamento secundário) são liberados de volta para o sistema, protegidos contra a divulgação para outros sistemas/aplicativos/usuários e os usuários não podem acessar vestígios de informações intencionalmente ou não intencionalmente.

- As Máquinas Virtuais devem ser migradas para os novos recursos do Azure Resource Manager

## <a name="security-of-network-services"></a>Segurança de Serviços de Rede

Os serviços acordados fornecidos por um provedor/gerente de serviços de rede são formalmente gerenciados e monitorados para garantir que sejam fornecidos com segurança.

- As Máquinas Virtuais devem ser migradas para os novos recursos do Azure Resource Manager

## <a name="network-routing-control"></a>Controle de roteamento de rede

Os controles de roteamento são implementados por meio de gateways de segurança (por exemplo, firewalls) usados entre redes internas e externas (por exemplo, a Internet e redes de terceiros).

- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="information-exchange-policies-and-procedures"></a>Procedimentos e políticas de troca de informações

A organização limita o uso de mídias de armazenamento portátil controlados por indivíduos autorizados em sistemas de informações externas.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- A depuração remota deve ser desativada para aplicativos Web
- O aplicativo de API só deve estar acessível via HTTPS

## <a name="electronic-messaging"></a>Mensagens eletrônicas

As aprovações são obtidas antes do uso de serviços públicos externos, incluindo mensagens instantâneas ou compartilhamento de arquivos.

- \[Versão Prévia\] Mostrar resultados de auditoria das VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644

## <a name="on-line-transactions"></a>Transações online

A organização requer de cada uma das partes envolvidas na transação o uso de criptografia e o uso de assinaturas eletrônicas. A organização garante que o armazenamento dos detalhes da transação esteja localizado fora de qualquer ambiente acessível publicamente (por exemplo, em uma plataforma de armazenamento existente na intranet da organização) e não mantido e exposto em um meio de armazenamento diretamente acessível pela Internet, em que uma autoridade confiável é usada (por exemplo, para a emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e inserida em todo o processo de gerenciamento de certificados/assinaturas de ponta a ponta.

- A criptografia de disco deve ser aplicada em VMs
- \[Versão Prévia\] Mostrar os resultados de auditoria das VMs do Windows que não contêm os certificados especificados na raiz confiável

## <a name="password-management"></a>Gerenciamento de senha

As senhas são criptografadas durante a transmissão e o armazenamento em todos os componentes do sistema.

- \[Versão Prévia\] Mostrar os resultados da auditoria das VMs do Windows que não têm a configuração de complexidade de senha habilitada

## <a name="user-authentication-for-external-connections"></a>Autenticação de usuário para conexões externas

Métodos de autenticação fortes, como multifator, Radius ou Kerberos (para acesso privilegiado) e CHAP (para criptografia de credenciais para métodos de conexão discada) são implementados para todas as conexões externas com a rede da organização.

- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O controle de acesso à rede just-in-time deve ser aplicado em VMs

## <a name="user-identification-and-authentication"></a>Identificação e autenticação do usuário

Os usuários que executam funções privilegiadas (por exemplo, administração do sistema) usam contas separadas ao executar essas funções privilegiadas. Os métodos de autenticação multifator são usados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O controle de acesso à rede just-in-time deve ser aplicado em VMs

## <a name="privilege-management"></a>Gerenciamento de privilégios

O acesso a funções de gerenciamento ou consoles administrativos para sistemas que hospedam sistemas virtualizados é restrito ao pessoal com base no princípio de privilégios mínimos e com suporte por meio de controles técnicos.

- O controle de acesso à rede just-in-time deve ser aplicado em VMs
- \[Versão Prévia\] O RBAC (Controle de Acesso Baseado em Função) deve ser usado nos serviços do Kubernetes

## <a name="review-of-user-access-rights"></a>Revisão dos direitos de acesso do usuário

A organização mantém uma lista documentada de usuários autorizados a ativos de informações.

- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Opções de Segurança – Contas"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Diagnóstico remoto e proteção da porta de configuração

Portas, serviços e aplicativos semelhantes instalados em um computador ou em sistemas de rede e que não são especificamente necessários para a funcionalidade comercial, são desabilitados ou removidos.

- Portas de gerenciamento devem ser fechadas nas máquinas virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas

## <a name="audit-logging"></a>Log de auditoria

Os logs de mensagens enviadas e recebidas são mantidos, incluindo a data, a hora, a origem e o destino da mensagem, mas não seu conteúdo. A auditoria está sempre disponível enquanto o sistema está ativo e rastreia eventos-chave, acesso a dados com êxito/falha, alterações de configuração de segurança do sistema, uso privilegiado ou de utilitário, alarmes gerados, ativação e desativação de sistemas de proteção (por exemplo, A/V e IDS), ativação e desativação de mecanismos de identificação e autenticação e criação e exclusão de objetos de nível de sistema.

- Os logs de diagnóstico no Hub de eventos devem ser habilitados
- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas

## <a name="monitoring-system-use"></a>Uso do sistema de monitoramento

Os sistemas automatizados implantados em todo o ambiente da organização são usados para monitorar eventos importantes e atividades anormais, além de analisar logs do sistema, cujos resultados são examinados regularmente. O monitoramento inclui operações privilegiadas, acesso autorizado ou tentativas de acesso não autorizadas, incluindo tentativas de acessar contas desativadas e alertas ou falhas do sistema.

- Os logs de diagnóstico nos Conjuntos de dimensionamento de máquinas virtuais devem ser habilitados

## <a name="segregation-of-duties"></a>Diferenciação de tarefas

A separação de tarefas é usada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas. Nenhuma pessoa pode acessar, modificar ou usar sistemas de informações sem autorização ou detecção. O acesso para indivíduos responsáveis por administrar e acessar controles é limitado ao mínimo necessário, com base na função e nas responsabilidades de cada usuário e esses indivíduos não podem acessar funções de auditoria relacionadas a esses controles.

- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura

## <a name="administrator-and-operator-logs"></a>Logs de administrador e operador

A organização garante que o registro em log apropriado esteja habilitado para auditar as atividades do administrador e revisa os logs do operador e do administrador do sistema regularmente.

- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação de riscos relacionados a partes externas

As conexões de acesso remoto entre a organização e as partes externas são criptografadas

- A criptografia de disco deve ser aplicada em Máquinas virtuais

## <a name="business-continuity-and-risk-assessment"></a>Continuidade dos negócios e avaliação de riscos

A organização identifica os processos críticos do negócio e integra os requisitos de gerenciamento de segurança de informações da continuidade de negócios com outros requisitos de continuidade relacionados a esses aspectos como operações, pessoal, materiais, transporte e instalações.

- \[Versão Prévia\] Mostrar resultados de auditoria das configurações de VMs do Windows em "Opções de Segurança – Console de recuperação"

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
