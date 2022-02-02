# Unity 게임 개발

## Camera

* 참고: http://docs.unity3d.com/Manual/class-Camera.html

### Camera Rendering Order
복수의 Camera를 사용하는 경우, 카메라 렌더링의 순서를 정해줄 필요가 있다. 이 경우 Camera 컴포넌트의 Depth 속성으로 조절한다. 3D좌표의 경우와는 다르게 Depth는 수치가 클 수록 더 화면 위에 그려지게 된다. (나중에 그려질 수록 결국은 더 위쪽에 있는 것처럼 보여지게 되므로)

## 물리 엔진(3D)

* 참고: http://unitykoreawiki.com/index.php?n=KrMain.Physics

### 충돌 영역(collider)
물체에서 충돌 판정이 일어나는 부분을 지정.

**Color의 Alpha값에 주의!**<br/>
Alpha가 0, 즉 완전투명해서 안보이게 된 상태에서는 colider가 지정되어 있어도 무시당한다.

### 충돌 검사

충돌하는 모든 개체에 **collider** 컴포넌트를 추가.

그리고 움직이는 물체에 **rigidbod**를 추가. rigidbody로 지정된 물체는 움직임이 있어야 충돌판정이 나온다.

※ collider는 충돌 영역 일뿐 그 자체만으로 충돌 이벤트가 발생 하지 않는다.

RigidBodyのIsKinematicをONにすると、他のオブジェクトには物理的影響を与えるが、 他のオブジェクトからは物理的影響をうけないオブジェクトになる。

RigidbodyのIsKinematicがTrue ColliderのIsTriggerがTrue じゃないとOnCollisionはコールされない。

IsKinematic이 켜져 있지 않으면 하나에 대해 OnTrigger 상태에서, 다른 오브젝트에 대한 OnTrigger이벤트가 발생되지 않음.

#### Trigger
트리커로 설정된 개체는 충돌되는 실체가 아니라 통과점으로 인식된다. 즉 순수하게 충돌 영역으로서만 인식된다. 따라서 충돌 후의 물리 계산도 자동으로 해주지 않는다.

#### Event

* OnCollisionEnter(Collision collision) : 충돌 지점에 진입할 때
* OnCollisionStay(Collision collision) : 충돌중...
* OnCollisionExit(Collision collision) : 충돌 지점에서 벗어날 때

* OnTriggerEnter(Collider other) : 트리거와 접촉 시작
* OnTriggerStay(Collider other) : 트리거와 접촉 중...
* OnTriggerExit(Collider other) : 트리거와 접촉 종료

### Jump 구현

```csharp
// http://www40.atwiki.jp/spellbound/pages/1605.html
using UnityEngine;
using System.Collections;
 
public class NewBehaviourScript : MonoBehaviour
{
  // ジャンプ中ならtrue
  public bool jump = false;
  // ジャンプする力
  public float force = 5.0f;
 
  private void FixedUpdate()
  {
    // ジャンプ中でないときにマウス左ボタンが押されたらジャンプする
    if (!jump && Input.GetMouseButtonDown(0))
    {
      jump = true;
      // オブジェクトの上方向に力を瞬間的に与える
      rigidbody.AddForce(transform.up * force, ForceMode.Impulse);
    }
  }
 
  private void OnCollisionEnter(Collision collision)
  {
    // 床オブジェクトと衝突したらジャンプ中ではないのでjump = falseにする
    if (collision.gameObject.name == "Floor")
    {
      jump = false;
    }
  }
}
```

### Pause

```csharp
Time.timeScale = 0;
```

### 중력 설정

```csharp
// http://www40.atwiki.jp/spellbound/pages/1376.html

Physics.gravity = Vector3.zero; // 無重力
Physics.gravity = Vector3.up; // 上に重力がかかる
```
gravityはVector3オブジェクトで、初期値は(0.0, -9.8, 0.0)になります。

### Raycast
시작점으로 부터 특정 방향으로 가상의 빔을 쏴서 부딛히는 객체(collider 영역이 있어야 함)를 찾는다.
```csharp
RaycastHit hit; // 찾은 오브젝트에 대한 정보가 반환되는 곳.
Ray ray = new Ray((Vector3)시작점, Vector3.right /* 방향 */);

Physics.Raycast(ray, out hit, (float)거리, (int)레이어_마스크);
// 거리를 무한으로 지정: Mathf.Infinity
// 레이어 마스크: ex) 8번 레이어만 조사 = 1 >> 8
```
Physics.Raycast()는 다양한 버전의 인자 형식이 있으니 여러가지로 시도해 볼것.

### 고속 충돌 문제
강한 중력등에 의해 빠르게 움직이는 물체는 충돌 판정이 의도한 대로 되지 않는 경우가 있다.

가령, 복합적인 Collider를 가진 이동 물체가 높은 곳에서 빠르게 지면에 착지하는 경우, 바닥에 박혀서 움직이지 못하는 케이스가 있을 수 있다. 이때 한가지 해결 방법은, 물제가 낙하가기 시작한 위치(y)와 바닥 충돌체의 위치(y)간의 차이를 구하고 이 차이가 어느 수준 이상이 되면 바닥에 착치판정이 있어난 시점에서 약한 점프 효과를 주는 것이다. 바닥에 착지한 순간에 살짝 통하고 튕기는 느낌으로.

### 중력 컴포넌트(자체 구현)
임의 위치에 중력 효과를 발생시키는 컴포넌트 구현.
```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class Gravity : MonoBehaviour {
  
  public float g_power = 50.0f; // 중력의 기본 당기는 힘.
  public float g_accl = 0.4f; // 중력의 가속양.
  public bool enable_gX = false; // x축 중력 활성화.
  public bool enable_gY = true; // y축 중력 활성화.
  
  public float g_distance = 0; // 중력이 영향을 미칠 수 있는 거리.
  public float g_core_distance = 20f; // 이 거리 안으로 들어오는 오브젝트트는 바로 중력 중심으로 끌어당긴다.
  
  public float limit_g_max_speed = 40f;
  
  // 중력으로 끌어당길 대상 정보.
  public class Target {
    GameObject obj;
    public bool activated_x = true; //x축 중력에 영향을 받는다.
    public bool activated_y = true; //y축 중력에 영향을 받는다.
    
    public float speed_y = 0.0f;
    public float speed_x = 0.0f;
    public float speed = 0.0f;
    public Vector3 prev_pos;
    
    public bool flag = false; // reserved
    
    public Target(GameObject obj, bool activated_x = true, bool activated_y = true) {
      this.obj = obj;
      this.activated_x = activated_x;
      this.activated_y = activated_y;
      prev_pos = GetPosition();
    }
    
    public bool isTop(Vector3 other, bool isPrevPos= false) {
      if (isPrevPos)
        return prev_pos.y > other.y;
      
      return obj.transform.position.y > other.y;
    }

    public bool isBottom(Vector3 other, bool isPrevPos= false) {
      if (isPrevPos)
        return prev_pos.y < other.y;
      
      return obj.transform.position.y < other.y;
    }
    
    public bool isLeft(Vector3 other, bool isPrevPos= false) {
      if (isPrevPos)
        return prev_pos.x < other.x;
      
      return obj.transform.position.x < other.x;  
    }
    
    public bool isRight(Vector3 other, bool isPrevPos= false) {
      if (isPrevPos)
        return prev_pos.x > other.x;
      
      return obj.transform.position.x > other.x;  
    }
    
    public bool isForwardTo_Y(Vector3 other) {
      return isTop(other) && speed_y <= 0 || isBottom(other) && speed_y >=0;
    }
    
    public void Activate() { 
      activated_x = activated_y = true; 
    }
    
    public void Activate_X() { 
      activated_x = true; 
    }
    
    public void Activate_Y() { 
      activated_y = true; 
    }
    
    public void Deactivate() { 
      activated_x = activated_y = false; 
    }
    
    public void Deactivate_X() { 
      activated_x = false; 
    }
    
    public void Deactivate_Y() { 
      activated_y = false; 
    }
    
    public bool isActivated() { 
      return activated_x || activated_y;
    }
    
    public bool isActivated_X() { 
      return activated_x;
    }
    
    public bool isActivated_Y() { 
      return activated_y;
    }
    
    public GameObject GetObject() { return obj; }
    public Vector3 GetPosition() { return obj.transform.position; }
    public void Translate(float x, float y, float z) {
      prev_pos = GetPosition();
      obj.transform.Translate(x, y, z);
    }
    
    public void MoveTo(Vector3 to) {
      obj.transform.position = to;
    }
    
    public void MoveTo(float x, float y, float z) {
      obj.transform.position = new Vector3(x, y, z);
    }
    
  }
  
  List<Target> targets;
  
  // Use this for initialization
  void Awake () {
    targets = new List<Target>();
  }
  
  // Update is called once per frame
  void FixedUpdate () {
    if (targets == null)
      return;

    for (int i = 0; i < targets.Count; ++i) {
      Target t = targets[i];
      
      if (t.GetObject() == null) {
        targets.Remove(t);
      }
      else {
        if (t.isActivated()) EffectG(t);
      }
    }
  }
  
  public float getG() {
    return g_accl;
  }
  
  public Target AddTarget(GameObject obj, bool activated_x = true, bool activated_y = true) {
    Target tar = new Target(obj, activated_x, activated_y);
    
    foreach (Target t in targets) {
      if (t.GetObject() == obj) {
        t.activated_x = activated_x;
        t.activated_y = activated_y;
        return t;
      }
    }
    
    targets.Add(tar);
    return tar;
  }
  
  public void ClearAllTargets() {
    targets.Clear();
  }
  
  public void RemoveTarget(GameObject obj) {
    targets.Remove( GetTarget(obj) );
  }
      
  public void RemoveTarget(Target t) {
    targets.Remove(t);
  }
  
  public Target GetTarget(GameObject obj) {
    foreach (Target t in targets) {
      if (t.GetObject() == obj)
        return t;
    }
    
    return null;
  }
  
  public List<Target> GetTargets() {
    return targets;
  }
  
  public void EffectG_XY(Target target) {
    Vector3 toO = transform.position;
    Vector3 fromO = target.GetPosition();
    
    float dist = Vector3.Distance(fromO, toO);
    
    if (dist <= g_core_distance) {
      target.GetObject().transform.position = toO;
      target.speed = 0;
      return;
    }
    
    if (g_distance > 0 && dist > g_distance)
      return;
    
    // m = ( y1 - y2 ) / ( x1 - x2 )
    float m = (fromO.y - toO.y) / (fromO.x - toO.x);
    
    // T = distance
    target.speed += g_accl;
    
    if (target.speed >= dist) {
      target.speed -= g_power;
      
      if (target.speed <= 0) {
        target.GetObject().transform.position = toO;
        target.speed = 0;
        return;
      }
    }
    
    if (target.speed > limit_g_max_speed) 
      target.speed = limit_g_max_speed;
    
    
    //dx = T / sqrt(1 + M*M)
    //dy = T * M / sqrt(1 + M*M)
    float sm = Mathf.Sqrt(1.0f + m*m);
    float dx = target.speed / sm;
    float dy = target.speed*m / sm;

    //Q1 = (PX + dx, PY + dy)
    //Q2 = (PX - dx, PY - dy)

    try {
      if (target.isRight(transform.position)) {
        target.GetObject().transform.Translate(-dx, -dy, 0);
      }
      else {
        target.GetObject().transform.Translate(dx, dy, 0);
      }
    }
    catch (System.Exception e) {
      RemoveTarget(target);
    }
  }
  
  public void EffectG(Target target) {
    if (enable_gY && enable_gX && target.activated_x && target.activated_y) {
      EffectG_XY(target);
      return;
    }
    
    Vector3 target_pos = target.GetPosition();
    
    bool changed = false;
    
    if (enable_gY && target.isActivated_Y()) {
      float dist_y = Mathf.Abs(transform.position.y - target_pos.y);
      
      if (Mathf.Abs(target.speed_y) <= g_power && dist_y <= g_power) {
        target.MoveTo(target_pos.x, transform.position.y, target_pos.z);
        target.Deactivate_Y();
        
        if (!enable_gX || !target.isActivated_X()) 
          return;
      }
      else {
        if (target.isBottom(transform.position)) {
          if (target.isTop(transform.position, true)) {
            target.speed_y += g_power;
          }
          else
            target.speed_y += g_accl;
        }
        else if (target.isTop(transform.position)) {
          if (target.isBottom(transform.position, true)) {
            target.speed_y -= g_power;
          }
          else
            target.speed_y -= g_accl;       
        }
      }
      
      changed = true;
    }
    
    if (enable_gX && target.isActivated_X()) {
      float dist_x = Mathf.Abs(transform.position.x - target_pos.x);
      
      if (Mathf.Abs(target.speed_x) <= g_power && dist_x <= g_power) {
        target.MoveTo(transform.position.x, target_pos.y , target_pos.z);
        target.Deactivate_X();
        
        if (!enable_gY || !target.isActivated_Y()) 
          return;
      }
      else {
        if (target.isLeft(transform.position)) {
          if (target.isRight(transform.position, true)) {
            target.speed_x += g_power;
          }
          else
            target.speed_x += g_accl;
        }
        else if (target.isRight(transform.position)) {
          if (target.isLeft(transform.position, true)) {
            target.speed_x -= g_power;
          }
          else
            target.speed_x -= g_accl;       
        }
      }
      
      changed = true;
    }
    
    if (target.speed_y > limit_g_max_speed) 
      target.speed_y = limit_g_max_speed;
    
    if (target.speed_x > limit_g_max_speed) 
      target.speed_x = limit_g_max_speed;
    
    if (changed)
      target.Translate(target.speed_x, target.speed_y, 0);
  }
    
  void OnDestory() {
    targets.Clear();
  }

}
```

## Texture / Sprite

런타임에서 텍스쳐 로딩
```csharp
Texture LoadTexture(string name) {
  return Resources.Load("path/" + name) as Texture;
}

gameObject.renderer.material.mainTexture = LoadTexture("...");
```

GUI Texture 변경
```csharp
gameObject.guiTexture.texture = LoadTexture("...");
```

### ファイルパスからTexture2D読み込み

```csharp
public Texture2D Texture2DFromFile(string path)
{
    Texture2D texture = null;
    if (File.Exists(path))
    {
        //byte取得
        FileStream fileStream = new FileStream(path, FileMode.Open, FileAccess.Read);
        BinaryReader bin = new BinaryReader(fileStream);
        byte[] readBinary = bin.ReadBytes((int)bin.BaseStream.Length);
        bin.Close();
        fileStream.Dispose();
        fileStream = null;
        if (readBinary != null)
        {
            //横サイズ
            int pos = 16;
            int width = 0;
            for (int i = 0; i < 4; i++)
            {
                width = width * 256 + readBinary[pos++];
            }
            //縦サイズ
            int height = 0;
            for (int i = 0; i < 4; i++)
            {
                height = height * 256 + readBinary[pos++];
            }
            //byteからTexture2D作成
            texture = new Texture2D(width, height);
            texture.LoadImage(readBinary);
        }
        readBinary = null;
    }
    return texture;
}
```

#### ファイルパスからSprite読み込み

```csharp
public Sprite SpriteFromTexture2D(Texture2D texture)
{
    Sprite sprite = null;
    if (texture)
    {
        //Texture2DからSprite作成
        sprite = Sprite.Create(texture, new UnityEngine.Rect(0, 0, texture.width, texture.height), Vector2.zero);
    }
    return sprite;
}

public Sprite SpriteFromFile(string path)
{
    Sprite sprite = null;
    Texture2D texture = Texture2DFromFile(path);
    if (texture)
    {
        //Texture2DからSprite作成
        sprite = SpriteFromTexture2D(texture);
    }
    texture = null;
    return sprite;
}
```

## Shader

### 2D Sprite

```csharp
// Custom sprite shader - no lighting, on/off alpha

Shader "Simple Sprite 2D" {
Properties {
    _MainTex ("Base (RGB) Trans (A)", 2D) = "white" {}
}

SubShader {
    Tags {"Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Transparent"}
//    LOD 100

    ZWrite Off
    Blend SrcAlpha OneMinusSrcAlpha 
    Lighting Off

    Pass {
        SetTexture [_MainTex] { combine texture } 
    }
}
}
```

## Particle Effect

### Particle 재생 후, 자동 제거

```csharp
public class AutoDestroyParticle : MonoBehaviour {

  public float additionalDestructionDelay = 0;

  // Use this for initialization
  void Start () {
    if (!particleSystem.loop) {
      Destroy(this.gameObject, particleSystem.duration + additionalDestructionDelay);
    }
  }
}
```

## Drawing
### Circle
LineRenderer를 이용한 원 그리기
```csharp
using UnityEngine;
using System.Collections;

public class CircleDrawer : MonoBehaviour {

  float radius = 0f;
  float theta_scale = 0.01f; //Set lower to add more points
  int size; //Total number of points in circle

  LineRenderer lineRenderer;

  void Awake() {
    float sizeValue = (2.0f * Mathf.PI) / theta_scale; 
    size = (int)sizeValue;
    size++;
    lineRenderer = GetComponent<LineRenderer>();
    //lineRenderer.material = new Material(Shader.Find("Particles/Additive"));
    lineRenderer.SetWidth(0.04f, 0.04f); //thickness of line
    lineRenderer.SetVertexCount(size);    
    lineRenderer.SetColors(Color.blue, Color.blue);
  }

  // LeanTween 이용.
  public void startTween(float time, System.Action onTweenComplete) {
    LeanTween.value(gameObject, 0, 5, time)
      .setLoopClamp()
      .setLoopCount(6)
      .setOnUpdate((value)=> radius = value)
      .setOnComplete(onTweenComplete);
  }

  void Update() {
    Vector3 pos;
    float theta = 0f;
    for(int i = 0; i < size; i++){          
      theta += (2.0f * Mathf.PI * theta_scale);         
      float x = radius * Mathf.Cos(theta);
      float y = radius * Mathf.Sin(theta);          
      x += gameObject.transform.position.x;
      y += gameObject.transform.position.y;
      pos = new Vector3(x, y, 0);
      lineRenderer.SetPosition(i, pos);
    }
  }

}
```
