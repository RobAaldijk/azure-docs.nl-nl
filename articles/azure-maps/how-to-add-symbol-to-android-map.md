---
title: Een symbool laag toevoegen aan Android-kaarten | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van een markering aan een kaart. Bekijk een voor beeld waarin de Azure Maps Android SDK wordt gebruikt om een Symbol-laag toe te voegen die op punten gebaseerde gegevens uit een gegevens bron bevat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: acd5f06a5383308ce736f2860810ebee7e5bce28
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897106"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Een symbool laag aan een kaart toevoegen met Azure Maps Android SDK

Dit artikel laat u zien hoe u punt gegevens van een gegevens bron kunt weer geven als een symbool laag op een kaart met behulp van de Azure Maps Android SDK.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel volledig wilt volgen, moet u [Azure Maps ANDROID SDK](./how-to-use-android-map-control-library.md) installeren om een kaart te laden.

## <a name="add-a-symbol-layer"></a>Een symboollaag toevoegen

Volg de onderstaande stappen om een markering op de kaart toe te voegen met behulp van de Symbol-laag:

1. **res**  >  **layout**  >  U kunt de indeling van de res bewerken **activity_main.xml** zodat deze eruitziet als de volgende XML:
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Kopieer het volgende code fragment in de methode **onCreate ()** van uw `MainActivity.java` klasse.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    In het bovenstaande code fragment wordt eerst een exemplaar van Azure Maps kaart besturings element opgehaald met de call back methode **onReady ()** . Vervolgens wordt een gegevens bron object gemaakt met behulp van de klasse **Data Source** en toegevoegd aan de kaart. Vervolgens wordt er een **functie** met een punt geometrie toegevoegd. Vervolgens wordt een rode afbeelding van de markering ingesteld als pictogram voor het symbool. Een **symbool laag** gebruikt tekst of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de gegevens bron zijn verpakt als symbool op de kaart. Er wordt vervolgens een symbool laag gemaakt en de gegevens bron wordt door gegeven om weer te geven en wordt vervolgens toegevoegd aan de lagen van de kaart.
    
    Nadat u het code fragment hierboven hebt toegevoegd, `MainActivity.java` ziet uw er als volgt uit:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
Als u de toepassing uitvoert, moet u op dit punt een markering op de kaart zien, zoals hier wordt weer gegeven:

<center>

![Pincode van Android-kaart](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Standaard optimaliseert symbool lagen de rendering van symbolen door symbolen te verbergen die elkaar overlappen. Wanneer u inzoomt, worden de verborgen symbolen zichtbaar. Als u deze functie wilt uitschakelen en alle symbolen op elk moment wilt weer geven, stelt `iconAllowOverlap` u de optie in op `true` .

## <a name="next-steps"></a>Volgende stappen

Als u meer dingen wilt toevoegen aan uw kaart, raadpleegt u:

> [!div class="nextstepaction"]
> [Vormen toevoegen aan een Android-kaart](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)