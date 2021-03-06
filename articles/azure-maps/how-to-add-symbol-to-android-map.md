---
title: Android haritalara sembol katmanı ekleme | Microsoft Azure Haritaları
description: Bu makalede, Microsoft Azure Haritalar Android SDK'yı kullanarak bir sembol katmanı ekleyerek haritadaki nokta verilerini nasıl işleyeceksiniz öğreneceksiniz.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335583"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Azure Haritalar Android SDK'yı kullanarak haritaya sembol katmanı ekleme

Bu makalede, azure haritalar Android SDK'yı kullanarak bir haritaüzerinde bir veri kaynağından gelen nokta verilerini bir sembol katmanı olarak nasıl işlendirilebilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu makaledeki adımları tamamen izlemek için, bir harita yüklemek için [Azure Haritalar Android SDK'yı](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) yüklemeniz gerekir.

## <a name="add-a-symbol-layer"></a>Sembol katmanı ekleme

Sembol katmanını kullanarak haritaya bir işaretçi eklemek için aşağıdaki adımları izleyin:

1. Aşağıdaki XML gibi görünüyor böylece**düzen** > **activity_main.xml** **res** > edit edin:
    
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

2. Aşağıdaki kod parçacıklarını sınıfınızın `MainActivity.java` **onCreate()** yöntemine kopyalayın.

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
    
    Yukarıdaki kod snippet, **onReady()** geri arama yöntemini kullanarak bir Azure Haritalar harita denetimi örneği alır. Daha sonra **DataSource** sınıfını kullanarak bir veri kaynağı nesnesi oluşturur ve eşe ekler. Daha sonra bir Nokta geometrisi içeren bir **Özellik** ekler. Kırmızı işaretçi görüntüsü daha sonra sembol simgesi olarak ayarlanır. **Sembol katmanı,** veri kaynağına sarılmış nokta tabanlı verileri haritada sembol olarak işlemek için metin veya simgeler kullanır. Daha sonra bir sembol katmanı oluşturulur ve veri kaynağı işlemek için ona aktarılır ve daha sonra haritanın katmanlarına eklenir.
    
    Yukarıdaki kod parçacığı ekledikten sonra, `MainActivity.java` aşağıdaki gibi görünmelidir:
    
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
    
Bu noktada, uygulamanızı çalıştırarsanız, burada gösterildiği gibi haritada bir işaretçi görmeniz gerekir:

<center>

![Android harita pini](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Sonraki adımlar

Haritanıza daha fazla malzeme eklemek için bkz:

> [!div class="nextstepaction"]
> [Android haritasına şekil ekleme](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Özellik bilgilerini görüntüleme](display-feature-information-android.md)