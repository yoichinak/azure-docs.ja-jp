---
title: Azure Maps で GeoJSON ジオメトリを拡張する | Microsoft Docs
description: Azure Maps で GeoJSON ジオメトリを拡張する方法について説明します。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 319f9cba23d088553f361b6a0d648bbde94e0743
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968563"
---
# <a name="extending-geojson-geometries"></a>GeoJSON ジオメトリの拡張

Azure Maps は、地理機能内で、または地理機能を併用して検索に役立つ API を提供します。
これらの API は、地理的な特長 (州の境界、高速道路など) を表すための [GeoJSON 仕様][1]を標準化します。  

[GeoJSON 仕様][1]は、次のジオメトリのみをサポートします。

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Azure Maps API ([Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) など) の中には、[GeoJSON 仕様][1] に含まれない "Circle" が使用できるものもあります。

この記事では、特定のジオメトリを表すため、Azure Maps が [GeoJSON 仕様][1]をどのように拡張するかについて詳細に説明します。

### <a name="circle"></a>Circle

`Circle` ジオメトリは [GeoJSON 仕様][1] ではサポートされていません。円を表すには、`GeoJSON Feature` オブジェクトが使用されます。

`GeoJSON Feature` オブジェクトを使用して表現される `Circle` ジオメトリは、以下を含む__必要があります__。

1. Center
   >円の中心は `GeoJSON Point` 型を使用して表します。

2. Radius
   >円の `radius` は `GeoJSON Feature` のプロパティを使用して表します。 半径値は、_メートル_単位で、`double` 型である必要があります。

3. SubType
   >円ジオメトリには、`subType` プロパティも含める必要があります。 このプロパティは、`GeoJSON Feature` のプロパティに含まれ、その値は _Circle_ である必要があります。


#### <a name="example"></a>例

ここでは、`GeoJSON Feature` オブジェクトを使用して、半径が 100 m で中心が緯度: 47.639754、経度:-122.126986 にある円を表します。

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

[1]: https://tools.ietf.org/html/rfc7946
