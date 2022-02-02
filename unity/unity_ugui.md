# Unity uGUI

## RectTransform
### width/height 변경

```csharp
rectTransform.sizeDelta = new Vector2( yourWidth, yourHeight);
```

### position

```csharp
rectTransform.anchoredPosition = new Vector2(x, y);
```

## Text
### Rich Text

```html
<b>Bold</b>
<i>イタリック体</i> 
<size=10px>ここだけ10pxにする</size> <size=20>pxは省略してsize=20などと記述可能</size>
<color=red>ここだけ赤色</color>の文字にする
<color=#00ff0055>ちゃんとアルファも反映されます</color>
```

### 자간 조절
Unity의 기본 Text 컴포넌트에는 자간 조절 기능이 없다.

아래 스크립트는 Text 객체에 자간 효과를 추가해 준다.

```csharp
// LetterSpacing.cs
// http://forum.unity3d.com/threads/adjustable-character-spacing-free-script.288277/
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

/*
 
Produces an simple tracking/letter-spacing effect on UI Text components.
 
Set the spacing parameter to adjust letter spacing.
  Negative values cuddle the text up tighter than normal. Go too far and it'll look odd.
  Positive values spread the text out more than normal. This will NOT respect the text area you've defined.
  Zero spacing will present the font with no changes.
 
Relies on counting off characters in your Text compoennt's text property and
matching those against the quads passed in via the verts array. This is really
rather primative, but I can't see any better way at the moment. It means that
all sorts of things can break the effect...
 
This component should be placed higher in component list than any other vertex
modifiers that alter the total number of verticies. Eg, place this above Shadow
or Outline effects. If you don't, the outline/shadow won't match the position
of the letters properly. If you place the outline/shadow effect second however,
it will just work on the altered vertices from this component, and function
as expected.
 
This component works best if you don't allow text to automatically wrap. It also
blows up outside of the given text area. Basically, it's a cheap and dirty effect,
not a clever text layout engine. It can't affect how Unity chooses to break up
your lines. If you manually use line breaks however, it should detect those and
function more or less as you'd expect.
 
The spacing parameter is measured in pixels multiplied by the font size. This was
chosen such that when you adjust the font size, it does not change the visual spacing
that you've dialed in. There's also a scale factor of 1/100 in this number to
bring it into a comfortable adjustable range. There's no limit on this parameter,
but obviously some values will look quite strange.
 
This component doesn't really work with Rich Text. You don't need to remember to
turn off Rich Text via the checkbox, but because it can't see what makes a
printable character and what doesn't, it will typically miscount characters when you
use HTML-like tags in your text. Try it out, you'll see what I mean. It doesn't
break down entirely, but it doesn't really do what you'd want either.
 
*/

namespace UnityEngine.UI
{
  [AddComponentMenu("UI/Effects/Letter Spacing", 14), RequireComponent(typeof(Text))]
  #if UNITY_5_2 || UNITY_5_3
  public class LetterSpacing : BaseMeshEffect, ILayoutElement
  #else
  public class LetterSpacing : BaseVertexEffect, ILayoutElement
  #endif
  {
    [SerializeField]
    private float m_spacing = 0f;

    public float spacing
    {
      get { return this.m_spacing; }
      set
      {
        if (this.m_spacing == value) return;
        this.m_spacing = value;
        if (this.graphic != null) this.graphic.SetVerticesDirty();
        LayoutRebuilder.MarkLayoutForRebuild((RectTransform)this.transform);
      }
    }

    private Text text
    {
      get { return this.gameObject.GetComponent<Text>(); }
    }

    public float minWidth
    {
      get { return this.text.minWidth; }
    }

    public float preferredWidth
    {
      get { return this.text.preferredWidth + ((this.spacing *(float)this.text.fontSize / 100f) * (this.text.text.Length - 1)); }
    }

    public float flexibleWidth
    {
      get { return this.text.flexibleWidth; }
    }

    public float minHeight
    {
      get { return this.text.minHeight; }
    }

    public float preferredHeight
    {
      get { return this.text.preferredHeight; }
    }

    public float flexibleHeight
    {
      get { return this.text.flexibleHeight; }
    }

    public int layoutPriority
    {
      get { return this.text.layoutPriority; }
    }

    protected LetterSpacing() { }

    #if UNITY_EDITOR
    protected override void OnValidate()
    {
      base.OnValidate();
      this.spacing = this.m_spacing;
      LayoutRebuilder.MarkLayoutForRebuild((RectTransform)this.transform);
    }
    #endif

    public void CalculateLayoutInputHorizontal() { }
    public void CalculateLayoutInputVertical() { }

    private string[] GetLines()
    {
      IList<UILineInfo> lineInfos = text.cachedTextGenerator.lines;
      string[] lines = new string[lineInfos.Count];

      for (int i = 0; i < lineInfos.Count; i++)
      {
        if ((i + 1) < lineInfos.Count)
        {
          lines[i] = this.text.text.Substring(lineInfos[i].startCharIdx, lineInfos[i + 1].startCharIdx - 1);
        }
        else
        {
          lines[i] = this.text.text.Substring(lineInfos[i].startCharIdx);
        }
      }

      return lines;
    }

    #if UNITY_5_2 || UNITY_5_3
    public override void ModifyMesh(Mesh mesh)
    {
    if (!this.IsActive())
    return;

    List<UIVertex> list = new List<UIVertex>();
    using (VertexHelper vertexHelper = new VertexHelper(mesh))
    {
    vertexHelper.GetUIVertexStream(list);
    }

    this.ModifyVertices(list);  // calls the old ModifyVertices which was used on pre 5.2

    using (VertexHelper vertexHelper2 = new VertexHelper())
    {
    vertexHelper2.AddUIVertexTriangleStream(list);
    vertexHelper2.FillMesh(mesh);
    }
    }
    #endif
    public override void ModifyMesh(VertexHelper vh)
    {
      if (!this.IsActive())
        return;

      List<UIVertex> vertexList = new List<UIVertex>();
      vh.GetUIVertexStream(vertexList);

      ModifyVertices(vertexList);

      vh.Clear();
      vh.AddUIVertexTriangleStream(vertexList);
    }

    public void ModifyVertices(List<UIVertex> verts)
    {
      if (!this.IsActive()) return;

      string[] lines = this.GetLines();

      Vector3 pos;
      float letterOffset = this.spacing * (float)this.text.fontSize / 100f;
      float alignmentFactor = 0;
      int glyphIdx = 0;

      switch (this.text.alignment)
      {
      case TextAnchor.LowerLeft:
      case TextAnchor.MiddleLeft:
      case TextAnchor.UpperLeft:
        alignmentFactor = 0f;
        break;

      case TextAnchor.LowerCenter:
      case TextAnchor.MiddleCenter:
      case TextAnchor.UpperCenter:
        alignmentFactor = 0.5f;
        break;

      case TextAnchor.LowerRight:
      case TextAnchor.MiddleRight:
      case TextAnchor.UpperRight:
        alignmentFactor = 1f;
        break;
      }

      for (int lineIdx = 0; lineIdx < lines.Length; lineIdx++)
      {
        string line = lines[lineIdx];
        float lineOffset = ((line.Length - 1) * letterOffset) * alignmentFactor;

        for (int charIdx = 0; charIdx < line.Length; charIdx++)
        {
          int idx1 = glyphIdx * 6 + 0;
          int idx2 = glyphIdx * 6 + 1;
          int idx3 = glyphIdx * 6 + 2;
          int idx4 = glyphIdx * 6 + 3;
          int idx5 = glyphIdx * 6 + 4;
          int idx6 = glyphIdx * 6 + 5;

          // Check for truncated text (doesn't generate verts for all characters)
          if (idx6 > verts.Count - 1) return;

          UIVertex vert1 = verts[idx1];
          UIVertex vert2 = verts[idx2];
          UIVertex vert3 = verts[idx3];
          UIVertex vert4 = verts[idx4];
          UIVertex vert5 = verts[idx5];
          UIVertex vert6 = verts[idx6];

          pos = Vector3.right * ((letterOffset * charIdx) - lineOffset);

          vert1.position += pos;
          vert2.position += pos;
          vert3.position += pos;
          vert4.position += pos;
          vert5.position += pos;
          vert6.position += pos;

          verts[idx1] = vert1;
          verts[idx2] = vert2;
          verts[idx3] = vert3;
          verts[idx4] = vert4;
          verts[idx5] = vert5;
          verts[idx6] = vert6;

          glyphIdx++;
        }

        // Offset for carriage return character that still generates verts
        glyphIdx++;
      }
    }
  }
}
```

## Button
### Enable/Disable 상태 전환

```csharp
using UnityEngine.UI;

Button button = GetComponent<Button>();
button.interactable = false; // set disable.
```

## Event System
### Tab으로 입력 컨트롤 이동

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.EventSystems;

public class UITabNavigator : MonoBehaviour {

  private EventSystem system;

  void Start() {
    system = EventSystem.current;
  }

  void Update() {
    if (Input.GetKeyDown(KeyCode.Tab)) {
      if (system.currentSelectedGameObject != null
        && system.currentSelectedGameObject.GetComponent<Selectable>() != null)
      {
        Selectable next = Input.GetKey(KeyCode.LeftShift) || Input.GetKey(KeyCode.RightShift) ?
          system.currentSelectedGameObject.GetComponent<Selectable>().FindSelectableOnUp() :
          system.currentSelectedGameObject.GetComponent<Selectable>().FindSelectableOnDown();

        if (next != null) {
          InputField inputfield = next.GetComponent<InputField>();
          if (inputfield != null)
          {
            inputfield.OnPointerClick(new PointerEventData(system));
          }

          system.SetSelectedGameObject(next.gameObject);
        }
        //Here is the navigating back part:
        else {
          next = Selectable.allSelectables[0];
          system.SetSelectedGameObject(next.gameObject);
        }

      }
    }
  }

}
```

## ScrollBar
### ListView

http://tsubakit1.hateblo.jp/entry/2014/12/18/040252

1. Canvas以下のパスにGameObjectを作ります（Create Empty Child等で）。=> ScrollView
1. ScrollViewの範囲を、スクロールビューを表示したい範囲に設定します。
1. ScrollViewにScrollRectとMaskとImageコンポーネントを追加します 
1. ScrollViewの子オブジェクトとして名前がContentのGameObjectを作ります。
1. ContentのRectTransformはのPivotを(x:0.5, y:1)とします。
   1. リストビューの開始位置を一番上にしています。スタート時を下にしたい場合は(x:0.5, y:0）
1. ContentにVerticalLayoutGroupとContentSizeFitterコンポーネントを追加し、ContentSizeFitterのVerticalFitをPreferred sizeに設定します。 
1. ScrollRectのContentにContentオブジェクトを指定します。 

Content에 추가할 Element 오브젝트에는 LayoutElement 컴포넌트를 추가해서, 오브젝트의 크기 정보를 입력.

スクリプトからノードを生成する
1. ContentオブジェクトにScrollControllerコンポーネントを追加します。コードは下
1. prefabに先ほど作ったNodeプレハブを指定します。

```csharp
using UnityEngine;
using System.Collections;
using UnityEngine.UI;

public class ScrollController : MonoBehaviour {
 
  [SerializeField]
  RectTransform prefab = null;
 
  void Start () 
  {
    for(int i=0; i<15; i++)
    {
      var item = GameObject.Instantiate(prefab) as RectTransform;
      item.SetParent(transform, false);
 
      var text = item.GetComponentInChildren<Text>();
      text.text = "item:" + i.ToString();
    }
  }
}
```

## InputField
### 일본어 입력

**Line Type**을 **Multi Line Newline**으로 한다.

일본어 입력시, 한자 변환때문에 Single Line으로 하면 입력된게 사라져 버린다. 단, 이러면 여러줄 입력이 가능해져 버리는데, 한줄 입력만 받고 싶다면 다음과 같이 한다.

OnValueChanged 이벤트 내용에 다음을 추가.

```csharp
public void OnInputChanged(string text) {
  inputField.text = text.Replace("\n", "");
}
```
