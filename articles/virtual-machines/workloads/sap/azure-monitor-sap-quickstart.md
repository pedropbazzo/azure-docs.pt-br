---
title: Implantar Azure Monitor para soluções SAP com o portal do Azure
description: Implantar Azure Monitor para soluções SAP com o portal do Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.date: 08/17/2020
ms.reviewer: cynthn
ms.openlocfilehash: 6deb7b535c3876ae8a8e83174b97a75582e82e58
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996431"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Implantar Azure Monitor para soluções SAP com portal do Azure

Azure Monitor para recursos de soluções SAP podem ser criadas por meio do [portal do Azure](https://azure.microsoft.com/features/azure-portal). Esse método fornece uma interface do usuário baseada em navegador para implantar Azure Monitor para soluções SAP e configurar provedores.

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no portal do Microsoft Azure em https://portal.azure.com

## <a name="create-monitoring-resource"></a>Criar recurso de monitoramento

1. Selecione **Azure monitor para soluções SAP** do **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Imagem mostra a seleção do Azure Monitor para a oferta de soluções SAP do Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Na guia **noções básicas** , forneça os valores necessários. Se aplicável, você pode usar um espaço de trabalho Log Analytics existente.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Imagem mostra a seleção do Azure Monitor para a oferta de soluções SAP do Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Ao selecionar uma rede virtual, verifique se os sistemas que você deseja monitorar estão acessíveis de dentro dessa VNET. 

   > [!IMPORTANT]
   > A seleção de **compartilhamento** para compartilhamento de dados com a Microsoft permite que nossas equipes de suporte forneçam suporte adicional.

## <a name="configure-providers"></a>Configurar provedores

### <a name="sap-hana-provider"></a>Provedor de SAP HANA 

1. Selecione a guia **provedor** para adicionar os provedores que você deseja configurar. Você pode adicionar vários provedores um após o outro ou adicioná-los depois de implantar o recurso de monitoramento. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Imagem mostra a seleção do Azure Monitor para a oferta de soluções SAP do Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Selecione **Adicionar provedor** e escolha **SAP Hana** na lista suspensa. 

3. Insira o IP privado para o servidor HANA.

4. Insira o nome do locatário do banco de dados que você deseja usar. Você pode escolher qualquer locatário no entanto, é recomendável usar o **SYSTEMDB** , pois ele permite uma matriz mais ampla de áreas de monitoramento. 

5. Insira o número da porta SQL associada ao seu banco de dados HANA. O número da porta deve estar no formato **[3]**  +  **[Instance #]**  +  **[13]** ou **[3]**  +  **[Instance #]**  +  **[15]**. Por exemplo, 30013 ou 30015. 

6. Insira o nome de usuário do banco de dados que você deseja usar. Verifique se o usuário do banco de dados tem as funções de leitura e de **monitoramento** de **Catálogo** atribuídas. 

7. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar provedores adicionais conforme necessário ou selecione **revisar + criar** para concluir a implantação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Imagem mostra a seleção do Azure Monitor para a oferta de soluções SAP do Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Provedor de pacemaker (cluster de alta disponibilidade)

1. Selecione **pacemaker (cluster de alta disponibilidade)** na lista suspensa. 

   > [!IMPORTANT]
   > Para configurar o provedor de cluster de alta disponibilidade (pacemaker), verifique se ha_cluster_provider está instalado em cada nó. Para obter mais informações, consulte [exportador de cluster de alta disponibilidade](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Insira o ponto de extremidade Prometheus na forma de http://IP:9664/metrics . 
 
3. Insira a ID do sistema (SID), o nome do host e do cluster.

4. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar provedores adicionais conforme necessário ou selecione **revisar + criar** para concluir a implantação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Imagem mostra a seleção do Azure Monitor para a oferta de soluções SAP do Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Provedor do Microsoft SQL Server

1. Antes de adicionar o provedor de Microsoft SQL Server, você deve executar o script a seguir em SQL Server Management Studio para criar um usuário com as permissões apropriadas necessárias para configurar o provedor.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Selecione **Adicionar provedor** e escolha **Microsoft SQL Server** na lista suspensa. 

3. Preencha os campos usando as informações associadas à sua Microsoft SQL Server. 

4. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar provedores adicionais conforme necessário ou selecione **revisar + criar** para concluir a implantação.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Imagem mostra a seleção do Azure Monitor para a oferta de soluções SAP do Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure monitor para soluções SAP](azure-monitor-overview.md)
