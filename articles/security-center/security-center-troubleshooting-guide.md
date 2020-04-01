---
title: Guia de solução de problemas da Central de Segurança do Azure | Microsoft Docs
description: Este guia é para profissionais de TI, analistas de segurança e administradores de nuvem que precisam resolver problemas relacionados ao Azure Security Center.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: b5a85f8ae1564d724b826c809261e56577f4783a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435540"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guia de solução de problemas da Central de Segurança do Azure

Este guia é para os profissionais de TI (tecnologia da informação), analistas de segurança de informações e administradores de nuvem cujas organizações estão usando a Central de Segurança do Azure e precisam solucionar os problemas relacionados a Central de Segurança.

O Security Center usa o agente Log Analytics para coletar e armazenar dados. Veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md) para saber mais. As informações deste artigo representam a funcionalidade do Security Center após a transição para o agente Log Analytics.

## <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Este guia explica como solucionar os problemas relacionados à Central de Segurança.

Tipos de alerta:

* Análise de Comportamento da Máquina Virtual (VMBA)
* Análise de Rede
* Análise do Banco de Dados SQL e SQL Data Warehouse
* Informações Contextuais

Dependendo dos tipos de alerta, é possível que os clientes tenham acesso às informações necessárias para investigar o alerta usando os seguintes recursos:

* Logs de segurança no visualizador de eventos da máquina Virtual (VM) no Windows
* AuditD no Linux
* Os logs de atividades do Azure e habilitar logs de diagnóstico no recurso do ataque.

Os clientes podem compartilhar comentários sobre a descrição e a relevância do alerta. Navegue até o alerta, marque o botão **Isso foi útil?**, escolha o motivo e, em seguida, insira um comentário para explicá-lo. Monitoramos consistentemente esse canal de comentários para melhorar nossos alertas.

## <a name="audit-log"></a>Log de auditoria

A maioria das soluções de problemas na Central de Segurança ocorre por meio do exame inicial dos registros do [Log de Auditoria](../azure-monitor/platform/platform-logs-overview.md) em busca do componente com falha. Com os logs de auditoria, você pode determinar:

* Quais operações ocorreram
* Quem iniciou a operação
* Quando a operação ocorreu
* O status da operação
* Os valores de outras propriedades que podem ajudar você a pesquisar a operação

O log de auditoria contém todas as operações de gravação (PUT, POST, DELETE) realizadas em seus recursos, mas não inclui operações de leitura (GET).

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O Security Center usa o agente Log Analytics – este é o mesmo agente usado pelo serviço Azure Monitor – para coletar dados de segurança de suas máquinas virtuais Do Azure. Após a coleta de dados estar habilitada e o agente estar instalado corretamente no computador de destino, os processos abaixo deverão estar em execução:

* HealthService.exe

Se você abrir o console de gerenciamento de serviços (services.msc), você também verá o serviço de agente log analytics em execução como mostrado abaixo:

![Serviços](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Para ver qual versão do agente você tem, abra o **Gerenciador de tarefas,** na guia **Processos,** localize o **Serviço de agente log analytics,** clique com o botão direito do mouse nele e clique em **Propriedades**. Na guia **Detalhes**, procure a versão do arquivo como mostrado abaixo:

![Arquivo](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Cenários de instalação do agente Log Analytics

Existem dois cenários de instalação que podem produzir resultados diferentes ao instalar o agente Log Analytics em seu computador. Os cenários compatíveis são:

* **Agente instalado automaticamente pela Central de Segurança**: neste cenário você poderá exibir os alertas em Locais, na Central de Segurança e na Pesquisa de Logs. Você receberá notificações de e-mail para o endereço de e-mail configurado na política de segurança para a assinatura a que o recurso pertence.

* **Agente instalado manualmente em uma VM localizada no Azure**: neste cenário, se você estiver usando agentes baixados e instalados manualmente antes de fevereiro de 2017, você pode visualizar os alertas no portal do Security Center somente se você filtrar na assinatura a que o espaço de trabalho pertence. Se você filtrar a assinatura a que o recurso pertence, você não verá nenhum alerta. Você receberá notificações de e-mail para o endereço de e-mail configurado na política de segurança para a assinatura a que o espaço de trabalho pertence.

> [!NOTE]
> Para evitar o comportamento explicado no segundo cenário, baixe a versão mais recente do agente.

## <a name="monitoring-agent-health-issues"></a>Problemas de saúde de agentes de monitoramento<a name="mon-agent"></a>

O **estado de monitoramento** define o motivo pelo qual a Central de Segurança não consegue monitorar as VMs e os computadores inicializados para o provisionamento automático. A tabela a seguir mostra os valores do **estado de monitoramento**, descrições e as etapas de resolução.

| Estado do monitoramento | Descrição | Etapas de resolução |
|---|---|---|
| Instalação do agente pendente | A instalação do agente Log Analytics ainda está em execução.  A instalação pode demorar algumas horas. | Aguarde até que a instalação automática seja concluída. |
| Estado de energia desativado | A máquina virtual está parada.  O agente Log Analytics só pode ser instalado em uma VM que está sendo executado. | Reinicie a VM. |
| Agente de VM do Azure ausente ou inválido | O agente Log Analytics ainda não está instalado.  Para a Central de Segurança instalar a extensão, é necessário um agente válido da VM do Azure. | Instale, reinstale ou atualize o agente de VM do Azure na máquina virtual. |
| O estado da máquina virtual não está pronto para instalação  | O agente Log Analytics ainda não está instalado porque a VM não está pronta para instalação. A máquina virtual não está pronta para a instalação devido a um problema com o agente de VM ou o provisionamento de VM. | Verifique o status da máquina virtual. Volte para as **máquinas virtuais** no portal e selecione a máquina virtual para ver as informações de status. |
|Falha na instalação - erro geral | O agente Log Analytics foi instalado, mas falhou devido a um erro. | [Instale manualmente a extensão](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) ou desinstale a extensão para que a Central de Segurança tente instalar novamente. |
| A instalação falhou - o agente local já está instalado | Falha na instalação do agente log analytics. O Security Center identificou um agente local (Log Analytics ou System Center Operations Manager) já instalado na VM. Para evitar a configuração de vários lugares, onde a VM está reportando a dois espaços de trabalho separados, a instalação do agente Log Analytics parou. | Há duas maneiras de resolver: [instalar manualmente a extensão](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) e conectá-la ao workspace desejado. Outra opção é definir o workspace desejado como o workspace padrão e habilitar o provisionamento automático do agente.  Veja [habilitar o provisionamento automático](security-center-enable-data-collection.md). |
| O agente não consegue se conectar ao workspace | O agente Log Analytics instalado, mas falhou devido à conectividade de rede.  Verifique se há acesso à Internet ou se um proxy HTTP válido foi configurado para o agente. | Confira Monitoramento de requisitos de rede do agente. |
| Agente conectado a um workspace ausente ou desconhecido | O Security Center identificou que o agente log analytics instalado na VM está conectado a um espaço de trabalho ao qual ele não tem acesso. | Isso pode acontecer em dois casos. O workspace foi excluído e não existe mais. Reinstale o agente com o workspace correto ou desinstale o agente e permita que a Central de Segurança conclua a instalação do provisionamento automático. O segundo caso acontece quando o workspace faz parte de uma assinatura à qual a Central de Segurança não tem permissões. A Central de Segurança requer assinaturas para permitir que o Microsoft Security Resource Provider os acesse. Para habilitar, registre a assinatura no Microsoft Security Resource Provider. Isso pode ser feito pela API, PowerShell, portal ou simplesmente filtrando-se a assinatura no painel **Visão geral** da Central de Segurança. Para saber mais, veja [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| O agente não responde ou sua ID está ausente | A Central de Segurança não consegue recuperar os dados de segurança obtidos da máquina virtual, mesmo com o agente instalado. | O agente não está relatando dado algum, incluindo a pulsação. O agente pode estar danificado ou algo está bloqueando o tráfego. Ou, o agente está relatando dados, mas está faltando um ID de recurso do Azure, então é impossível combinar os dados com a VM do Azure. Para solucionar problemas do Linux, consulte [Guia de Solução de Problemas para o Agente do Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Para solucionar problemas do Windows, consulte [Solucionar problemas de Máquinas Virtuais do Windows](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agente não instalado | A coleta de dados está desabilitada. | Ative a coleta de dados na política de segurança ou instale manualmente o agente Log Analytics. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Solução de problemas que monitoram os requisitos da rede dos agentes<a name="mon-network-req"></a>

Para que os agentes se conectem e se registrem na Central de Segurança, eles deverão ter acesso aos recursos de rede, incluindo os números de porta e as URLs de domínio.

* Para servidores proxy, você precisa garantir que os recursos do servidor proxy apropriados estejam configurados nas configurações do agente. Leia este artigo para saber mais sobre [como alterar as configurações de proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
* Para os firewalls que restringem o acesso à Internet, será necessário configurar o firewall para permitir o acesso ao Log Analytics. Nenhuma ação é necessária nas configurações do agente.

A tabela a seguir mostra os recursos necessários para comunicação.

| Recurso de agente | Portas | Ignorar a inspeção de HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sim |
| *.oms.opinsights.azure.com | 443 | Sim |
| *.blob.core.windows.net | 443 | Sim |
| *.azure-automation.net | 443 | Sim |

Se você encontrar problemas de integração com o agente, leia o artigo [Como solucionar problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Solução de problemas do Endpoint Protection que não está funcionando corretamente

O agente convidado é o processo pai de tudo o que a extensão [Antimalware da Microsoft](../security/fundamentals/antimalware.md) faz. Quando o processo do agente convidado falha, o Antimalware da Microsoft, que é executado como um processo filho do agente convidado, também pode falhar.  Em cenários como esse é recomendável verificar as seguintes opções:

* Se a VM de destino é uma imagem personalizada e o criador da VM nunca instalou o agente convidado.
* Se o destino for uma VM do Linux em vez de uma VM do Windows, instalar a versão do Windows da extensão antimalware em uma VM do Linux falhará. O agente convidado do Linux tem requisitos específicos em termos de versão do sistema operacional e pacotes necessários e, se esses requisitos não forem atendidos, o agente de VM também não funcionará lá.
* Se a VM foi criada com uma versão antiga do agente convidado. Se foi, esteja ciente de que alguns agentes antigos podem não realizar a própria atualização automática para a versão mais recente e isso pode causar esse problema. Sempre use a versão mais recente do agente convidado se estiver criando suas próprias imagens.
* Alguns softwares de administração de terceiros podem desabilitar o agente convidado ou bloquear o acesso a determinados locais de arquivo. Se você tiver software de terceiros instalado em sua VM, verifique se o agente está na lista de exclusões.
* Determinadas configurações de firewall ou o NSG (Grupo de Segurança de Rede) podem bloquear o tráfego de rede de/para o agente convidado.
* Algumas ACLs (listas de controle de acesso) podem impedir o acesso ao disco.
* Falta de espaço em disco pode impedir que o agente convidado funcione corretamente.

Por padrão, a Interface do Usuário do Antimalware da Microsoft está desabilitada. Leia [Habilitando a Interface de Usuário do Antimalware da Microsoft em VMs do Azure Resource Manager após a implantação](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) para obter mais informações sobre como habilitá-la, se você precisar.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Problemas ao carregar o painel de solução de problemas

Se você enfrentar problemas ao carregar o painel central de segurança, certifique-se de que o usuário que registra a inscrição para a Central de segurança (ou seja, o primeiro usuário de um que abrir a Central de segurança com a assinatura) e o usuário que deseja ativar a coleta de dados deve ser *proprietário* ou *Colaborador* na assinatura. A partir desse momento também usuários com *leitor* na assinatura, pode ver a painel de controle/alertas/recomendação/política.

## <a name="contacting-microsoft-support"></a>Entrando em contato com o Suporte da Microsoft

Alguns problemas podem ser identificados usando as diretrizes fornecidas neste artigo. Outros, você também pode encontrar documentados no [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) público da Central de Segurança. No entanto, se você precisar de mais solução de problemas, você pode abrir uma nova solicitação de suporte usando **o portal Azure,** conforme mostrado abaixo:

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Confira também

Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Guia de Operações e Planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) - saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Monitoramento de segurança no Azure Security Center](security-center-monitoring.md) — Saiba como monitorar a saúde dos seus recursos do Azure
* [Gerenciamento e resposta a alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](security-center-alerts-type.md)
* [Tutorial: Responder a incidentes de segurança](tutorial-security-incident.md)
* [Validação de alertas na Central de Segurança do Azure](security-center-alert-validation.md)
* [Notificações de Email na Central de Segurança do Azure](security-center-provide-security-contact-details.md)
* [Tratando Incidentes de Segurança na Central de Segurança do Azure](security-center-incident.md)
* [Investigar os incidentes e alertas na Central de Segurança do Azure](security-center-investigation.md)
* [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md)
* [Monitoramento de soluções de parceiros com o Azure Security Center](security-center-partner-solutions.md) — Saiba como monitorar o estado de saúde das soluções de seus parceiros.
* [Faq do Azure Security Center](faq-general.md) — Encontre perguntas frequentes sobre o uso do serviço
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) — Encontre posts no blog sobre segurança e conformidade do Azure
