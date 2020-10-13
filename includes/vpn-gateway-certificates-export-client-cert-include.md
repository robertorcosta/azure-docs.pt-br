---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3031ec6ba17c61c780351169c4e8c2e5e94ab257
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606759"
---
Quando você gerar um certificado do cliente, ele será instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado do cliente em outro computador cliente, será necessário exportar o certificado do cliente que você gerou.

1. Para exportar um certificado de cliente, abra **Gerenciar certificados de usuário**. Os certificados do cliente que você gerou são, por padrão, localizados em 'Certificates - Current User\Personal\Certificates'. Clique com o botão direito do mouse no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar** para abrir o **Assistente para Exportação de Certificados**.

   ![Captura de tela mostra a janela certificados para o usuário atual com certificados selecionados e um menu contextual com todas as tarefas e, em seguida, exportar selecionado.](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. No Assistente para Exportação de Certificados, clique em **Avançar** para continuar.

   ![Captura de tela mostra a mensagem de boas-vindas assistente para exportação de certificados](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecione **Sim, exportar a chave privada** e, em seguida, clique em **Avançar**.

   ![exportar chave privada](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na página **Formato do Arquivo de Exportação**, deixe os padrões selecionados. Certifique-se de que **incluir todos os certificados no caminho de certificação, se possível** , esteja selecionado. Adicionalmente, essa exporta as informações sobre o certificado raiz necessárias para uma autenticação de cliente bem-sucedida. Sem isso, a autenticação de cliente irá falhar porque o cliente não possui o certificado raiz confiável. Em seguida, clique em **Próximo**.

   ![exportar formato de arquivo](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Próximo**.

   ![Captura de tela mostra a página segurança do assistente para exportação de certificados com a senha digitada e confirmada e próxima realçada.](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.

   ![arquivo para exportar](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Clique em **Concluir** para exportar o certificado.

   ![Captura de tela mostra a página final do assistente para exportação de certificados com as configurações selecionadas.](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)