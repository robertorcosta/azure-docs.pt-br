---
title: Monitorando a segurança de seus contêineres na central de segurança do Azure
description: Saiba como verificar a postura de segurança de seus contêineres na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 8ec06fc23b147eb3e4a5922242aa922063f4172c
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514145"
---
# <a name="monitor-the-security-of-your-containers"></a>Monitorar a segurança de seus contêineres

Esta página explica como usar os recursos de segurança do contêiner descritos no [artigo segurança do contêiner](container-security.md) em nossa seção de conceitos.


## <a name="scan-your-arm-based-container-registries-for-vulnerabilities"></a>Verificar se há vulnerabilidades nos registros de contêiner baseados em ARM 

1. Para habilitar verificações de vulnerabilidade de suas imagens do registro de contêiner do Azure:

    1. Verifique se você está no tipo de preço Standard da central de segurança do Azure.

    1. Na página **configurações de & de preços** , habilite o pacote de registros de contêiner opcional para sua assinatura: ![ habilitando o pacote de registros de contêiner](media/monitor-container-security/enabling-container-registries-bundle.png)

        A central de segurança agora está pronta para verificar imagens que são enviadas por push ao registro. 

      >[!NOTE]
      >Esse recurso é cobrado por imagem.


1. Para disparar a verificação de uma imagem, envie-a por push para o registro. 

    Quando a verificação é concluída (normalmente após aproximadamente 2 minutos, mas pode ter até 15 minutos), as descobertas estão disponíveis como recomendações da central de segurança.

1. Para exibir as descobertas, vá para a página **recomendações** . Se forem encontrados problemas, você verá a seguinte recomendação:

    ![Recomendação para corrigir problemas ](media/monitor-container-security/acr-finding.png)

1. Selecione a recomendação. 
    A página detalhes da recomendação é aberta com informações adicionais. Essas informações incluem a lista de registros com imagens vulneráveis ("recursos afetados") e as etapas de correção. 

1. Selecione um registro específico para ver os repositórios dentro dele que têm repositórios vulneráveis.

    ![Selecionar um registro](media/monitor-container-security/acr-finding-select-registry.png)

    A página detalhes do registro é aberta com a lista de repositórios afetados.

1. Selecione um repositório específico para ver os repositórios dentro dele que têm imagens vulneráveis.

    ![Selecionar um repositório](media/monitor-container-security/acr-finding-select-repository.png)

    A página detalhes do repositório é aberta. Ele lista as imagens vulneráveis junto com uma avaliação da gravidade das descobertas.

1. Selecione uma imagem específica para ver as vulnerabilidades.

    ![Selecionar imagens](media/monitor-container-security/acr-finding-select-image.png)

    A lista de conclusões para a imagem selecionada é aberta.

    ![Lista de conclusões](media/monitor-container-security/acr-findings.png)

1. Para saber mais sobre uma localização, selecione a localização. 

    O painel detalhes de conclusões é aberto.

    [![Painel de detalhes de conclusões](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Esse painel inclui uma descrição detalhada do problema e links para recursos externos para ajudar a mitigar as ameaças.

1. Siga as etapas na seção correção deste painel.

1. Quando você executou as etapas necessárias para corrigir o problema de segurança, substitua a imagem no registro:

    1. Envie por push a imagem atualizada. Isso irá disparar uma verificação. 
    
    1. Marque a página recomendações para a recomendação "vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas". 
    
        Se a recomendação ainda aparecer e a imagem que você tratou ainda aparecer na lista de imagens vulneráveis, verifique as etapas de correção novamente.

    1. Quando tiver certeza de que a imagem atualizada foi enviada por push, verificada e não está mais aparecendo na recomendação, exclua a imagem "antiga" vulnerável do registro.


## <a name="harden-your-containers-docker-hosts"></a>Proteger hosts do Docker de seus contêineres

A central de segurança monitora constantemente a configuração dos hosts do Docker e gera recomendações de segurança que refletem os padrões do setor.

Para exibir as recomendações de segurança da central de segurança do Azure para os hosts do Docker de seus contêineres:

1. Na barra de navegação da central de segurança, abra **computação & aplicativos** e selecione a guia **contêineres** .

1. Opcionalmente, filtre a lista de seus recursos de contêiner para hosts de contêineres.

    ![Filtro de recursos de contêiner](media/monitor-container-security/container-resources-filter.png)

1. Na lista de computadores host do contêiner, selecione um para investigar mais.

    ![Recomendações de host do contêiner](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    A **página informações do host do contêiner** é aberta com detalhes do host e uma lista de recomendações.

1. Na lista de recomendações, selecione uma recomendação para investigar mais.

    ![Lista de recomendações de host do contêiner](media/monitor-container-security/container-host-rec.png)

1. Opcionalmente, leia a descrição, as informações, as ameaças e as etapas de correção. 

1. Selecione **executar ação** na parte inferior da página.

    [![Botão tomar ação](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics é aberto com uma operação personalizada pronta para ser executada. A consulta personalizada padrão inclui uma lista de todas as regras com falha que foram avaliadas, juntamente com as diretrizes para ajudá-lo a resolver os problemas.

    [![Ação de Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Ajuste os parâmetros de consulta, se necessário.

1. Quando tiver certeza de que o comando é apropriado e pronto para o host, selecione **executar**.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar os recursos de segurança do contêiner da central de segurança. 

Para obter outros materiais relacionados, consulte as seguintes páginas: 

- [Recomendações da central de segurança para contêineres](recommendations-reference.md#recs-containers)
- [Alertas para o nível de cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de host do contêiner](alerts-reference.md#alerts-containerhost)
