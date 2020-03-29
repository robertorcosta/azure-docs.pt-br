---
title: Monitorando a segurança de seus contêineres no Azure Security Center
description: Saiba como verificar a postura de segurança de seus contêineres no Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919525"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitorando a segurança de seus contêineres

Esta página explica como usar os recursos de segurança de contêineres descritos no artigo de Segurança de [Contêineres](container-security.md) em nossa seção de conceitos.

O Azure Security Center abrange os seguintes três aspectos da segurança dos contêineres:

- **Gerenciamento de vulnerabilidades** - Se você estiver no nível de preços padrão do Security Center (veja [preços),](/azure/security-center/security-center-pricing)você pode escanear seu Registro de Contêineres Azure baseado em ARM toda vez que uma nova imagem for empurrada. O scanner (alimentado pela Qualys) apresenta achados como recomendações do Security Center.
    Para obter instruções [detalhadas, consulte Escaneando os registros de contêineres para obter vulnerabilidades](#scanning-your-arm-based-container-registries-for-vulnerabilities) abaixo.

- **Endurecendo os hosts Docker dos contêineres** - o Security Center encontra contêineres não gerenciados hospedados em VMs Linux IaaS ou outras máquinas Linux executando o Docker, e compara continuamente as configurações dos contêineres com o Benchmark do Center for Internet Security (CIS). O Centro de Segurança alerta se seus contêineres não satisfizerem nenhum dos controles. O monitoramento contínuo dos riscos de segurança devido a configurações erradas é um componente crucial de qualquer programa de segurança. 
    Para obter instruções detalhadas, consulte [endurecendo os hosts Docker dos seus contêineres](#hardening-your-containers-docker-hosts) abaixo.

- **Endurecendo seus clusters de serviço do Azure Kubernetes** - o Security Center fornece recomendações quando encontra vulnerabilidades na configuração de seus clusters do Azure Kubernetes Service. Para obter detalhes sobre as recomendações específicas que podem aparecer, consulte as recomendações do [Serviço Kubernetes](recommendations-reference.md#recs-containers).

- **Proteção por tempo de execução** - Se você estiver no nível de preços padrão do Security Center, você terá proteção contra ameaças em tempo real para seus ambientes contêineres. O Security Center gera alertas para atividades suspeitas no nível de cluster host e AKS. Para obter detalhes sobre os alertas de segurança relevantes que podem aparecer, consulte os clusters e alertas do [Serviço Alertes para OZure Kubernetes](alerts-reference.md#alerts-akscluster) para [contêineres -](alerts-reference.md#alerts-containerhost) seções de nível de host da tabela de referência de alertas.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Digitalizando seus registros de contêineres baseados em ARM para obter vulnerabilidades 

1. Para habilitar varreduras de vulnerabilidade de suas imagens do Registro de Contêiner do Azure:

    1. Certifique-se de que você está no nível de preços padrão do Azure Security Center.

    1. Na página **de configurações de & de preços,** habilite ![o pacote de registros de contêineres opcional para sua assinatura: Habilitando o pacote Registros de Contêineres](media/monitor-container-security/enabling-container-registries-bundle.png)

        O Centro de Segurança está pronto para escanear imagens que são empurradas para o registro. 

        >[!NOTE]
        >Esse recurso é carregado por imagem.


1. Para ativar a varredura de uma imagem, empurre-a para o seu registro. 

    Quando a varredura é concluída (normalmente após aproximadamente 10 minutos), as descobertas estão disponíveis nas recomendações do Security Center.
    

1. Para ver as descobertas, acesse a página **Recomendações.** Se os problemas forem encontrados, você verá a seguinte recomendação:

    ![Recomendação para remediar questões ](media/monitor-container-security/acr-finding.png)


1. Selecione a recomendação. 
    A página de detalhes da recomendação é aberta com informações adicionais. Essas informações incluem a lista de registros com imagens vulneráveis ("Recursos afetados") e as etapas de remediação. 

1. Selecione um registro específico para ver os repositórios dentro dele que possuem repositórios vulneráveis.

    ![Selecione um registro](media/monitor-container-security/acr-finding-select-registry.png)

    A página de detalhes do registro é aberta com a lista de repositórios afetados.

1. Selecione um repositório específico para ver os repositórios dentro dele que tenham imagens vulneráveis.

    ![Selecionar um repositório](media/monitor-container-security/acr-finding-select-repository.png)

    A página de detalhes do repositório é aberta. Ele lista as imagens vulneráveis juntamente com uma avaliação da gravidade dos achados.

1. Selecione uma imagem específica para ver as vulnerabilidades.

    ![Selecione imagens](media/monitor-container-security/acr-finding-select-image.png)

    A lista de achados para a imagem selecionada é aberta.

    ![Lista de achados](media/monitor-container-security/acr-findings.png)

1. Para saber mais sobre uma descoberta, selecione o achado. 

    Os detalhes das descobertas abrem o painel.

    [![Detalhes de resultados painel](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Este painel inclui uma descrição detalhada do problema e links para recursos externos para ajudar a mitigar as ameaças.

1. Siga os passos na seção de correção deste painel.

1. Quando você tiver tomado as medidas necessárias para corrigir o problema de segurança, substitua a imagem em seu registro:

    1. Empurre a imagem atualizada. Isso vai desencadear uma varredura. 
    
    1. Verifique a página de recomendações para a recomendação "Vulnerabilidades no Registro de Contêineres do Azure devem ser corrigidas". 
    
        Se a recomendação ainda aparecer e a imagem que você lidou ainda aparecer na lista de imagens vulneráveis, verifique novamente as etapas de remediação.

    1. Quando você tiver certeza de que a imagem atualizada foi empurrada, digitalizada e não aparecer mais na recomendação, exclua a imagem vulnerável "antiga" do seu registro.


## <a name="hardening-your-containers-docker-hosts"></a>Endurecendo os hosts Docker de seus contêineres

O Security Center monitora constantemente a configuração de seus hosts Docker e gera recomendações de segurança que refletem os padrões do setor.

Para ver as recomendações de segurança do Azure Security Center para os hosts Docker dos seus contêineres:

1. Na barra de navegação do Security Center, abra **os aplicativos do Compute &** e selecione a guia **Contêineres.**

1. Opcionalmente, filtre a lista de recursos de contêiner para hosts de hosts de contêineres.

    ![Filtro de recursos de contêiner](media/monitor-container-security/container-resources-filter.png)

1. Na lista de suas máquinas host de contêiner, selecione uma para investigar mais.

    ![Recomendações de host de contêineres](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    A **página de informações do host Container** é aberta com detalhes do host e uma lista de recomendações.

1. Na lista de recomendações, selecione uma recomendação para investigar mais.

    ![Lista de recomendação de host de contêineres](media/monitor-container-security/container-host-rec.png)

1. Opcionalmente, leia a descrição, informações, ameaças e etapas de remediação. 

1. Selecione **Tomar ação** na parte inferior da página.

    [![Tome o botão de ação](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    O Log Analytics é aberto com uma operação personalizada pronta para ser executada. A consulta personalizada padrão inclui uma lista de todas as regras que foram avaliadas, juntamente com diretrizes para ajudá-lo a resolver os problemas.

    [![Ação do Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Ajuste os parâmetros de consulta e **selecione Executar** quando tiver certeza de que está pronto para o host. 



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar os recursos de segurança de contêineres do Security Center. 

Para outros materiais relacionados, consulte as seguintes páginas: 

- [Recomendações do Security Center para contêineres](recommendations-reference.md#recs-containers)
- [Alertas para o nível de cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de host do Contêiner](alerts-reference.md#alerts-containerhost)
