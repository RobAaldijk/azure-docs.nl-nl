---
title: SSIS-migratie met Azure SQL Managed instance als de data base-werk belasting bestemming
description: SSIS-migratie met Azure SQL Managed instance als de werkbelasting bestemming voor de data base.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 184cd7ec0dd490152e7234383bffe4f0fd822913
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635708"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS-migratie met Azure SQL Managed instance als de data base-werk belasting bestemming

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bij het migreren van data base-workloads van een SQL Server-exemplaar naar een Azure SQL Managed instance, moet u bekend zijn met de [Azure Data Migration service](../dms/dms-overview.md)(DMS) en de [netwerktopologieën voor migraties van SQL Managed instances met behulp van DMS](../dms/resource-network-topologies.md).

Dit artikel is gericht op de migratie van de SSIS-pakketten (SQL Server Integration service) die zijn opgeslagen in SSIS Catalog (SSISDB) en SQL Server Agent Jobs waarmee SSIS-pakket uitvoeringen worden gepland.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-catalogus migreren (SSISDB)

SSISDB-migratie kan worden uitgevoerd met behulp van DMS, zoals beschreven in het artikel: [SSIS-pakketten migreren naar SQL Managed instance](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS-taken naar SQL Managed instance agent

SQL Managed instance heeft een systeem eigen, eersteklas scheduler, net als SQL Server Agent on-premises.  U kunt [SSIS-pakketten uitvoeren via de Azure SQL Managed instance agent](how-to-invoke-ssis-package-managed-instance-agent.md).

Omdat er nog geen migratie hulpprogramma voor SSIS-taken beschikbaar is, moeten deze worden gemigreerd van SQL Server Agent on-premises naar de SQL Managed instance agent via scripts/hand matig kopiëren.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Netwerk topologieën voor migraties van SQL-beheerde exemplaren met behulp van DMS](../dms/resource-network-topologies.md)
- [SSIS-pakketten migreren naar een beheerd exemplaar van SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)