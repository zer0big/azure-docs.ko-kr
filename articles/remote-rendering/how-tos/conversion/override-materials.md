---
title: 모델 변환 중 재질 재정의
description: 변환 시 워크플로를 재정의 하는 재질에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576645"
---
# <a name="override-materials-during-model-conversion"></a>모델 변환 중 재질 재정의

원본 모델의 재질 설정은 렌더러에 사용 되는 [.pbr 재질](../../overview/features/pbr-materials.md) 을 정의 하는 데 사용 됩니다.
[기본 변환이](../../reference/material-mapping.md) 원하는 결과를 제공 하지 않으며 변경 해야 하는 경우도 있습니다.
Azure 원격 렌더링에서 사용 하기 위해 모델을 변환할 때 재질을 기준으로 자료를 변환 하는 방법을 사용자 지정 하는 재질 재정의 파일을 제공할 수 있습니다.
이라는 파일이 입력 `<modelName>.MaterialOverrides.json` 모델 옆의 입력 컨테이너에 있으면 해당 파일은 `<modelName>.<ext>` 재질 재정의 파일로 사용 됩니다.

## <a name="the-override-file-used-during-conversion"></a>변환 하는 동안 사용 된 재정의 파일

간단한 예로, box 모델에 "Default" 라는 단일 재료가 있다고 가정해 보겠습니다.
또한 ARR에서 사용할 수 있도록 albedo 색을 조정 해야 한다고 가정 합니다.
이 경우 다음과 `box.MaterialOverrides.json` 같이 파일을 만들 수 있습니다.

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box.MaterialOverrides.json`이 파일은 `box.fbx` 변환 서비스에 새 설정을 적용 하도록 지시 하는의 입력 컨테이너에 배치 됩니다.

### <a name="color-materials"></a>색 재질

[색 재질](../../overview/features/color-materials.md) 모델은 조명과 독립적인 지속적으로 음영이 있는 표면에 대해 설명 합니다.
색 재질은 Photogrammetry 알고리즘에서 만든 자산에 유용 합니다 (예:).
재질 재정의 파일에서을로 설정 하 여 재질을 색 재질로 선언할 수 있습니다 `unlit` `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>특정 텍스처 맵 무시

경우에 따라 변환 프로세스에서 특정 질감 맵을 무시 하도록 할 수 있습니다. 이는 렌더러에서 의해 올바르게 인식 되지 않는 특수 맵을 생성 하는 도구에 의해 모델이 생성 된 경우에 발생할 수 있습니다. 예를 들어 불투명도 이외의 항목을 정의 하는 데 사용 되는 "OpacityMap" 또는 "NormalMap"이 "BumpMap"로 저장 되는 모델입니다. 후자의 경우 "NormalMap"을 무시 하는 것이 좋습니다 .이 경우 변환기가 "BumpMap"를 "NormalMap"로 사용 합니다.

원칙은 간단 합니다. 이라는 속성을 추가 `ignoreTextureMaps` 하 고 무시할 질감 맵을 추가 하기만 하면 됩니다.

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

무시할 수 있는 질감 맵의 전체 목록은 아래의 JSON 스키마를 참조 하세요.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>여러 자료에 동일한 재정의 적용

기본적으로 해당 이름이 재료 이름과 정확히 일치 하는 경우 재질 재정의 파일의 항목이 적용 됩니다.
동일한 재정의가 여러 재질에 적용 되는 것은 매우 일반적 이기 때문에 필요에 따라 정규식을 항목 이름으로 제공할 수 있습니다.
필드에는 `nameMatching` 기본값이 `exact` 있지만로 설정 하 여 일치 하는 `regex` 모든 자료에 항목을 적용 해야 함을 지정할 수 있습니다.
사용 되는 구문은 JavaScript에 사용 되는 구문과 동일 합니다. 다음 예제에서는 "Material2", "Material01" 및 "Material999"와 같은 이름을 가진 재질에 적용 되는 재정의를 보여 줍니다.

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

재질 재정의 파일에서 최대 하나의 항목은 단일 재질에 적용 됩니다.
재료 이름에 정확히 일치 하는 항목 (예: `nameMatching` 가 없거나 같음)이 있으면 `exact` 해당 항목이 선택 됩니다.
그렇지 않으면 재질 이름과 일치 하는 파일의 첫 번째 regex 항목이 선택 됩니다.

### <a name="getting-information-about-which-entries-applied"></a>적용 된 항목에 대 한 정보 가져오기

출력 컨테이너에 작성 된 [정보 파일](get-information.md#information-about-a-converted-model-the-info-file) 은 제공 된 재정의 수와 재정의 된 자료 수에 대 한 정보를 전달 합니다.

## <a name="json-schema"></a>JSON 스키마

여기에는 자료 파일에 대 한 전체 JSON 스키마가 제공 됩니다. 및를 제외 하 `unlit` 고 `ignoreTextureMaps` 사용할 수 있는 속성은 [색 재질](../../overview/features/color-materials.md) 및 [.pbr 재질](../../overview/features/pbr-materials.md) 모델의 섹션에서 설명 하는 속성의 하위 집합입니다.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>다음 단계

* [색 재질](../../overview/features/color-materials.md)
* [PBR 재질](../../overview/features/pbr-materials.md)
