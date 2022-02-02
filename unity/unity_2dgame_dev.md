# Unity 2D 게임 개발

## RectTransform

### Rect
RectTransform의 rect속성은 읽기 전용이다. rect 속성을 변경하려면 rect.Set() 매서드를 이용.
```csharp
public GameObject o;

o.GetComponent<RectTransform>().rect.Set(x, y, width, height);
```

### Anchored Position
Unity 에디터의 인스펙터 창에 표시되는 좌표는 anchoredPosition 속성값이다. Vector2 타입이다.
```csharp
// 객체가 화면 아래쪽으로 사라질 때, 객체 제거하기

using UnityEngine;
using System.Collections;

public class AutoDestroyAtBottom : MonoBehaviour {

  RectTransform rectTr;

  void Start() {
    rectTr = GetComponent<RectTransform>();
  }
  
  void FixedUpdate() {
    if (rectTr.anchoredPosition.y <= -rectTr.rect.height) {
      Destroy(gameObject);
    }
  }
}
```

### 좌표 변환, 검사
화면 좌표가 RectTransform 영역안에 들어 있는지 검사.
```csharp
RectTransform box;

void Start() {
  box = GetComponent<RectTransform>();
}

void FixedUpdate() {
  if (Input.GetMouseButtonDown(0)) {
    if (RectTransformUtility.RectangleContainsScreenPoint(box, Input.mousePosition)) {
      // TODO
    }
  }
}
```

## Sprite

Resource에서 Sprite 로딩.

```csharp
SpriteRenderer spr = GetComponent<SpriteRenderer>();
spr.sprite = Resources.Load<Sprite>("path/name");

// 라이브러리화
public static Sprite LoadSpriteFromResource(string path, string res_name) {
  return Resources.Load<Sprite>(path + "/" + res_name);
}
```
