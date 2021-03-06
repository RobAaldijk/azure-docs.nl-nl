---
title: Virtuele VMware-machines beoordelen voor migratie naar Azure VMware Solution (AVS) met Azure Migrate
description: Leer hoe u virtuele VMware-machines beoordeelt voor migratie naar AVS met Azure Migrate-serverevaluatie.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 29f7f824d96aedd80e490ba84c390be4d9493683
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604237"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Zelfstudie: VMware-VM's evalueren voor migratie naar AVS

Als onderdeel van uw migratietraject naar Azure, beoordeelt u uw on-premises werkbelastingen om de gereedheid voor de cloud te meten, risico's in kaart te brengen en de kosten en complexiteit te ramen.

In dit artikel wordt beschreven hoe u gedetecteerde virtuele VMware-machines (VM's) kunt evalueren voor migratie naar Azure VMware Solution (AVS) met behulp van Azure Migrate: Serverevaluatieprogramma. AVS is een beheerde service waarmee u het VMware-platform in Azure kunt uitvoeren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
- Voer een evaluatie uit op basis van metagegevens en configuratie-informatie van de machine.
- Voer een evaluatie uit op basis van prestatiegegevens.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.



## <a name="prerequisites"></a>Vereisten

Voordat u deze zelfstudie volgt om uw computers te evalueren voor migratie naar AVS, moet u controleren of u de machines hebt gedetecteerd die u wilt beoordelen:

- Volg [deze zelfstudie](tutorial-discover-vmware.md) om machines te detecteren met behulp van het Azure Migrate-apparaat. 
- Volg [deze zelfstudie](tutorial-discover-import.md) om machines te detecteren met behulp van een geïmporteerd CSV-bestand.


## <a name="decide-which-assessment-to-run"></a>Bepalen welke evaluatie moet worden uitgevoerd


Beslis of u een evaluatie wilt uitvoeren met behulp van de groottecriteria op basis van de configuratiegegevens/metagegevens van de machine die zonder aanpassingen on-premises worden verzameld of aan de hand van dynamische prestatiegegevens.

**Evaluatie** | **Details** | **Aanbeveling**
--- | --- | ---
**As-is on-premises** | Beoordeling op basis van configuratiegegevens/metagegevens van de machine.  | De aanbevolen knooppuntgrootte in AVS is gebaseerd op de grootte van de on-premises virtuele machine, samen met de instellingen die u opgeeft in de evaluatie van het knooppunttype, het opslagtype en de instelling voor fouttolerantie.
**Op basis van prestaties** | Evaluaties op basis van verzamelde dynamische prestatiegegevens. | De aanbevolen knooppuntgrootte in AVS is gebaseerd op de gegevens over het CPU- en geheugengebruik, samen met de instellingen die u opgeeft in de evaluatie van het knooppunttype, het opslagtype en de instelling voor fouttolerantie.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Klik op de pagina **Servers** > **Windows- en Linux-servers** op **Servers evalueren en migreren**.

   ![Locatie van de knop om servers te evalueren en migreren](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. In **Azure Migrate: Serverevaluatie** klikt u op **Evalueren**.

3. Selecteer onder **Servers evalueren** > **Type evaluatie**, **Azure VMware Solution (AVS) (preview)** .
4. In **Detectiebron**:

    - Als u machines hebt gedetecteerd met behulp van het apparaat, selecteert u **Machines die zijn gedetecteerd via een Azure Migrate-apparaat**.
    - Selecteer **Geïmporteerde machines** als u machines hebt gedetecteerd met behulp van een geïmporteerd CSV-bestand. 
    
5. Geef een naam op voor de evaluatie. 
6. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Pagina voor het selecteren van de evaluatie-instellingen](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. In **Evaluatie-eigenschappen** > **Doeleigenschappen**:

    - Geef onder **Doelregio** de Azure-regio op waarnaar u de migratie wilt uitvoeren.
       - De aanbevelingen voor grootte en kosten zijn gebaseerd op de locatie die u opgeeft.
       - U kunt momenteel drie regio's evalueren (US - oost, US - west, Europa - west)
   - Laat in **Opslagtype** **vSAN** ongemoeid. Dit is het standaard opslagtype voor een privécloud.
   - **Gereserveerde instanties** worden momenteel niet ondersteund voor AVS-knooppunten.
8. In **VM-grootte**:
    - Selecteer onder **Knooppunttype**een knooppunttype op basis van de werkbelastingen die worden uitgevoerd op de on-premises virtuele machines.
        - Azure Migrate adviseert het knooppunt van knooppunten die nodig zijn om de virtuele machines naar AVS te migreren.
        - Het standaard knooppunttype is AV36.
    - **FTT-instelling, RAID-niveau**, selecteert u de combinatie van fouttolerantie en RAID.  De geselecteerde FTT-optie bepaalt samen met de schijfvereiste voor de on-premises virtuele machine de totale vSAN-opslag die is vereist in AVS.
    - Geef bij **CPU-overbezetting** de verhouding op van virtuele kerngeheugens die zijn gekoppeld aan één fysiek kerngeheugen in het AVS-knooppunt. Een overschrijding van meer dan 4:1 kan leiden tot verminderde prestaties, maar kan worden gebruikt voor werkbelastingen van het type webserver.

9. In **Knooppuntgrootte**: 
    - Selecteer onder **Criterium voor het aanpassen van de grootte** of u de evaluatie wilt baseren op statische metagegevens of op gegevens op basis van de prestaties. Als u prestatiegegevens gebruikt:
        - Geef onder **Prestatiegeschiedenis** de gegevensduur aan waarop u de evaluatie wilt baseren
        - Geef onder **Percentielgebruik** de percentielwaarde op die u wilt gebruiken voor de prestatiesteekproef. 
    - Geef onder **Comfortfactor** de buffer op die u tijdens de evaluatie wilt gebruiken. Deze houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst. Als u bijvoorbeeld een comfortfactor van twee gebruikt:
    
        **Onderdeel** | **Effectief gebruik** | **Comfortfactor toevoegen (2,0)**
        --- | --- | ---  
        Kernen | 2 | 4
        Geheugen | 8 GB | 16 GB     

10. Onder **Prijzen**:
    - In **Aanbieding** wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarvoor u bent ingeschreven weergegeven; serverevaluatie schat de kosten voor die aanbieding.
    - Selecteer bij **Valuta** de factureringsvaluta voor uw account.
    - Voeg onder **Korting (%)** een abonnementspecifieke korting toe die u bovenop de Azure-aanbieding ontvangt. De standaardinstelling is 0%.

11. Klik op **Opslaan** als u wijzigingen aanbrengt.

    ![Evaluatie-eigenschappen](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. Klik onder **Servers evalueren** op **Volgende**.
13. U kunt onder **Servers evalueren** > **Machines selecteren voor evaluatie** een nieuwe groep met servers voor evaluatie maken door **Nieuwe maken** te selecteren en een groepsnaam op te geven. 
14. Selecteer het apparaat en selecteer de virtuele machines die u wilt toevoegen aan de groep. Klik op **Volgende**.
15. Controleer de details van de evaluatie onder **Controleren + evaluatie maken** en klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    > [!NOTE]
    > Voor evaluaties op basis van prestaties raden we u aan om minstens een dag na het starten van de detectie te wachten voordat u een evaluatie maakt. Dit biedt tijd voor het verzamelen van betrouwbaarder prestatiegegevens. In het ideale geval wacht u na het starten van de detectie op de prestatieduur die u opgeeft (dag/week/maand) voor een classificatie met hoge betrouwbaarheid.

## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een AVS-evaluatie beschrijft het volgende:

- Gereedheid voor AVS: Of de on-premises virtuele machines geschikt zijn voor migratie naar de Azure VMware Solution (AVS).
- Aantal AVS-knooppunten: Geschat aantal AVS-knooppunten dat is vereist om de virtuele machines uit te voeren.
- Gebruik over AVS-knooppunten: Geprojecteerde CPU-, geheugen- en opslaggebruik op alle knooppunten.
- Schatting van de maandelijkse kosten: De geschatte maandelijkse kosten voor alle knooppunten van de Azure VMware Solution (AVS) die de on-premises virtuele machines uitvoeren.

## <a name="view-an-assessment"></a>Een evaluatie weergeven

U geeft een evaluatie als volgt weer:

1. In **Servers** > **Azure Migrate: Serverevaluatie** klikt u op het nummer naast **Evaluaties**.
2. Selecteer in **Evaluaties** een evaluatie om deze te openen. 
3. Controleer de samenvatting van de evaluatie. U kunt ook de evaluatie-eigenschappen bewerken of de evaluatie opnieuw berekenen.
 

### <a name="review-readiness"></a>Gereedheid controleren

1. Klik op **Azure-gereedheid**.
2. Controleer onder **Azure-gereedheid** de status van de virtuele machine:

    - **Gereed voor AVS**: De machine kan zonder wijzigingen worden gemigreerd naar Azure AVS. De machine wordt gestart in AVS, met volledige AVS-ondersteuning.
    - **Gereed met voorwaarden**: De machine heeft mogelijk compatibiliteitsproblemen met de huidige vSphere-versie. Mogelijk moeten VMware-hulpprogramma's worden geïnstalleerd of andere instellingen worden geconfigureerd voordat er sprake is van volledige functionaliteit in AVS.
    - **Niet gereed voor AVS**: De virtuele machine wordt niet gestart in AVS. Vmotion zal niet werken als u een on-premises virtuele VMware-machine bijvoorbeeld een extern apparaat (zoals een cd-rom) heeft aangesloten en u VMware VMotion gebruikt.
 - **Gereedheid onbekend**: Azure Migrate kan de gereedheid van de machine niet bepalen omdat er onvoldoende metagegevens zijn verzameld in de on-premises omgeving.

3. Bekijk het voorgestelde hulpprogramma.

    - VMware HCX of Enterprise: Voor VMware-machines is de VMWare HCX-oplossing (Hybrid Cloud Extension) het voorgestelde hulpprogramma voor migratie voor het migreren van uw on-premises workload naar uw AVS-privécloud (Azure VMware Solution). Meer informatie
    - Onbekend: Voor machines die zijn geïmporteerd via een CSV-bestand, is het standaardhulpprogramma voor migratie onbekend. Voor VMware-machines wordt echter aanbevolen om de VMWare HCX-oplossing (Hybrid Cloud Extension) te gebruiken.
4. Klik op een Azure-gereedheidsstatus. U kunt details van de VM-gereedheid weergeven en inzoomen op de details van de VM, inclusief berekenings-, opslag- en netwerkinstellingen.

### <a name="review-cost-estimates"></a>Geschatte kosten

Het evaluatieoverzicht toont de geschatte reken- en opslagkosten om virtuele machines in Azure uit te voeren. 

1. Controleer de maandelijkse totale kosten. De kosten worden geaggregeerd voor alle VM's in de geëvalueerde groep.

    - Kostenramingen zijn gebaseerd op het aantal AVS-knooppunten dat is vereist op basis van de resourcevereisten van alle virtuele machines bij elkaar.
    - Omdat de prijzen voor AVS per knooppunt gelden, zijn de totale kosten zonder verdeling van de rekenkosten en opslagkosten.
    - De schatting van de kosten is voor het uitvoeren van de on-premises virtuele machines in AVS. Azure Migrate Serverevaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. Controleer de geschatte de maandelijkse opslag. In deze weergave worden de samengevoegde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven. 
3. U kunt inzoomen om de kostendetails voor specifieke VM's te bekijken.

### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Serverevaluatie wijst een betrouwbaarheidsclassificatie toe aan evaluaties op basis van prestaties. De classificatie is van één ster (laagste) tot vijf sterren (hoogste).

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte in de evaluatie. De classificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.

> [!NOTE]
> Betrouwbaarheidsclassificaties worden niet toegewezen als u een evaluatie maakt op basis van een CSV-bestand.

Betrouwbaarheidsclassificaties zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](concepts-assessment-calculation.md#confidence-ratings-performance-based) over betrouwbaarheidsclassificaties.

## <a name="next-steps"></a>Volgende stappen

- Vind machineafhankelijkheden met behulp van [Toewijzing van afhankelijkheid](concepts-dependency-visualization.md).
- Stel [agentloze](how-to-create-group-machine-dependencies-agentless.md) of [op agent gebaseerde](how-to-create-group-machine-dependencies.md) afhankelijkheidstoewijzing in.
