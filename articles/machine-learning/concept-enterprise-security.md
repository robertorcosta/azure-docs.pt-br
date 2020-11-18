---
title: Segurança e governança corporativas
titleSuffix: Azure Machine Learning
description: 'Use o Azure Machine Learning com segurança: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: a9ad018980784a1f809ad28a77dacf9f0328fffa
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873889"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Segurança e governança corporativas para Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis para o Azure Machine Learning.

Ao usar um serviço de nuvem, um tipo de melhor prática é restringir o acesso somente aos usuários que precisam dele. Comece entendendo o modelo de autenticação e autorização usado pelo serviço. É possível que você também queira restringir o acesso à rede ou unir recursos de modo seguro em sua rede local com a nuvem. A criptografia de dados também é vital, tanto em repouso quanto durante o tráfego de dados entre os serviços. Talvez você também queira criar políticas para impor determinadas configurações ou log quando configurações sem conformidade forem criadas. Por fim, você precisa ser capaz de monitorar o serviço e produzir um log de auditoria de todas as atividades.

> [!NOTE]
> As informações presentes neste artigo funcionam com o SDK do Python do Azure Machine Learning versão 1.0.83.1 ou superior.

## <a name="authentication--authorization"></a>Autorização de & de autenticação

A maior parte da autenticação para Azure Machine Learning recursos usa o Azure Active Directory (AD do Azure) para autenticação e o RBAC do Azure (controle de acesso baseado em função) para autorização. As exceções a isso são:

* __SSH__: você pode habilitar o acesso SSH a alguns recursos de computação, como Azure Machine Learning instância de computação. O acesso SSH usa a autenticação baseada em chave. Para obter mais informações sobre como criar chaves SSH, consulte [criar e gerenciar chaves SSH](../virtual-machines/linux/create-ssh-keys-detailed.md). Para obter informações sobre como habilitar o acesso SSH, consulte [criar e gerenciar Azure Machine Learning instância de computação](how-to-create-manage-compute-instance.md).
* __Modelos implantados como serviços Web__: as implantações de serviço Web podem usar o controle de acesso baseado em __token__ ou __chave__ . As chaves são cadeias de caracteres estáticas. Os tokens são recuperados usando uma conta do Azure AD. Para obter mais informações, consulte [Configurar a autenticação para modelos implantados como um serviço Web](how-to-authenticate-web-service.md).

Serviços específicos dos quais Azure Machine Learning depende, como os serviços de armazenamento de dados do Azure, têm seus próprios métodos de autenticação e autorização. Para obter mais informações sobre a autenticação dos serviços de armazenamento, consulte [conectar-se aos serviços de armazenamento](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A autenticação multifator terá suporte se o Azure AD (Azure Active Directory) estiver configurado para usá-la. Este é o processo de autenticação:

1. O cliente entra no Azure AD e obtém um token do Azure Resource Manager.  Há suporte total para os usuários e para as entidades de serviço.
1. O cliente apresenta o token para o Azure Resource Manager e para todos os Azure Machine Learning.
1. O serviço do Machine Learning fornece um token de serviço do Machine Learning para o destino de computação do usuário (por exemplo, Computação do Machine Learning). Esse token é usado pelo destino de computação do usuário para retornar ao serviço do Machine Learning depois de a execução ter sido concluída. O escopo é limitado ao workspace.

[![Autenticação no Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Para obter mais informações, consulte [autenticação para Azure Machine Learning espaço de trabalho](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>RBAC do Azure

Você pode criar vários workspaces, e cada workspace pode ser compartilhado por várias pessoas. Você pode controlar quais recursos ou operações do espaço de trabalho os usuários podem acessar atribuindo sua conta do Azure AD às funções de RBAC do Azure. A seguir estão as funções internas:

* Proprietário
* Colaborador
* Leitor

Proprietários e colaboradores podem usar todos os destinos de computação e armazenamentos de dados anexados ao workspace.  

A tabela a seguir lista algumas das principais operações do Azure Machine Learning e as funções que podem executá-las:

| Operação do Azure Machine Learning | Proprietário | Colaborador | Leitor |
| ---- |:----:|:----:|:----:|
| Criar workspace | ✓ | ✓ | |
| Compartilhar o workspace | ✓ | |  |
| Criar destino de computação | ✓ | ✓ | |
| Anexar destino de computação | ✓ | ✓ | |
| Anexar armazenamento de dados | ✓ | ✓ | |
| Executar o experimento | ✓ | ✓ | |
| Exibir execuções/métricas | ✓ | ✓ | ✓ |
| Registrar modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implantar serviço Web | ✓ | ✓ | |
| Exibir modelos/imagens | ✓ | ✓ | ✓ |
| Chamar serviço Web | ✓ | ✓ | ✓ |

Caso as funções internas não atendam às suas necessidades, você poderá criar funções personalizadas. As funções personalizadas controlam todas as operações dentro de um espaço de trabalho, como a criação de uma computação, o envio de uma execução, o registro de um repositório de armazenamento ou a implantação de um modelo. As funções personalizadas podem ter permissões de leitura, gravação ou exclusão nos vários recursos de um espaço de trabalho, como clusters, repositórios de armazenamento, modelos e pontos de extremidade. Você pode tornar a função disponível em níveis específicos do workspace, do grupo de recursos ou da assinatura. Para obter mais informações, consulte [Gerenciar usuários e funções em um workspace de Azure Machine Learning](how-to-assign-roles.md).

Para obter mais informações sobre como usar o RBAC com kubernetes, consulte [controle de acesso do Azure Role-Based para autorização do kubernetes](../aks/manage-azure-rbac.md).

> [!IMPORTANT]
> Azure Machine Learning depende de outros serviços do Azure, como o armazenamento de BLOBs do Azure e os serviços Kubernetess do Azure. Cada serviço do Azure tem suas próprias configurações de RBAC do Azure. Para obter o nível desejado de controle de acesso, talvez seja necessário aplicar as configurações do RBAC do Azure para Azure Machine Learning e os serviços usados com Azure Machine Learning.

> [!WARNING]
> O Azure Machine Learning tem suporte com a colaboração entre empresas do Azure Active Directory, mas ainda não tem suporte com a colaboração entre consumidores do Azure Active Directory.

### <a name="managed-identities"></a>Identidades gerenciadas

Cada espaço de trabalho também tem uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) atribuída pelo sistema associada que tem o mesmo nome que o espaço de trabalho. A identidade gerenciada é usada para acessar com segurança os recursos usados pelo espaço de trabalho. Ele tem as seguintes permissões em recursos anexados:

| Recurso | Permissões |
| ----- | ----- |
| Workspace | Colaborador |
| Conta de armazenamento | Colaborador de dados de blob de armazenamento |
| Cofre de chaves | Acesso a todas as chaves, segredos, certificados |
| Registro de Contêiner do Azure | Colaborador |
| Grupo de recursos que contém os workspace | Colaborador |
| Grupo de recursos que contém o cofre de chaves (caso seja diferente daquele que contém o workspace) | Colaborador |

Não recomendamos que os administradores revoguem o acesso da identidade gerenciada dos recursos mencionados na tabela anterior. É possível restaurar o acesso usando a operação de ressincronização de chaves.

O Azure Machine Learning cria um aplicativo adicional (o nome começa com `aml-` ou `Microsoft-AzureML-Support-App-`) que tenha acesso no nível de colaborador em sua assinatura para cada região do workspace. Por exemplo, caso você tenha um workspace no Leste dos EUA e outro no Norte da Europa na mesma assinatura, verá dois desses aplicativos. Esses aplicativos habilitam o Azure Machine Learning a ajudar você a gerenciar recursos de computação.

Opcionalmente, você pode configurar suas próprias identidades gerenciadas para uso com máquinas virtuais do Azure e Azure Machine Learning cluster de computação. Com uma VM, a identidade gerenciada pode ser usada para acessar seu espaço de trabalho do SDK, em vez da conta do Azure AD do usuário individual. Com um cluster de computação, a identidade gerenciada é usada para acessar recursos, como repositórios de armazenamento protegidos que o usuário que está executando o trabalho de treinamento pode não ter acesso ao. Para obter mais informações, consulte [autenticação para Azure Machine Learning espaço de trabalho](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Segurança e isolamento de rede

Para restringir o acesso físico a recursos de Azure Machine Learning, você pode usar a VNet (rede virtual) do Azure. O VNets permite que você crie ambientes de rede que são parcialmente ou totalmente isolados da Internet pública. Isso reduz a superfície de ataque para sua solução, bem como as chances de vazamento de dados.

Para obter mais informações, consulte um dos seguintes documentos:

* [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)
* [Recursos de workspace seguro](how-to-secure-workspace-vnet.md)
* [Ambiente de treinamento seguro](how-to-secure-training-vnet.md)
* [Ambiente de inferência seguro](how-to-secure-inferencing-vnet.md)
* [Usar o estúdio em uma rede virtual segura](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Criptografia de dados

O Azure Machine Learning usa uma variedade de recursos de computação e armazenamentos de dados. Para saber mais sobre como cada um deles dá suporte à criptografia de dados em repouso e em trânsito, consulte [criptografia de dados com Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao usar serviços como o Machine Learning Automatizado, a Microsoft pode gerar dados temporários e pré-processados para treinar vários modelos. Esses dados são armazenados em um datastore em seu workspace, o que permite que você aplique controles de acesso e criptografia adequadamente.

Talvez também seja interessante criptografar [informações de diagnóstico registradas do seu ponto de extremidade implantado](how-to-enable-app-insights.md) em sua instância do Azure Application Insights.

## <a name="monitoring"></a>Monitoramento

Há vários cenários de monitoramento com Azure Machine Learning, dependendo da função e o que está sendo monitorado.

| Função | Monitorando para usar | Descrição |
| ---- | ----- | ----- |
| Admin, DevOps, MLOps | [Métricas de Azure monitor](#azure-monitor), [log de atividades](#activity-log), [verificação de vulnerabilidade](#vulnerability-scanning) | Informações de nível de serviço |
| Cientista de dados, MLOps | [Monitorar execuções](#monitor-runs) | Informações registradas durante execuções de treinamento |
| MLOps | [Coletar dados de modelo](how-to-enable-data-collection.md), [monitorar com Application insights](how-to-enable-app-insights.md) | Informações registradas em log por modelos implantados como serviços Web ou módulos de IoT Edge|

### <a name="monitor-runs"></a>Monitorar execuções

Você pode monitorar execuções de teste em Azure Machine Learning, incluindo informações de registro em log de seus scripts de treinamento. Essas informações podem ser exibidas por meio do SDK, CLI do Azure e Studio. Para obter mais informações, consulte os seguintes artigos:

* [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md)
* [Habilitar logs](how-to-track-experiments.md)
* [Exibir logs](how-to-monitor-view-training-logs.md)
* [Visualizar execuções com o TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Você pode usar as métricas do Azure Monitor para exibir e monitorar as métricas do seu workspace do Azure Machine Learning. No [portal do Azure](https://portal.azure.com), selecione seu workspace e depois **Métricas**:

[![Captura de tela mostrando métricas de exemplo de um workspace](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas contam com informações sobre execuções, implantações e registros.

Para obter mais informações, confira [Métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

### <a name="activity-log"></a>Log de atividades

Você pode exibir o log de atividades de um workspace para ver várias operações executadas nele. O log inclui informações básicas, como o nome da operação, o iniciador de eventos e o carimbo de data/hora.

Esta captura de tela mostra o log de atividades de um workspace:

[![Captura de tela mostrando o log de atividades de um workspace](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Os detalhes da solicitação de pontuação são armazenados no Application Insights. O Application Insights é criado em sua assinatura ao criar um workspace. As informações registradas incluem campos como:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Algumas ações no workspace do Azure Machine Learning não registram informações no log de atividades. Por exemplo, não são registrados o início de uma execução de treinamento e o registro de um modelo.
>
> Algumas dessas ações são exibidas na área **Atividades** do workspace, mas essas notificações não indicam quem iniciou a atividade.

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Para o Azure Machine Learning, você deve habilitar a verificação do recurso de registro de contêiner do Azure e os recursos do serviço kubernetes do Azure. Consulte [verificação de imagem do registro de contêiner do Azure por central de segurança](../security-center/defender-for-container-registries-introduction.md) e [integração dos serviços Kubernetess do Azure com a central de segurança](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditar e gerenciar conformidade

[Azure Policy](../governance/policy/index.yml) é uma ferramenta de governança que permite garantir que os recursos do Azure estejam em conformidade com suas políticas. Com Azure Machine Learning, você pode atribuir as seguintes políticas:

* **Chave gerenciada pelo cliente**: auditar ou impor se os espaços de trabalho devem usar uma chave gerenciada pelo cliente.
* **Link privado**: auditar se os espaços de trabalho usam um ponto de extremidade privado para se comunicar com uma rede virtual.

Para obter mais informações sobre Azure Policy, consulte a [documentação do Azure Policy](../governance/policy/overview.md).

Para obter mais informações sobre as políticas específicas para Azure Machine Learning, consulte [auditar e gerenciar a conformidade com o Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Bloqueios de recurso

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Próximas etapas

* [Proteger serviços Web do Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Usar o Azure Machine Learning com o Firewall do Azure](how-to-access-azureml-behind-firewall.md)
* [Usar o Azure Machine Learning com a Rede Virtual do Azure](how-to-network-security-overview.md)
* [Criptografia de dados em repouso e em trânsito](concept-data-encryption.md)
* [Compilar uma API de recomendação em tempo real no Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
