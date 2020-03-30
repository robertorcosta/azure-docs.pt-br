---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059943"
---
Quando você gerar um certificado do cliente, ele será instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado do cliente em outro computador cliente, será necessário exportar o certificado do cliente que você gerou.

1. Para exportar um certificado de cliente, abra **Gerenciar certificados de usuário**. Os certificados do cliente que você gerou são, por padrão, localizados em 'Certificates - Current User\Personal\Certificates'. Clique com o botão direito do mouse no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar** para abrir o **Assistente para Exportação de Certificados**.

   ![Exportação](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. No Assistente para Exportação de Certificados, clique em **Avançar** para continuar.

   ![Próximo](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecione **Sim, exportar a chave privada** e, em seguida, clique em **Avançar**.

   ![exportar chave privada](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na página **Formato do Arquivo de Exportação**, deixe os padrões selecionados. Certifique-se de incluir **todos os certificados no caminho da certificação, se possível** for selecionado. Adicionalmente, essa exporta as informações sobre o certificado raiz necessárias para uma autenticação de cliente bem-sucedida. Sem isso, a autenticação de cliente irá falhar porque o cliente não possui o certificado raiz confiável. Em seguida, clique em **Avançar**.

   ![exportar formato de arquivo](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Avançar**.

   ![segurança](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Avançar**.

   ![arquivo para exportar](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Clique em **Concluir** para exportar o certificado.

   ![concluir](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)